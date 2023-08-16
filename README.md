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

- OpenLANE is a completely automated RTL to GDSII flow which embeds in it different opensource tools, namely, OpenROAD, Yosys, ABC, Magic etc., apart from many custom methodology scripts for design exploration and optimization. Openlane is built around Skywater 130nm process node and is capable of performing full ASIC implementation steps from RTL all the way down to GDSII. The flow-chart below gives a better picture of openlane flow as a whole (Image Courtesy: efabless/openlane)

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
- In Output dir we can find all run time logs, report and result associate with each design stage.
![dir struct](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_directory_struct.jpg)

# Day 2 : Understand importance of good floorplan vs bad floorplan and introduction to library cells

### Floorplaning 
- Defining the core area for the macro as well as the cell sites and the tracks (init_fp). Placing the macro input and output ports (ioplacer). Generating the power distribution network (pdn).

```
% run_floorplan
```
![floorplan image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_floorplan_day2.jpg)

- Results
![image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/Screenshot%202023-08-15%20191957day2_floorplan.jpg)

- To see floorplan in magic tool
```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read merged_unpadded.lef def picorv32a.floorplan.def &
```
![image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/floorplan_result_day2.jpg)

### Placement
- Performing global placement (RePLace). Perfroming detailed placement to legalize the globally placed components (OpenDP)
```
% run_placement
```
![run_placement](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_placement_day2.jpg)

- Results
![placement_result](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/placement_result_day2.jpg)

- To open using magic 
```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read merged_unpadded.lef def picorv32a.placement.def &
```
![placement1_result](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/placement1_result_day2.jpg)


# Day 3 : Design and characterize one library cell using Magic Layout tool and ngspice

- The goal of the project is to design a single height standard cell and plug this custom cell into a more complex design and perform it's PnR in the openlane flow. The standard cell chosen is a basic CMOS inverter and the design into which it's plugged into is a pre-built picorv32a core.

- So, we will clone the stdcell repo using this command ``` git clone https://github.com/nickson-jose/vsdstdcelldesign ``` at the openlane directory.
Next step is copy tech file (sky130A.tech) which is present at the pdks directory under libs.tech to the clone directory 

- Next is to open magic tool to see stdcell design 
``` magic -T sky130A.tech sky130_inv.mag ```

![stdcell](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/vsdstdinv_day3.jpg)

- Steps to extract spice netlist : In magic tool console type below command to generate spice netlist
  ```
  extract all
  ext2spice cthresh 0 rthresh 0
  ext2spice
  ```

  ![command_netlist](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/extract_all_day3.jpg)

- After running above commands netlist will be generate and it will look like below image

![netlist](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/day3_spice_netlist.jpg)

- After modifying to netlist according to tech file which have pmos & nmos model

  
![modifidy_netlist](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/spice_file.jpg)
  
- Invoking ngspice to transient analysis, also to get fall,rise transition time and cell rise,fall delay by plotting input and output using
  ```
  plot y vs time a
  ```
![ngspice](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/invoking_ngspice_day3.jpg)


- Plotted curve

![image](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/ngspice_plot_day3.jpg)

![rise_fall_time](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/rise_and_fall_trans_delay_day3.jpg)

# Day 4 : Pre-layout timing analysis and importance of good clock tree

- Steps to covert grid info to track info
   - In magic tool console type ``` grid 0.46um 0.34um 0.23um 0.17um ```

     ![grid on](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/grid_on_day4.jpg)

  - Track file which present in pdks dir

    ![track file](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/track_info_day4.jpg)
  
  - Giving custom name to cell and saving it and also writing cell file using below commands
    ```
    save sky130_vsdinv.mag
    lef write
    ```

  ![custom name](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/day4_giving_custom_name_to_cell.jpg)
  ![lef_write](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/lef_write_day4.jpg)
  ![lef file location](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/day4_lef_file.jpg)

  - lef file
  ![lef_file](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/vsdinv_lef_file_day4.jpg)


  - After creating lef file, we need to cp .lef and .lib file from our clone stdcell design repo to design i.e picorv32a/src
  ``` cp ~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/sky130_vsd.lef ~/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/libs/sky130_fd* ~/Desktop/work/tools/openlane_working_dir/openlane/designspicorv32a/src/.```

  ![copying](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/day4_copying_lef_tech_file_from_vsdcell%20to%20design_src.jpg)

  - Modify design.picorv32a.conf.tcl
  ![conf.tcl](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/day4_conf_tcl_file.jpg)

  - In terminal type following commands
    ```
    docker
    ./flow -interactive
    package require openLANE 0.9
    prep -design picorv32a -tag 12-08_20-54 -overwrite
    set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
    add_lefs -src $lefs
    run_synthesis
    ```
    ![run_synthesis](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/synth_run.jpg)

    - Run floorplan if it is failed then type below commands in order
    ```
    init_floorplan
    place_io
    global_placement_or
    tap_decap_or
    run_placement
    ```
    ![Placement run](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_placement.jpg)
    ![Placement result](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/placement_result.jpg)
    ![Placement run1](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/placement_result_cell_confirm.jpg)

    - Creating a new SDC constraint file

     ![sdc file](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/sdc_file.jpg)

    - Creating a new pre_sta conf file for timing analysis

      ![pre_conf file](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/pre_sta_conf%20file.jpg)

    - Invoking OpenSTA for analysis using ``` sta pre_sta.conf ```
      ![sta result](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/sta_result.jpg)

    - To improve slack we can use trade of between delay and area, openlane have configuration paarameter which are mentioned in README.md
      ```
      set ::env(SYNTH_SIZING) 1
      set ::env(SYNTH_MAX_FANOUT) 4
      set ::env(SYNTH_STRATEGY) "DELAY 0"
      ```

      We can improve slack by changing the size of the stdcell we improve the driving strength
      
      ![changing_size](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/changing%20cell%20size%20to%20improve%20slack.jpg)

      

















# Day 5 : Final steps for RTL2GDS


# ACKNOWLEDGEMENT
- Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd.
- Nickson Jose
- Ahmed Ghazy, openlane team, efabless
