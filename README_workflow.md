# 🏔️ r.avaflow Simulation Workflow: Gyapche Landslide

This guide outlines the systematic process for conducting landslide simulations using the **r.avaflow 4.0G** model within a **WSL/GRASS GIS** environment.

---

## 💻 Software Environment
* **Operating System:** Windows 11 + WSL2 (Linux)
* **GIS Platform:** `GRASS GIS 7.8.2`
* **Model:** `r.avaflow 4.0G (Revision 7)`
* **User:** `arjun@CDS`

---

## 🟢 0. Start WSL Environment
Open your Windows Command Prompt or PowerShell and initialize the Linux subsystem:

```bash
C:\Users\Admin> wsl
```

## 📂 1. Navigate to Project Directory
It is highly recommended to work within the Linux native filesystem (~) for significantly faster I/O performance.

#### Move to Linux home
```bash
cd ~
```
#### Enter your data repository
```bash
cd data
```
#### Verify directory structure
```bash
ls
# Expected: PROJECTS | SOFTWARE | SPATIALDATA
```

#### Enter the specific landslide project
```bash
cd PROJECTS/Gyapche
```
Final Path: arjun@CDS:~/data/PROJECTS/Gyapche$

#### 🌿 2. Start GRASS GIS
Launch the GRASS environment. Ensure your Location is set to UTM Zone 45N.
```bash
grass78
```
#### 📥 3. Import Input Data
Import the high-resolution GeoTIFF files into the GRASS database format.
```bash
# 1. Import Original DEM (0.25 m)
r.in.gdal -o input=TIFF/gy_elev.tif output=gy_elev0p25
```
```bash
# 2. Import Release Thickness Map(2.5m)
r.in.gdal -o input=TIFF/gy_hrelease.tif output=gy_hrelease0p25
```

```bash
# 2. Import Release Thickness Map (4.5m)
r.in.gdal -o input=TIFF/impactarea.tif output=impactarea4p5m
```
#### 📏 4. Set Computational Region (Native)
Check the region settings for the original high-resolution data.

```bash

g.region -s rast=impactarea4p5m
```
[!CAUTION] Warning: Current resolution of elevation and hrelease raster map is 0.25 m. This is computationally too heavy for stable r.avaflow simulations on standard hardware. Downsampling is required.

#### 🔄 5. Resample Data to 5 m (Recommended)
We use a 5 m resolution to achieve a balance between physical detail and numerical stability.

Step A: Set Target Resolution
```bash

g.region -s rast=impactarea res=5 -a
```
Step B: Execute Resampling
We use the average method to preserve the physical integrity of the terrain and thickness.

```bash

# Resample Elevation
r.resamp.stats input=gy_elev0p25m output=gy_elev method=average
```

```bash
# Check resolution
r.info map=gy_elev
```

```bash
# Resample Release Thickness
r.resamp.stats input=gy_hrelease0p25m output=gy_hrelease method=average
```
```bash
# Resample impact area Thickness
r.resamp.stats input=impactarea4p5m output=impactarea method=average
```
#### 🎯 6. Set Final Computational Region
Finalize the environment settings before running the model.

```bash
g.region -s rast=gy_elev
```
```bash
g.region -p
```
look for units,resolution, no of cells
Status: 🟢 Optimized for simulation.

#### 🚀 7. Run r.avaflow Simulation
Execute a Solid-phase (1), single-phase landslide simulation.

```bash
r.avaflow.40G elevation=gy_elev_5 hrelease=gy_hrelease_5  impactarea=gy_hrelease_5 phases=1
```

#### 📈 8. Model Execution Output

After running the command, the model initializes the parameters. Pay close attention to the **R (Release)** row:

```bash
Starting model execution...
nout nsum cfl tlength tsum dmax vmax volume ekin
R    ---  --- ---     ---  55.54 ---  4030.6 ---
0    0    --- ---     0.0  55.54 0.00 4030.6 ---

```
## Citation

If you use r.avaflow in academic work, please cite:

Mergili et al. (2017–2024), r.avaflow: mass flow simulation framework
https://www.avaflow.org/

Workflow documented by:
Arjun Adhikari
Civil Engineering Student, Nepal
