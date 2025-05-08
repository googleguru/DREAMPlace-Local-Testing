# ✅ DREAMPlace: Local GPU-Accelerated Setup and Execution on Windows (with Docker)

This guide documents how I successfully tested the DREAMPlace VLSI placement tool locally on a Windows machine using Docker with GPU acceleration, including generating visual placement progress across multiple iterations.

---

## 🧰 System Requirements

- **Operating System:** Windows 10/11  
- **GPU:** NVIDIA GPU with Compute Capability ≥ 6.0  
- **Software:**
  - [Docker Desktop](https://www.docker.com/products/docker-desktop) (WSL 2 backend enabled)
  - [NVIDIA GPU Driver](https://www.nvidia.com/Download/index.aspx)
  - [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)
  - [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#windows)
  - [Git for Windows](https://git-scm.com/)

---

## ⚙️ Setup and Execution Steps

### 1. Clone DREAMPlace with Submodules

```bash
git clone --recursive https://github.com/limbo018/DREAMPlace.git
cd DREAMPlace
```

If cloned without `--recursive`, use:

```bash
git submodule init
git submodule update
```

---

### 2. Pull Docker Image (with GPU Support)

```bash
docker pull limbo018/dreamplace:cuda
```

---

### 3. Run Docker with GPU Access

Replace `C:\path\to\DREAMPlace` with your local path:

```bash
docker run --gpus all -it -v C:\path\to\DREAMPlace:/DREAMPlace limbo018/dreamplace:cuda bash
```

---

### 4. Build DREAMPlace in the Container

```bash
cd /DREAMPlace
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=../install -DPython_EXECUTABLE=$(which python)
make -j$(nproc)
make install
```

---

### 5. Install Python and Cairo Dependencies

```bash
pip install -r /DREAMPlace/requirements.txt
apt-get update && apt-get install -y libcairo2-dev
```

---

### 6. Download Benchmarks

```bash
cd /DREAMPlace
python benchmarks/ispd2005_2015.py
```

---

## 🚀 Run a Benchmark with Visualization

### 1. Edit JSON Configuration

Open and edit `test/ispd2005/adaptec1.json` to enable plotting:

```json
"plot_flag": 1
```

### 2. Execute DREAMPlace

```bash
cd /DREAMPlace/install
python dreamplace/Placer.py test/ispd2005/adaptec1.json
```

---

## 📊 Output and Visualization

After execution, navigate to:

```
C:\path\to\DREAMPlace\results\adaptec1
```

### Output Images

Eight images were generated, representing placement **at different iterations** of the global placement process — essentially a **time-lapse** of the algorithm's progress:

- `placement_0000.png`
- `placement_0001.png`
- `placement_0002.png`
- `placement_0003.png`
- `placement_0004.png`
- `placement_0005.png`
- `placement_0006.png`
- `placement_0007.png`

You can view these individually or combine them into a GIF using tools like:

```bash
convert -delay 100 -loop 0 placement_*.png placement.gif
```

Or use free tools like [EZGIF](https://ezgif.com/maker) online.

---

## ✅ Summary

- Successfully cloned, built, and ran DREAMPlace with GPU support using Docker on Windows.
- Benchmarks executed successfully.
- Generated 8 visual snapshots showing placement improvement over time.
- Optional: compile snapshots into an animation for better visualization.
