
# IHC schematic


##Steckerbelegung
###2Pol stecker 6mm2:
1: Anlasser 12V versorgung (9)
2: Gluehspirale

###6Pol? PC #Stecker 3#
1: leer
2: Scheinwerfer rechts AR; Scheinwerfer links AL    - rechts oben
2a: Bremslichtschalter LS 1/2
3: Hupe 1/2
3a: Bremslichtschalter LS 2/2
4: Scheinwerfer links AL, Scheinwerfer rechts AR    - rechts unten

###6Pol?`PB   #Stecker 2#
1: (20) Anlasser (Start)                - links oben
2: (22) Kraftstoffanzeiger (geber)
3: (24) Hupe 2/2
4: (26) Oeldruckschalter (switches to gnd)
5: (28) Fernthermomenter (geber)
6: (30) Spannungsregler lm??

###6pol PA     #Stecker 1#
1: (31,32) Steckdose; Klemme FD seitenlichter BR CR (Blink-Begrenzungslampe rechts, Schlussbegrenzungsleuchte rechts)       -links oben
2: (37) Klemme LB   -> Seitenlichter BL, CL (Blink-Begrenzungslampe links, Schluss-blinkleuchte links)              - links mitte?
                    -> Steckdose
3: (90) Klemme Fd -> seitenlicht BR
4: (88) Klemme Fc -> seitenlicht BL
5: (43,42) Klemme Fa  -> Steckdose                                  - rechts mitte
            Klemme Fb (1/2) -> Seitenlichter BR, Cr (Blink-begrenzungslampe rechts, schluss-blinkleuchte rechts)
                      -> Seitenlicht Cr (Schluss-blinleuchte rechts)
            
6: (48) Klemme La -> Seitenlicht CL (Schlussb-blinkleuchte link)            - rechts unten
                    -> Steckdose



# I/O Definition
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





# Notes pcb milling
Machine used: Stepcraft 420

## Parameters WinPC-NC
- WinPC-NC has weird default z coorinates direction
!!!!!  
  => **Z coordinates have to be inverted** (otherwise will crash)
  -> parameters -> dateiformat -> z-koordinaten invertieren
!!!!!  

- Change units to mm/min instead of default mm/s

## pcb2gcode
- kicad: export/plot pcb to gerber files
- kicad: generate drill file
```
back=./in/pcb-ihc_kicad-B_Cu.gbr
front=./in/pcb-ihc_kicad-F_Cu.gbr
outline=./in/pcb-ihc_kicad-Edge_Cuts.gbr
drill=./in/pcb-ihc_kicad-PTH.drl
see config file: pcb2gcode/millproject
```
- run pcb2gcode: `ihc-control_pcb/pcb2gcode> pcb2gcod`  
  will use millproject file for parameters as default config


### engraving text
- kicad: moved text to be engraved into pcb on separate layer User1.  
- also export/plot User1 with other gerber files
- use separate millproject_text parameter file with pcb2gcode
- the trick was to use a negative offet (almost half the line width) to get more or less clean characters
`pcb2gcode --config millproject_text`
- generates pcb2gcode/out/text/back.ngc
