
# 🚀 OpenROAD Flow: From RTL to Placement

This repository documents a **complete hands-on flow using OpenROAD** — taking a Verilog RTL design (`gcd`) all the way through **synthesis, floorplanning, and placement** using the **Sky130 HD** PDK.
It includes environment setup, troubleshooting steps, and visualization of the resulting layout using both **OpenROAD GUI** and **KLayout**.

-----

## 🧠 Why This Flow is Important in VLSI Journey

In **VLSI (Very Large Scale Integration) design**, we transform abstract hardware concepts written in a **Hardware Description Language (HDL)** like Verilog into a **physical chip layout** that can be fabricated on silicon. This transformation is called the **RTL-to-GDSII flow**.

The backend physical design steps—**synthesis, floorplanning, placement, clock tree synthesis, and routing**—directly impact the **chip’s performance, power, and area**. By mastering these steps, designers can:

1.  **Bridge Logic and Layout:** Turn logic gates and flip-flops into a manufacturable chip structure.
2.  **Optimize Performance:** Placement and floorplanning affect critical path delays and timing.
3.  **Ensure Power Efficiency:** Proper planning of power rails and cell placement reduces IR drop and leakage.
4.  **Reduce Area:** Efficient utilization of silicon reduces cost and allows more functionality per chip.

-----

## 🏗️ OpenROAD RTL-to-Placement Flowchart

```mermaid
graph TD
    subgraph "Phase 1: Inputs"
        A["Gate-Level Netlist (from Yosys Synthesis)"];
        B["Sky130 Physical Libraries (.lef, .lib files)"];
        C["Design Configuration File (config.mk)"];
    end

    subgraph "Phase 2: Physical Design using OpenROAD"
        D{"OpenROAD Flow Engine"};
        D --> F["Floorplanning: Define Core Area, IOs, Power Grid"];
        F --> G["Placement: Place Standard Cells in Rows"];
        G --> H["Output Layout Database (.def & .odb files)"];
    end
    
    subgraph "Phase 3: Visualization & Analysis"
        I{"Visualization Tools"};
        J["OpenROAD GUI: Interactive Layout Viewer"];
        K["KLayout: Alternative DEF Viewer"];
        L["Visual Verification: Floorplan & Placement"];
        I --> J;
        I --> K;
        J --> L;
        K --> L;
    end
    
    A --> D
    B --> D
    C --> D
    
    H --> I
    B --> I
```

### 🧭 Diagram Explanation

| **Phase** | **Purpose** | **Key Outputs** |
| :--- | :--- | :--- |
| 🟩 Phase 1: Inputs | Collects design and PDK resources. | Verilog Netlist, Sky130 LEF/LIB files, `config.mk` |
| 🟦 Phase 2: Physical Design | OpenROAD flow executes synthesis, floorplan, and placement. | `2_floorplan.def`, `3_place.def`, `.odb` |
| 🟧 Phase 3: Visualization | Layouts are loaded in OpenROAD GUI / KLayout for analysis. | Verified Layout View |

-----

### 💡 Simplified View of Where This Task Fits

| Stage | What Happens |
| :--- | :--- |
| **Synthesis** | Converts your Verilog code into a gate-level netlist of standard logic cells. |
| **Floorplanning** | Defines the chip’s physical dimensions, allocates space for blocks, and plans the power grid (like creating a building blueprint). |
| **Placement** | Places thousands of standard cells within the floorplan, aiming for optimal performance and routability. |

-----

## ⚙️ Step 1: Installation and Setup

### 1.1 Clone the Repository

```bash
# Clones the flow scripts repository and all its submodules (--recursive)
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts.git
# Enters the newly cloned directory
cd OpenROAD-flow-scripts
```
<img width="1210" height="773" alt="Screenshot from 2025-10-21 05-51-18" src="https://github.com/user-attachments/assets/1564c359-48bf-4969-8882-9997cb0d71a7" />

### 1.2 Install Dependencies

```bash
# Runs the official script to install all required libraries and tools
sudo ./etc/DependencyInstaller.sh -all
```
> ⏳ May take an hour or more — especially building libraries like Boost or Eigen.
### 1.3 Check yosys ans openroad

```bash
# Sets up the environment variables (like $PATH) needed to run the tools
source ./env.sh
# Checks if Yosys (synthesis tool) is installed and prints help
yosys -help  
# Checks if OpenROAD (P&R tool) is installed and prints help
openroad -help
```
<img width="1211" height="685" alt="Screenshot from 2025-10-24 10-50-50" src="https://github.com/user-attachments/assets/dd00d5dc-a922-4dff-abf0-0f12d8390be7" />

<img width="1211" height="368" alt="Screenshot from 2025-10-24 10-51-15" src="https://github.com/user-attachments/assets/dea52a95-e170-497b-9bfd-93dcf1e7db1d" />


**Manual setup (if needed):**

```bash
# Refreshes the local package database list
sudo apt update
# Installs all necessary packages for building (compilers, libraries, etc.)
sudo apt install -y build-essential cmake python3 python3-venv python3-pip \
tclsh libreadline-dev bison flex libffi-dev git \
libboost-all-dev clang libeigen3-dev swig \
libx11-dev libxaw7-dev libxrandr-dev libxcb1-dev \
libxinerama-dev libxcursor-dev libxft-dev
```

