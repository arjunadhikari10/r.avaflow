r.avaflow Simulation Workflow – Gyapche Landslide
Software Environment

Operating System: Windows + WSL (Linux)

GIS Platform: GRASS GIS 7.8.2

Model: r.avaflow 4.0G (Revision 7)

Environment: Linux / WSL

0. Start WSL Environment

Open WSL from Windows Command Prompt:

C:\Users\Admin>wsl

1. Navigate to Project Directory

Move to the Linux home directory:

cd ~


Navigate to the working data directory created inside WSL:

cd data


Check directory structure:

ls


Expected output:

PROJECTS  SOFTWARE  SPATIALDATA


Navigate to the project folder:

cd PROJECTS
cd Gyapche


Final working directory:

arjun@CDS:~/data/PROJECTS/Gyapche$

2. Start GRASS GIS

Launch GRASS GIS:

grass78


Create a new location (UTM Zone 45N) if required

Or select an existing location created previously

3. Import Input Data
Import DEM (Original 0.25 m Resolution)
r.in.gdal -o input=TIFF/gy_elev.tif output=gy_elev_0p25

Import Release Thickness Map
r.in.gdal -o input=TIFF/gy_hrelease.tif output=gy_hrelease_0p25

4. Set Computational Region to Original DEM
g.region raster=gy_elev_0p25
g.region -p


Result:

Resolution ≈ 0.25 m

Total cells ≈ 18.8 million

⚠️ Warning:
This resolution is computationally too fine for stable and efficient r.avaflow simulations on standard systems (especially under WSL).

5. Resample Data to 5 m Resolution (Recommended)
Set Target Resolution
g.region raster=gy_elev_0p25 res=5

Resample DEM (Average)
r.resamp.stats \
  input=gy_elev_0p25 \
  output=gy_elev_5 \
  method=average

Resample Release Thickness (Average)
r.resamp.stats \
  input=gy_hrelease_0p25 \
  output=gy_hrelease_5 \
  method=average

Why Average Resampling?

Elevation: Represents mean surface height

Release thickness (hrelease): Represents thickness (m), not volume

Preserves physical meaning and numerical stability at coarser resolution

6. Set Computational Region to 5 m DEM
g.region raster=gy_elev_5
g.region -p


Result:

Resolution ≈ 5 m

Total cells ≈ ~47,000

Numerically stable and computationally efficient

7. Run r.avaflow Simulation (Single Run)
Solid-phase, Single-phase Simulation
r.avaflow.40G \
  elevation=gy_elev_5 \
  hrelease=gy_hrelease_5 \
  impactarea=gy_hrelease_5 \
  phases=s

8. Model Execution Output (Example)
Starting model execution.

nout nsum cfl tlength tsum dmax vmax volume ekin
R   ----- ----- ----- ----- *55.54 ----- *4030.6 -----
0   0     ----- ----- 0.0   55.54  0.00  4030.6 -----
1   5     0.031 0.0   0.0   ...

Interpretation

Maximum release thickness ≈ 55 m

Total volume computed internally by r.avaflow
