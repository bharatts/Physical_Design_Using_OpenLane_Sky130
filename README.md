# Physical_Design_Using_OpenLane_Sky130

Physical Design or PnR (Place and Route) is the core of any IC design cycle. From a RTL netlist to final tape-out, each phase of PnR brings it’s own challenges and surprises. “What are these challenges?” “What is the process?” “Can I build a chip of my own?”- If you have these questions and if you are eager to delve into the world of ASIC design flow..Wait no more!

With the announcement of Google-SkyWater’s first manufacturable open source 130nm process design kit (pdk), open source EDA world is no longer limited in scope to academic research and small scale projects only. This along with the conception of Openlane flow, a fully-automated RTL2GDSII flow, has made the dream of “an IC for all” a near reality.
So here’s announcing the ultimate workshop on SoC design planning in Openlane flow using the latest Google-SkyWater 130nm process node.

![vsd_logo](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/vsd_logo.jpg)

- Day1 : Inception of open-source EDA, OpenLANE and Sky130 PDK
  * How to talk to computers
  * SoC design and OpenLANE
  * Starting RISC-V SoC Reference design
  * Get familiar to open-source EDA tools
- Day 2 : Understand importance of good floorplan vs bad floorplan and introduction to library cells
  * Chip Floor planning considerations
  * Library Binding and Placement
  * Cell design and characterization flows
  * General timing characterization parameters
- Day 3 : Design and characterize one library cell using Magic Layout tool and ngspice
  * Labs for CMOS inverter ngspice simulations
  * Inception of Layout – CMOS fabrication process
  * Sky130 Tech File Labs
- Day 4 : Pre-layout timing analysis and importance of good clock tree
  * Timing modelling using delay tables
  * Timing analysis with ideal clocks using openSTA
  * Clock tree synthesis TritonCTS and signal integrity
  * Timing analysis with real clocks using openSTA
- Day 5 : Final steps for RTL2GDS
  * Routing and design rule check (DRC)
  * PNR interactive flow tutorial

# Day 1 : Inception of open-source EDA

OpenLANE is a completely automated RTL to GDSII flow which embeds in it different opensource tools, namely, OpenROAD, Yosys, ABC, Magic etc., apart from many custom methodology scripts for design exploration and optimization. Openlane is built around Skywater 130nm process node and is capable of performing full ASIC implementation steps from RTL all the way down to GDSII. The flow-chart below gives a better picture of openlane flow as a whole (Image Courtesy: efabless/openlane)

![flow](https://raw.githubusercontent.com/nickson-jose/vsdstdcelldesign/master/Images/openlane.flow.1.png)

### Invoking Openlane

```
-> docker
% ./flow.tcl -interactive
```

![openlane_invoke](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/invoking_openlane.jpg)

### Design Preparation
```
% package require openlane 0.9
% prep -design picorv32a
```
![prpe design](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/design_prep.jpg)

### Running Synthesis
```
% run_synthesis
```
![run_synthesis](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_synthesis_day2.jpg)

### Output Directory Structure
In Output dir we can find all run time logs, report and result associate with each design stage.
![dir struct](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_directory_struct.jpg)

# Day 2 : Understand importance of good floorplan vs bad floorplan and introduction to library cells

### Floorplaning 
Defining the core area for the macro as well as the cell sites and the tracks (init_fp). Placing the macro input and output ports (ioplacer). Generating the power distribution network (pdn).

```
% run_floorplan
```
![floorplan image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_floorplan_day2.jpg)

- Results
![image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/Screenshot%202023-08-15%20191957day2_floorplan.jpg)

To see floorplan in magic tool
```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read merged_unpadded.lef def picorv32a.floorplan.def &
```
![image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/floorplan_result_day2.jpg)

### Placement
Performing global placement (RePLace). Perfroming detailed placement to legalize the globally placed components (OpenDP)
```
% run_placement
```
![run_placement](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_placement_day2.jpg)

 - Results
![placement_result](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/placement_result_day2.jpg)

To open using magic 
```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read merged_unpadded.lef def picorv32a.placement.def &
```
![placement1_result](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/placement1_result_day2.jpg)
