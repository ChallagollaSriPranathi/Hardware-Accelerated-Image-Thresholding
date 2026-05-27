# Hardware-Accelerated-Image-Thresholding on PYNQ-Z2 FPGA Board

> Hardware-accelerated image thresholding using the PYNQ-Z2 FPGA platform — converting color images to grayscale via a custom AXI-based design in Xilinx Vivado and deploying it through a Jupyter Notebook on the PYNQ framework.

---

## 📌 Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Hardware & Software Requirements](#hardware--software-requirements)
- [Workflow / Step-by-Step Procedure](#workflow--step-by-step-procedure)
- [Results / Output](#results--output)
- [Applications](#applications)
- [Challenges Faced](#challenges-faced)
- [Future Improvements](#future-improvements)

---

## 🔍 Overview

This project demonstrates real-time image thresholding on an FPGA using the **PYNQ-Z2 development board**. A custom hardware design was built in **Xilinx Vivado** using an AXI-based block design that integrates the Zynq Processing System (PS) with programmable logic (PL). The hardware bitstream was deployed onto the PYNQ board and controlled via a **Python/Jupyter Notebook** interface over a browser. The final result is the conversion of a color image to a grayscale image using hardware-accelerated logic.

> https://github.com/ChallagollaSriPranathi/Hardware-Accelerated-Image-Thresholding/blob/main/BlockDiagram.jpeg

---

## 🎯 Objectives

- Understand the PYNQ-Z2 hardware platform and the PS-PL architecture of Zynq SoCs
- Design a block diagram in Xilinx Vivado with AXI interconnect and streaming interfaces
- Write Verilog RTL code for thresholding logic and integrate it into the block design
- Generate a bitstream and export hardware to the PYNQ board
- Use Python in Jupyter Notebook to load the overlay and process images
- Convert a color image to a grayscale image using FPGA-accelerated hardware

---

## 🛠️ Hardware & Software Requirements

### Hardware
| Component | Details |
|-----------|---------|
| FPGA Board | PYNQ-Z2 (Zynq-7000 SoC) |
| Host PC | For Vivado design and bitstream generation |
| Ethernet / USB | To connect to PYNQ board |

### Software
| Tool | Purpose |
|------|---------|
| Xilinx Vivado (2019.x or later) | Block design, synthesis, implementation, bitstream generation |
| PYNQ Framework (v2.x) | Python overlay API on the board |
| Jupyter Notebook | Python code execution via browser |
| Python 3 | Scripting and image processing |
| OpenCV / PIL (optional) | Image loading and display |
| Google Chrome / any browser | Access PYNQ Jupyter at `http://pynq:9090` |

---

## ⚙️ Workflow / Step-by-Step Procedure

### Phase 1: Vivado Block Design

#### Step 1 — Create a New Project
1. Open Xilinx Vivado
2. Select **Create Project** → Name it `thresholding`
3. Choose **RTL Project**

#### Step 2 — Select the PYNQ-Z2 Board
1. Go to **Parts / Boards** tab
2. Search for `Pynq` → select **Pynq-Z2**
3. If the board is not listed, click **Refresh** to update the board repository

#### Step 3 — Create Block Diagram Design
1. In the Flow Navigator, click **Create Block Design**
2. Name the design (e.g., `design_1`)

#### Step 4 — Add Design Sources (Verilog)
1. Click **Add Sources** → **Add or Create Design Sources**
2. Create a new Verilog file for the thresholding logic
3. A Verilog file will open — type your thresholding code and save it

#### Step 5 — Add IP Components to Block Design
Add the following **4 components** via the **Add IP** (`+`) button:

| # | Component | Purpose |
|---|-----------|---------|
| 1 | Zynq7 Processing System | PS (ARM processor core) |
| 2 | AXI Interconnect | Connects PS to PL IP cores |
| 3 | AXI Direct Memory Access (AXI DMA) | Handles memory access for streaming data |
| 4 | Processor System Reset | Provides synchronized reset signals |

#### Step 6 — Run Block Automation
- Click **Run Block Automation** → Click **OK**
- Click **Run Connection Automation** → Select All → Click **OK**

#### Step 7 — Add Custom Thresholding Module
1. In **Design Sources**, right-click your Verilog file
2. Select **"Add Module to Block Design"**
3. The thresholding module appears in the block diagram alongside the 4 IP components

#### Step 8 — Add AXI Streaming Components
Add the following additional IPs:
- **AXI4-Stream Data FIFO** — for buffering stream data
- **AXI SmartConnect** — for optimized AXI connections

#### Step 9 — Configure PS Ports for ZYNQ
1. Double-click the **Zynq Processing System** block
2. Under **PS-PL Configuration**, enable:
   - **HP Slave AXI Interface**
   - **S AXI HPO** ✅
3. Click **OK**

#### Step 10 — Give All Connections & Validate Design
1. Manually connect all remaining ports as required
2. Click **Validate Design** ✅
3. Confirm: *"Validation successful"*

#### Step 11 — Create HDL Wrapper
1. After validation, a `design_1.bd` file is created
2. In Sources, right-click the block design → **Create HDL Wrapper**
3. Let Vivado manage the wrapper automatically

#### Step 12 — Run Synthesis, Implementation & Generate Bitstream
```
Flow Navigator:
  → Run Synthesis
  → Run Implementation
  → Generate Bitstream
```
> ⚠️ This bitstream is the PL-side logic that needs to be interfaced into the PS.

#### Step 13 — Export Hardware
```
File → Export → Export Hardware → ☑ Include Bitstream → OK
```

---

### Phase 2: Deploy on PYNQ Board

#### Step 14 — Collect Output Files
After opening the exported hardware (thresholding) folder, you will find many generated files. Focus on:
- `*.gen` file

Create a **new folder** inside the main project folder and paste the hardware (`.hw`) file into it.

In the new folder, paste **2 required files**:

| File Path | Description |
|-----------|-------------|
| `thresholding-runs > impl_1 > design_1_wrapper.bit` | Bitstream file |
| `thresholding-gen > sources_1 > bd > design_1 > hw_handoff > design_1.hwh` | Hardware handoff file |

> ⚠️ **Important:** Both files must be **renamed to the same base name**, e.g.:
> - `design_1.bit`
> - `design_1.hwh`

Copy and paste both files into the new folder.

#### Step 15 — Access PYNQ Board via Browser
1. Connect PYNQ-Z2 board to your PC via Ethernet
2. Open **Google Chrome**
3. Navigate to: `http://pynq:9090` (or `http://<board-ip>:9090`)
4. Enter password: **`xilinx`**

#### Step 16 — Upload Files to PYNQ Jupyter
Upload the following files to Jupyter:
- `design_1.bit` — Bitstream
- `design_1.hwh` — Hardware handoff
- Input image file (color image, e.g., `.jpg` / `.png`)

#### Step 17 — Open Notebook & Run Python Code
1. Open a new **Jupyter Notebook**
2. Copy your Python thresholding code into the notebook
3. Save the notebook
4. Run all cells to execute the design

---

## 📊 Results / Output

| Input | Output |
|-------|--------|
| Color image (RGB, `.jpg`) | Grayscale image |
| Processed via FPGA hardware | Displayed in Jupyter Notebook |

><img width="1080" height="1080" alt="image" src="https://github.com/user-attachments/assets/e55d9572-405a-44c8-ba0a-196d4fdcee30" />


**Key observations:**
- The color image was successfully converted to grayscale
- Hardware-accelerated processing via the PYNQ-Z2 PL fabric
- Output rendered correctly in Jupyter Notebook

---

## 🚀 Applications

- **Medical Imaging** — preprocessing scans before analysis
- **Computer Vision pipelines** — grayscale is a standard first step in edge detection, face recognition, etc.
- **Embedded surveillance systems** — low-power FPGA-based image filtering
- **Real-time video processing** — hardware thresholding for live camera feeds
- **Industrial inspection systems** — defect detection using binary or grayscale thresholding
- **Autonomous vehicles** — fast lane/obstacle detection preprocessing

---

## ⚠️ Challenges Faced

- **Board not listed in Vivado** — Required refreshing the board repository; the Pynq-Z2 board files needed to be manually installed
- **AXI connection complexity** — Managing multiple AXI interconnect types (AXI4, AXI4-Lite, AXI4-Stream) requires careful port matching
- **File naming mismatch** — The `.bit` and `.hwh` files must have identical base names; a mismatch prevents the overlay from loading
- **Browser access to PYNQ** — IP address configuration and network setup on first use can be confusing
- **Bitstream generation time** — Synthesis and implementation can take significant time depending on design complexity

---

## 🔮 Future Improvements

- Extend to **full binary thresholding** with adjustable threshold values via Python sliders in Jupyter
- Implement **real-time camera feed** processing using HDMI input/output on PYNQ-Z2
- Add **Otsu's thresholding** algorithm in hardware for adaptive threshold selection
- Explore **multi-level thresholding** (not just grayscale but segmentation)
- Port design to **PYNQ-Z1** or **Ultra96** for scalability comparison
- Package as a **reusable PYNQ overlay** for the community

---


## 🙏 Acknowledgements

This project was completed as part of a hands-on workshop on FPGA-based image processing using the PYNQ-Z2 platform.

