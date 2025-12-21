# r.avaflow 4.0G Installation Guide for Windows 11 using WSL2 + Ubuntu 20.04 + GRASS GIS 7.8

## Overview

**r.avaflow** is a multi-purpose landslide simulation framework that runs on **Linux**. This guide explains how to install it on **Windows 11** using **WSL2** and **Ubuntu 20.04**, along with its dependencies (GRASS GIS, Python 3, R).

### Components
- **Windows 11** (host OS)
- **WSL2** (Linux on Windows)
- **Ubuntu 20.04 LTS** (required OS for r.avaflow)
- **GRASS GIS 7.8** (core GIS engine)
- **Python 3 + Pillow** (required by r.avaflow)
- **R + required packages** (stats, codetools, Rcpp, terra, lattice, sp, raster, ROCR)
- **r.avaflow 4.0G** (landslide simulation)

> Visualization (ParaView, Blender) can be done on Windows.

---

## Step 1: Enable WSL2

1. Open **PowerShell as Administrator**.
2. Run:

```powershell
wsl --install
```

3. Restart Windows when prompted.
4. Ubuntu is installed automatically.

---

## Step 2: Open Ubuntu (WSL)

1. Open **Start Menu → Ubuntu**.
2. Set username: `arjun` (or your choice).
3. Set password. You are now in Linux terminal.

---

## Step 3: Create Folder Structure

```bash
mkdir -p ~/data/Projects
mkdir -p ~/data/SOFTWARE
mkdir -p ~/data/spatialdata
```

- Place r.avaflow source code in:

```text
/home/arjun/data/SOFTWARE/r.avaflow.40G
```

---

## Step 4: Install System Dependencies

```bash
sudo apt update

sudo apt install -y \
build-essential \
git \
grass \
grass-dev \
grass-doc \
python3 \
python3-pip \
r-base \
r-base-dev \
gfortran \
libssl-dev \
libcurl4-gnutls-dev \
libxml2-dev
```

- `grass`, `grass-dev` → GRASS GIS core and development headers
- `python3` → scripting support
- `r-base` → R language
- `build-essential` → compiler
- libraries → required by R packages

---

## Step 5: Install Python Dependency

```bash
pip3 install pillow
```

---

## Step 6: Install R Packages

Start R:

```bash
R
```

Install required packages:

```r
install.packages(c(
  "codetools",
  "Rcpp",
  "terra",
  "lattice",
  "sp",
  "raster",
  "ROCR"
))
```

> Ignore warnings about `stats` — it is a base package.

Exit R:

```r
q()
```

Save workspace if prompted.

---

## Step 7: Create GRASS GIS Database

1. Create `grassdata` folder:

```bash
mkdir ~/grassdata
```

2. Start GRASS for the first time:

```bash
grass78
```

- Create a **new location**: `wsl_test`
- Choose a valid CRS (e.g., UTM / WGS84)
- Mapset: `PERMANENT`

> This creates `/home/arjun/grassdata/wsl_test/PERMANENT`

---

## Step 8: Start GRASS Correctly

```bash
grass ~/grassdata/wsl_test/PERMANENT
```

You will see:

```
GRASS 7.8.2 (wsl_test):~ >
```

> Ignore GUI warnings (WSL limitation).

---

## Step 9: Install r.avaflow Addon

Inside GRASS terminal:

```bash
g.extension extension=r.avaflow.40G \
url=/home/arjun/data/SOFTWARE/r.avaflow.40G \
--verbose
```

Success message:

```
Installation of <r.avaflow.40G> successfully finished
```

---

## Step 10: Verify Installation

Inside GRASS:

```bash
r.avaflow.40G --help
```

> Do not use `r.avaflow --help` — only the versioned module works.

---

## Step 11: Starting r.avaflow in Future

```bash
wsl
grass ~/grassdata/wsl_test/PERMANENT
r.avaflow.40G
```

---


