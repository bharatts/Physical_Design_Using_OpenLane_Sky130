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

      We can improve slack by changing the size of the stdcell       
      ![changing_size](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/changing%20cell%20size%20to%20improve%20slack.jpg)
   
      To check report after increasing cell size ``` report_checks -fields {net cap slew input_pins} -digits 4 ```

      Result - slack improved by some factor
      ![impproved slack](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/improved_slack.jpg)

      - Next steps is to update the synthesis netlist type this command in OpenSTA Terminal
        ```
        write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/nosynthcmds/results/synthesis/picorv32a.synthesis.v
        ```

       we can confirm the replace cell in netlist
      ![cell change](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/confirming_cell_changed_in_netlist.jpg)
      - Run floorplan and placement stage after synthesis netlist is saved
        ```
        init_floorplan
        place_io
        global_placement_or
        tap_decap_or
        run_placement
        ```

     - Check the default parameters type below commands
       ```
        echo $::env(LIB_SYNTH_COMPLETE)
        echo $::env(LIB_TYPICAL)
        echo $::env(CURRENT_DEF)
        echo $::env(SYNTH_MAX_TRAN)
        echo $::env(CTS_MAX_CAP)
       ```

       ![default value](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/cts_defulat_value.jpg)
       

  - OpenRoad , to invoke openroad type ``` openroad ``` in terminal and type these following commands

    We will do timing analysis after creating db file, so for creating db file we need led and def file as input,
   ```
    read_lef /openLANE_flow/designs/picorv32a/runs/nosynthcmds/tmp/merged.lef
    read_def /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/cts/picorv32a.cts.def
    write_db pico_cts.db
    read_db pico_cts.db
   ```
   After cts statge clock network has been build so using real clock timing analysis is done and check the report generated by ``` report_checks -path_delay min_max -format full_clock_expanded -digits 4```
    ```
    read_verilog /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/synthesis/picorv32a.synthesis_cts.v

    read_liberty -max $::env(LIB_SLOWEST)
    read_liberty -min $::env(LIB_FASTEST)
    read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
    set_propagated_clock [all_clocks]
    report_checks -path_delay min_max -format full_clock_expanded -digits 4

    ```
  - we are using min_max lib not typical lib
  
  ![cts_value](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/cts_defulat_value_commands.jpg)
  ![post_cts_timing_analysis](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/post_cts_timing_analysis.jpg)

  - Now exit the openroad, type ```exit```
    
  - using typical lib, timing analysis will be done
    ```
    openroad
    read_db pico_cts.db
    read_verilog /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/synthesis/picorv32a.synthesis_cts.v
    read_liberty $::env(LIB_SYNTH_COMPLETE)
    link_design picorv32a
    read_verilog /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/synthesis/picorv32a.synthesis_cts.v
    set_propagated_clock [all_clocks]
    report_checks -path_delay min_max -format full_clock_expanded -digits 4
    ```
    ![cts typical](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/post_cts_timing_analysis_tpyical.jpg)
    - ```exit``` openroad
  - Removing the clk_buf and to see the timing report and impact it has on slack
    ```
    echo $::env(CTS_CLK_BUFFER_LIST)
    lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0
    echo $::env(CTS_CLK_BUFFER_LIST)
    set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
    echo $::env(CTS_CLK_BUFFER_LIST)
    run_cts
    ```
    ![clk buf remove](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/cts_buf_cell_chnage.jpg)
    cts stage will failed so we have to update current_def file and run again cts stage
  - Updating CURRENT DEF file so, type these following cmds
    ```
    echo $::env(CURRENT_DEF)
    set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/placement/picorv32a.placement.def 
    echo $::env(CURRENT_DEF)
    echo $::env(CTS_CLK_BUFFER_LIST)
    run_cts

    ```
 - Invoke OpenRoad
   ```
    openroad
    read_lef /openLANE_flow/designs/picorv32a/runs/nosynthcmds/tmp/merged.lef
    read_def /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/cts/picorv32a.cts.def
    write_db pico_cts.db
    read_db pico_cts.db
    read_verilog /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/synthesis/picorv32a.synthesis_cts.v
    read_liberty $::env(LIB_SYNTH_COMPLETE)
    link_design picorv32a
    read_verilog /openLANE_flow/designs/picorv32a/runs/nosynthcmds/results/synthesis/picorv32a.synthesis_cts.v
    set_propagated_clock [all_clocks]
    report_checks -path_delay min_max -format full_clock_expanded -digits 4

   ```
  - ```exit``` openroad and adding again clk_buf
    ```
    echo $::env(CTS_CLK_BUFFER_LIST)
    set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky_fd_sc_hd__clkbuf_1]

    ```
  ![clk buf added](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/again_added_clk_buf.jpg)

# Day 5 : Final steps for RTL2GDS

 - Generating Power Delivery Network (pdn)
   ``` gen_pdn ```

   ![pdn](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/gen_dpn.jpg)

- Invoking TritonRoute tool for routing
  ``` run_routing``` after generating power delivery network

  ![rounting](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/run_routing.jpg)

  Tool generate a png file in results/routing/picorv32a.def.png
  ![tool png](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/routing.jpg)

  Result from magic tool

  ![magic_routin](https://github.com/bharatts/Physical_Design_Using_OpenLane_Sky130/blob/main/images/routing_magic.jpg)

# ACKNOWLEDGEMENT
- Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd.
- Nickson Jose
- Ahmed Ghazy, openlane team, efabless
