
# IHC schematic



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
  => Z coordinates have to be inverted (otherwise will crash)

- Change units to mm/min instead of default mm/s

## pcb2gcode
see config file: pcb2gcode/millproject
