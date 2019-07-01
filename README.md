# Limbitless Solutions Inc. ISP1507, nrF52 Based Board Setup
This README will detail everything from start to finish to set up the LSI Board for programming via the Arduino IDE. It will have a troubleshooting section in case of any roadblocks.

## Table of Contents
- [Beginning Workflow Setup](https://github.com/AlexanderMcLaughlin/TestRepo#beginning-workflow-setup)
  - [Things to Download](https://github.com/AlexanderMcLaughlin/TestRepo#things-to-download)
  - [nRFgo Studio](https://github.com/AlexanderMcLaughlin/TestRepo#nrfgo-studio)
  - [Arduino IDE](https://github.com/AlexanderMcLaughlin/TestRepo#arduino-ide)
  - [Zadig.exe](https://github.com/AlexanderMcLaughlin/TestRepo#zadigexe)
- [Firmware Replacement and Driver Updating](https://github.com/AlexanderMcLaughlin/TestRepo#firmware-replacement-and-driver-updating)
  - [Initial Steps](https://github.com/AlexanderMcLaughlin/TestRepo#initial-steps)
  - [JLink Configurator Firmware Replacement](https://github.com/AlexanderMcLaughlin/TestRepo#jlink-configurator-firmware-replacement)
  - [nRFgo Studio Firmware Deletion](https://github.com/AlexanderMcLaughlin/TestRepo#nrfgo-studio-firmware-deletion)
  - [Replacing Device Drivers with Zadig](https://github.com/AlexanderMcLaughlin/TestRepo#replacing-device-drivers-with-zadig)
- [Arduino IDE Setup with LSI Board JSON File](https://github.com/AlexanderMcLaughlin/TestRepo#arduino-ide-setup-with-lsi-board-json-file)
  - [Install Board Files from github Repository](https://github.com/AlexanderMcLaughlin/TestRepo#install-board-files-from-github-repository)
  - [Setup "Flash SoftDevice" Option](https://github.com/AlexanderMcLaughlin/TestRepo#setup-flash-softdevice-option)
- [Troubleshooting](https://github.com/AlexanderMcLaughlin/TestRepo#troubleshooting)
- [Reset Default Drivers](https://github.com/AlexanderMcLaughlin/TestRepo#reset-default-drivers)

## Beginning Workflow Setup
Make sure that any SEGGER JLink software that you have on your machine currently is taken note of because this walkthrough will only be compatible with the version of SEGGER JLink installed by nRFGo Studio (v5.12f for the most recent version).
The software that will be used in this guide is as follows:
- [nRFgo Studio](https://www.nordicsemi.com/?sc_itemid=%7B23B6FAAE-0B1B-415A-B891-5B916E854AC4%7D)
  - SEGGER JLink (The version automatically installed by nRFGo Studio)
- [Arduino IDE](https://www.arduino.cc/en/Main/OldSoftwareReleases) (Not windows store version, direct download only, this walkthrough has only been tested with v1.8.5)
- [Zadig](https://zadig.akeo.ie/)

Make sure that you have access to the internet in order to complete the Arduino IDE setup, the files needed for the board will be downloaded directly from a github repository online.

### Things to Download
Most of the software necessary in this tutorial will be included in the provided software package, after downloading this you should have everything you'll need. Inside the package a python installation script is included which will begin the process for you.

### nRFgo Studio
[Install nRFgo Studio](https://www.nordicsemi.com/?sc_itemid=%7B23B6FAAE-0B1B-415A-B891-5B916E854AC4%7D), during this process it will start two separate installers: one for a specific version of JLink and the other for command line utilities which will not be used in this setup.

Select the last option for installation and wait for the installer to open the JLink and Command Line Utilities installers and change the options to your liking or stick with defaults (This guide will use defaults).

Upon completion close the installer and verify that the new JLink version 5.12f is under Program Files x86>>SEGGER and the nRFgo Studio directory is located under Program Files x86>>Nordic Semiconductor.

### Arduino IDE
[Install the Arduino IDE v1.8.5](https://www.arduino.cc/en/Main/OldSoftwareReleases), if you already have another version installed just start this installer and it will replace the version you have with 1.8.5.

Upon completion close the installer and open the Arduino IDE to verify that it opens without error. 

Make sure that in Windows Explorer the "show hidden files" option is selected: In Windows Explorer select View>>Options>>Change folder and search options>>Select the view tab>>Under advanced settings select "show hidden files, folders, and drives">>Select OK.

After this verify that under C:/Users/<UserName>/AppData/Local/ the Arduino15 directory exists, this directory is essential and verifies that it is not the Windows Store version.

### Zadig.exe
Download [Zadig](), verify that it works properly by opening it.

## Firmware Replacement and Driver Updating
The steps that follow should only be completed once all the above softwares have been properly installed and verified, Arduino IDE setup will follow after.

### Initial Steps
Plug in the LSI device connected via a ribbon cable to the development kit through a mini USB to any USB port on your Windows 10 machine. Wait for your computer to install initial JLink drivers for the device (this shouldn't take more than 30 seconds). Only upon completion should you move on to the next step.

### JLink Configurator Firmware Replacement
Under C:/Program Files x86/SEGGER/JLink_V512f/ run JLinkConfig.exe to start JLink Configurator.
Right click on your LSI device and select "Replace Firmware" Once this is completed the dialog box should close automatically.

### nRFgo Studio Firmware Deletion
**WARNING: This section can sometimes be _very_ tricky, please read through the entire thing before starting.**
Under C:/Program Files x86/Nordic Semiconductor/nRFgo Studio/ run nRFgoStudio.exe.
On the left hand side toolbox select "nRF5x Programming" and if prompted by a dialog asking you to update the firmware of a device, **select No**. It will prompt you several more times, but **continue selecting No** it will eventually give up and stop prompting you.

Once you have reached the page select the "Erase All" option if you were prompted by that dialog box in the previous step you will be prompted by it again, repeat the same steps and **select No until it has stopped prompting you.**
After it has stopped prompting you, you are finished with nRFgo, close nRFgo studio.

### Replacing Device Drivers with Zadig
Run zadig.exe and select the Options dropdown and click "List All Devices". Select J-Link (Interface 2) from the dropdown and change the driver that will replace its current one with "WinUSB (v6.1...". Click "Replace Driver" and wait for the process to finish. Once this is done you can verify that it was successful by selecting J-Link (Interface 2) from the dropdown and seeing that its driver is now "WinUSB (v6.1...".

## Arduino IDE Setup with LSI Board JSON File

### Install Board Files from github Repository
- Click File>>Preferences
- Under Additional Board Manager URLs paste this URL: 
`https://raw.githubusercontent.com/AlexanderMcLaughlin/TestRepo/master/package_nRF5_boards_index.json`
- Click OK
- Click the Tools dropdown menu and hover over 'Board: "Current Board"' and then select "Boards Manager..."
- Click the top bar where it says "__Filter your search...__" and type "LSI Board"
- Click on the first item and install version 0.0.1
- This should take about 10 minutes
- Once completed select the tools dropdown menu and set the following properties:
  - Board: LSI Board
  - SoftDevice: S132 (Not S132v6)
  - Low Frequency Clock: Crystal Oscillator
  - Port: <The current USB Port that your Development Kit/LSI Board Setup is plugged into (Should be COM#)>
  - Programmer: J-Link

### Setup "Flash SoftDevice" Option
- Download this [nRF5FlashSoftDevice.jar](https://github.com/sandeepmistry/arduino-nRF5/releases/download/tools/nRF5FlashSoftDevice.jar) file
- Navigate to C:/Documents/Arduino/tools/
- Add a new folder here /nRF5FlashSoftDevice and go inside
- Add a new folder here /tool and go inside
- Move nRF5FlashSoftDevice.jar to this location

Close and reopen the Arduino IDE to add the menu option to the IDE.

Select the tools dropdown and select "nRF5 Flash SoftDevice" from menu with your choice of example sketch. **This step will take a while, wait until you get the following message:** `Shutdown Command Invoked`

After this simply press the "Verify" and "Upload" options in the main GUI of the Arduino IDE. Again, **this step will take a while.** This will verify the sketch and upload it to the board. Upon successful completion the following message will be displayed: `Shutdown Command Invoked`

Several Errors have been accounted for in the troubleshooting section if the upload is unsuccessful.

You should now be able to program the board using the Arduino IDE, several examples are provided under File>>Examples>> and "Examples for LSI Board"



## Troubleshooting

<details>
  <summary>I accidentally clicked "yes" when nRFgo prompted me to update the firmware on the device. What do I do now?</summary>
  <p>Go <a href="https://github.com/AlexanderMcLaughlin/TestRepo#reset-default-drivers">here</a> to reset default drivers and follow the steps to restore the board drivers to their original settings. Then follow all steps underneath <a href="https://github.com/AlexanderMcLaughlin/TestRepo#firmware-replacement-and-driver-updating">Firmware Replacement and Driver Updating</a></p>
</details>
<details>
  <summary>I get the error "No valid JTAG interface found" when programming the softdevice. What do I do?</summary>
  <p>This likely means that one of the above steps has failed. Verify that under Device Manager in the Universal Serial Bus Devices you have J-Link (Interface 2), if it says BULK (Interface 2) or anything of the sort you need to <a href="https://github.com/AlexanderMcLaughlin/TestRepo#reset-default-drivers">reset to the default drivers</a> and repeat the steps underneath <a href="https://github.com/AlexanderMcLaughlin/TestRepo#firmware-replacement-and-driver-updating">Firmware Replacement and Driver Updating</a></p>
</details>
<details>
  <summary>Upon clicking "nRF5 Flash SoftDevice" I get no results on the LSI Board. What do I do?</summary>
  <p>Try pressing "Verify" and "Upload" in the main GUI, if you get an error from "Verify" there is a compilation error with the .ino file. Otherwise it should upload and begin working.</p>
</details>
<details>
  <summary>nRFgo keeps freezing on me when I click on "nRF5x programming". What do I do?</summary>
  <p>This usually happens when the wrong version of JLink Configurator is used. Be careful and make sure that you follow the steps in <a href="https://github.com/AlexanderMcLaughlin/TestRepo#jlink-configurator-firmware-replacement">JLink Configurator Firmware Replacement</a> using the JLink version installed automatically by nRFgo Studio and not any other.</p>
</details>

## Reset Default Drivers
This section should be used if you are having a lot of difficulty and need to start from scratch with a clean slate. This will remove all progress done (or undone if steps are done out of order or incorrectly). Only follow these steps if you want to start over completely.

- Open Device Manager
- Scroll down to Universal Serial Bus Devices
- Right click on device and click uninstall
- Check the box that says "Delete the driver software for this device" in the dialog box and click OK
- Unplug and plug back in the whole LSI Board setup with the Development kit and all from the USB port
- Wait for the default drivers to be installed
- At this point it should be a clean slate
- Continue [here](https://github.com/AlexanderMcLaughlin/TestRepo#firmware-replacement-and-driver-updating)
