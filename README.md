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
![openlane_invoke]()














