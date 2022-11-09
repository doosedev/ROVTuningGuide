# ROVTuningGuide

# Table of Contents
[Arduino Setup](#arduino-setup)  
[UI Setup](#ui-setup)  
[Vehicle Tuning](#tuning-the-vehicle)

# Arduino Setup
## Installing VSCode
1. Download the appropriate release of Visual Studio Code for your operating system from [this link](https://code.visualstudio.com/download)
2. Install VSCode, open it up, and complete first-time setup

## Installing Platform.io
1. Within VSCode, open the extension manager
2. Search for "PlatformIO IDE", as shown below
3. Install the PlatformIO IDE extension
4. If not done automatically, restart VSCode

![VSCode Screenshot](https://cdn.platformio.org/images/platformio-ide-vscode-pkg-installer.4463251e.png)

The PlatformIO installation will include all necessary Arduino & C++ tools, so we can move on to the firmware setup

## Flashing the Firmware
1. From the PlatformIO home screen, choose Open Project
2. Find and open the ROVTuningFirmware project you downloaded
3. In the very bottom bar, make sure the selected environment is `env:mega`
4. Connect your Arduino Mega directly to your computer
5. Press the `Upload` button on the bottom bar, which looks like a small arrow

# UI Setup
## Launching the UI
1. Navigate to the ROVTuningUI folder
2. Open the `windows-amd64` folder and launch `ROVTuningUI.exe`
3. If prompted with a warning to install Java, follow the listed steps
4. You should see a window like the one below:

![UI Screenshot](https://user-images.githubusercontent.com/111624006/200897850-fcf1bf29-067a-4504-a791-c1f13098e351.png)

## UI Anatomy
### Control Bar
![image](https://user-images.githubusercontent.com/111624006/200899155-f8919601-0dc4-4813-abd6-7374081db34f.png)
The control bar manages connection to the submarine, activation and deactivation of the software arming switch, and saving/loading settings from the Arduino's onboard memory.

### Gains/Setpoints/Console
![image](https://user-images.githubusercontent.com/111624006/200899448-7ed13bd0-b007-4880-a2df-725d866ef35b.png)
PID gains for the four (yaw, pitch, roll, depth) controllers can be found under their respective accordion sections. All gains default to (0, 0, 0, 0) and deactivated. Gains can be loaded onto the Arduino with either their individual `Send` button, or the lower `Send All` button.  

Setpoints can be set for each controller as well, and for ease of use setpoints are sent automatically upon moving the control sliders.  

A set of gains can be stored, edited, and loaded in the text boxes below the `Save Gains` and `Load Gains` buttons. This can be used to store the last known "good" set of controller gains, or to manually input more precise values.  

The console can be safely ignored during normal operations, but will show any errors or abnormal Arduino responses that come up the tether.

### Vehicle Overview
![image](https://user-images.githubusercontent.com/111624006/200900125-b67974bc-e294-4c39-b58e-9656e3940630.png)
An image of the ROV and its six thrusters is paired with six bidirectional bar levels to show the current state of the thruster outputs. This can be used to validate the controllers are working properly by turning on the software arming switch, but keeping the hardware kill switch turned off. Be sure to disarm the submarine before pressing the hardware kill switch.

### Telemetry
![image](https://user-images.githubusercontent.com/111624006/200900558-8d6e76dd-60af-4bb1-aabf-b89a9e6b46c6.png)
One graph for each of the four controller variables is displayed. The graphs span 5 seconds into the past, and can be used to hunt down oscillations, or characterize the response curve of your controller and set of gains.

## Connecting the ROV
1. On the control bar, click `Choose Serial Port`
2. A window like the one shown below should appear, with a drop-down list of available serial ports
3. Select the appropriate serial port for your connected Arduino, and press OK

![image](https://user-images.githubusercontent.com/111624006/200901044-a84138e1-8c09-4f44-a679-2b94e6807f76.png)

4. Again on the control bar, click `Connect`
5. You shuold see your chosen serial port and a `Connected` text appear above the control bar
6. If the text `Connected` doesn't appear, press `Disconnect`, un- and re-plug your Arduino cable, and try again

## Arming the ROV
1. After connecting to the vehicle, the software arming switch can be controlled with the green `Arm` and red `Disarm` buttons. The `SAFE` and `ARMED` text in the vehicle overview show the current vehicle status, and so requires a two-way handshake with the controller for safety
2. **NEVER** allow someone to approach the vehicle without a confirmed disarmed state in the UI

## Setting/Sending Gains
1. Open the appropriate gains accordion for the controller you wish to tune
2. Drag the four sliders (proportional, integral, derivative, and anti-windup) to your desired gains settings
3. Ensure the controlled is `Enabled` and click `Send`

## Saving/Loading Gains Locally
1. To store the current gains from all four controllers, press the `Save Gains` button
2. The gains for each controller can be edited in their text boxes if desired
3. To load the gains back into the local editing accordions, press `Load Gains`. This does not send the gains to the robot, but only loads them into the sliders for editing

## Saving/Loading Gains to the Vehicle
1. To save or load a set of gains from the Arduino's onboard memory, use the `Save Gains to EEPROM` and `Load Gains from EEPROM` buttons on the right side of the control bar
2. Make sure the vehicle is disarmed before loading gains from EEPROM, to avoid sudden unexpected behaviors
3. Don't forget to save your set of gains to EEPROM before disconnecting, or they may be lost

# Tuning the Vehicle
## Tuning order
Due to the natural stability of the vehicle, yaw, pitch, and roll will be passively stable enough to avoid tuning on the surface. As such, the order of controller tuning can be as follows:
1. Depth
2. Pitch
3. Roll
4. Yaw

## Tuning a Controller
1. First, set a setpoint for the controller you wish to tune
2. Raise the controller's proportional (P) gain until you get a control response
3. Don't forget to enable the controller and send each new set of gains while tuning
4. Raise the proportional gain further until you get a small oscillation when changing setpoints
5. Raise the derivative (D) gain until the oscillations are dampened after one or two peaks
6. Repeat steps 4 and 5 until you can no longer find a stable combination of P and D
7. Return the gains to the last stable set you found (this is a good use of the stored gains!) 
8. If you observe any steady-state error, increase the integral (I) and antiwindup (A) gains a bit to apply correction over time