### 1.3 Build OpenROAD

```bash
# Compiles and builds the OpenROAD application locally from source code
./build_openroad.sh --local
```
> ⏳ May take an hour or more —
-----

## 🏃‍♂️ Step 2: Running the Flow (Up to Placement)

```bash
# Enters the 'flow' directory where the Makefiles are located
cd flow
# Runs the flow up to the 'place' step for the 'gcd' design
make DESIGN_CONFIG=./designs/sky130hd/gcd/config.mk place
```
<img width="1209" height="685" alt="Screenshot from 2025-10-24 10-56-28" src="https://github.com/user-attachments/assets/22b71f04-5470-4f4f-8607-3973e2ec8610" />

📁 Output DEF/ODB files in:

```
results/sky130hd/gcd/base/
2_floorplan.def
3_place.def
```
<img width="1211" height="771" alt="Screenshot from 2025-10-24 10-53-51" src="https://github.com/user-attachments/assets/9d7652ce-f70e-417a-a4ad-fda75a537fa5" />



-----

## 🖼️ Step 4: Visualizing the Layout

### 4.1 Using OpenROAD GUI

```bash
# Navigates to the root of the flow scripts directory
cd ~/OpenROAD-flow-scripts
# Sets up the environment variables
source ./env.sh
# Navigates back into the flow directory
cd flow
# Starts the OpenROAD tool in graphical user interface mode
openroad -gui
```

Open floorplan: `results/sky130hd/gcd/base/2_floorplan.odb`
Open placement: `results/sky130hd/gcd/base/3_place.odb`

<img width="857" height="650" alt="Screenshot from 2025-10-24 10-58-34" src="https://github.com/user-attachments/assets/848b8f1c-d413-43d2-9ed5-5b11c041c0d2" />

<img width="961" height="459" alt="Screenshot from 2025-10-24 10-59-41" src="https://github.com/user-attachments/assets/deeb75e1-7f2b-42f8-9987-f1a24be25900" />

<img width="961" height="459" alt="Screenshot from 2025-10-24 11-00-35" src="https://github.com/user-attachments/assets/8ed7e616-807e-41d1-92c0-899166fe51bb" />
<img width="1148" height="454" alt="Screenshot from 2025-10-24 11-03-24" src="https://github.com/user-attachments/assets/ac245cc5-71c2-46ef-b743-7de66a5490b8" />

### OUTPUTS

<img width="1194" height="732" alt="Screenshot from 2025-10-24 11-02-00" src="https://github.com/user-attachments/assets/06a6aca7-a454-4805-b50e-571d6568fdf1" />

<img width="1206" height="747" alt="Screenshot from 2025-10-24 11-04-07" src="https://github.com/user-attachments/assets/f3699039-0f4f-47b8-9661-ecaaa7d6fa28" />


### 4.2 Using KLayout

```bash
# Navigates directly to the flow directory
cd ~/OpenROAD-flow-scripts/flow
# Starts the OpenROAD tool in command-line (Tcl) mode
openroad
```

In OpenROAD shell:

```tcl
# Reads the base technology LEF file (physical library)
read_lef platforms/sky130hd/lef/sky130_fd_sc_hd.tlef
# Reads the merged LEF file containing all standard cells
read_lef platforms/sky130hd/lef/sky130_fd_sc_hd_merged.lef
# Loads the floorplan database file (.odb) into memory
read_db results/sky130hd/gcd/base/2_floorplan.odb
# Exports the loaded floorplan as a .def file (for KLayout)
write_def results/sky130hd/gcd/base/2_floorplan.def
# Loads the placement database file (.odb) into memory
read_db results/sky130hd/gcd/base/3_place.odb
# Exports the loaded placement as a .def file (for KLayout)
write_def results/sky130hd/gcd/base/3_place.def
# Exits the OpenROAD shell
exit
```
<img width="1192" height="361" alt="Screenshot from 2025-10-24 11-06-05" src="https://github.com/user-attachments/assets/e85e97ff-25ec-44da-850a-cff84794941a" />

Open in KLayout:

```bash
# Starts KLayout, loading the .lef files as technology and opening the .def layout
klayout -l platforms/sky130hd/lef/sky130_fd_sc_hd.tlef \
        -l platforms/sky130hd/lef/sky130_fd_sc_hd_merged.lef \
        results/sky130hd/gcd/base/2_floorplan.def
```
<img width="1160" height="748" alt="Screenshot from 2025-10-24 11-14-04" src="https://github.com/user-attachments/assets/f5d90be9-c35b-4a73-a774-289a110bdc1b" />

## 🧾 Results Summary

| Stage | File | Description |
| :--- | :--- | :--- |
| Floorplan | `2_floorplan.odb / .def` | Core area defined, IOs placed |
| Placement | `3_place.odb / .def` | Standard cells placed and legalized |

-----

## 🏁 Conclusion

✅ Installed and configured OpenROAD
✅ Built and debugged all dependencies
✅ Executed RTL → Placement flow
✅ Visualized layout using OpenROAD GUI and KLayout

Demonstrates a **complete mini physical design flow** using **Sky130 PDK**, bridging **digital logic** to **physical chip layout**.

-----
