Repo for designing a custom pcb that controlls the electrical components of an IHC-Tractor.



# TOC
- [Links](#links)
- [Schematic, Layout](#schematic--layout)
- [Connector Pin-assignment](#connector-pin-assignment)
  * [New Connectors](#new-connectors)
  * [Old Connectors](#old-connectors)
- [I/O List](#i-o-list)
  * [Switches](#switches)
  * [Relays](#relays)
- [Instructions (milling process)](#instructions--milling-process-)
  * [Step 1: export files](#step-1--export-files)
  * [Step 2: generate gcode (holes, traces, outline)](#step-2--generate-gcode--holes--traces--outline-)
  * [Step 3: generate gcode (engraving text)](#step-3--generate-gcode--engraving-text-)
  * [Step 4: mill outline](#step-4--mill-outline)
  * [Step 5: post processing](#step-5--post-processing)
- [Notes for next version (pcb design)](#notes-for-next-version--pcb-design-)
- [Notes for next pcb milling](#notes-for-next-pcb-milling)
  * [CNC-Control (WinPC-NC)](#cnc-control--winpc-nc-)
  * [G-code generation (pcb2gcode)](#g-code-generation--pcb2gcode-)



# Links
- Project documentation: https://pfusch.zone/control-pcb
- Firmware repo: https://github.com/Jonny999999/ihc-control_fw



# Open Schematic and Layout
Install kicad and open /kicad/pcb-ihc_kicad.kicad_pro  

For quickly viewing the current schematic and layout without installing kicad see exported files in
`kicad/export` or complete documentation at https://pfusch.zone



# Connector Pin-assignment
## New Connectors
See plan in firmware repo: 
https://github.com/Jonny999999/ihc-control_fw/blob/master/connection-plan.pdf

## Old Connectors
### 2 Pole connector 6mm2:
- 1: Starter motor (9)
- 2: Glowplug

### 6 Pole PC (3)
- 1: empty
- 2: Front light right, left (top right)
- 2a: Switch brakelight LS 1/2
- 3: Horn 1/2
- 3a Switch brakelight LS 2/2
- 4: Light left AL, right AR (bottom right)

### 6 Pole PB (2)
- 1: (20) Starter motor (relay) (top left)
- 2: (22) Fuel sensord
- 3: (24) Horn 2/2
- 4: (26) Oil pressure (switch to gnd)
- 5: (28) Temperature sensor
- 6: (30) Voltage regulator alternator (?)

### 6 Pole PA (1)
- 1: (31,32) FD sidelight -> trailer connector
- 2: (37) LB LB Light left -> trailer connector
- 3: (90) Side light BR
- 4: (88) Side light BL
- 5: (43, 42) FA -> trailer connector
- 6: (48) LA light left -> trailer connector



# I/O List
List of required I/O pins for planning the board
## Switches
### Switch to GND (pullup input)
- S_Warning Lights
- S_TurnLeft  toggle
- S_TurnRight toggle
- S_HighBeam  momentary
- S_Warning Lights  toggle
- S_Horn

### Switch to 12V (pulldown input)
- S_ParkingLight
- S_LowBeam
- S_Brake

### Notes
- S_worklight !!! no input left?!

## Relays
- K_BlinkLeft
- K_BlinkRight
- K_ParkingLight
- K_LowBeam
- K_HighBeam
- K_Horn




# Instructions (milling process)
Machine used: Stepcraft 420
## Step 1: export files
- Kicad: Export/plot pcb to gerber files
  Note: Also export User1 layer (includes text for engraving)
- Kicad: Generate drill file
```
back=./in/pcb-ihc_kicad-B_Cu.gbr
front=./in/pcb-ihc_kicad-F_Cu.gbr
outline=./in/pcb-ihc_kicad-Edge_Cuts.gbr
drill=./in/pcb-ihc_kicad-PTH.drl
see config file: pcb2gcode/millproject
```

## Step 2: generate gcode (holes, traces, outline)
- run pcb2gcode: `ihc-control_pcb/pcb2gcode> pcb2gcode`  
  will use millproject file for parameters as default config

## Step 3: generate gcode (engraving text)
- Kicad: moved text to be engraved into pcb on separate layer (User1)
- Also export/plot User1 layer with other gerber files
- Use separate millproject_text parameter file with pcb2gcode
  `pcb2gcode --config millproject_text`
NOTE: also include outline and maybe front in that millproject, even though that output is not used. It is necessary that the origin fits, otherwise there will be an offset!
- The trick was to use a negative offest (almost half the line width) to get more or less clean characters
- Generates pcb2gcode/out/text/back.ngc
Note: check if there is an offset/scaling issue to other files (back, drill). last time text got engraved into traces even though it matched on the other side

## Step 4: mill outline
Was not necessary in this case because size already matched

## Step 5: post processing
- Slightly smooth copper layer of pcb using grinding wool
- Drill holes for big 4mm2 2-pin terminals to 1.5mm if drilled with just 1mm on cnc




# Notes for next version (pcb design)
- Add resistor and diode for slowly charging the input capacitor (limit charging current)
- Add diode and/or jumper for isp programmer. Noticed that current is flowing from board 5v to programmer (input resistor got hot - only when programme connected) (alternatively jumper on programmer can be used for this...)
- ADD FUSES
  - Add fuse for 12v supply to 5v regulator
  - Fuses for mosfets?
  - Fuses for relay?
- Add mounting holes / at least make some space for third hole
- Slightly smaller size for easier cnc-milling




# Notes for next pcb milling
## CNC-Control (WinPC-NC)
### Parameters
- WinPC-NC has weird default z coorinates direction
  => **Z coordinates have to be inverted (!!!!!)** (otherwise will crash)
  -> parameters -> dateiformat -> z-koordinaten invertieren
- Change units to mm/min instead of default mm/s
### Occurred problems
- Problem Stepper not moving, only producing noise, but winpc-nc actually thinks that it moved coordinates - especially happened for Z-Axis
  - Solution 1: remove top section of gcode (simple G1 commands worked - see gcode generated with estlcam that is compatible)
  - Solution 2: switch from windows in Virtualbox to native windows

## G-code generation (pcb2gcode)
### notes for next time:
TODO: merge this section with milling instructions?
- Text engraving: slightly reduce Z depth 
- Zero Z-axis on multiple places: 
  At 2 edges pcb material was apparently lower, thus only scratched the surface -> had to mill again
- Zero-XY NOT at file origin! (the offset is caused by outline cutter diameter) 
  - 1. Load outline gcode, zero x,y next to border (note offset) 
  - 2. Confirm with moving on other side of pcb
- Make screenshot of absolute coordinates of origin (in case of accidential shift / position loss)
- Check position before drilling holes
- Fixing the pcb: Screws around the pcb are probably enough (check bend)
- When generating gcode for text separately, also include at least outline, otherwise there is an offset (origin makes no sense)

### Gerber export (kicad)
- design pcb slightly smaller for more tolerance...
- define custom origin 
  => tried this, setting "drill/place file origin" and tick the checkbox at plot, but had no effect to pcb2gcode in any way
- check if origin is the same in every gerber/gcode file (verify with gcode viewer?)
