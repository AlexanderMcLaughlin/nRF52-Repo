# Limbitless Solutions Inc. ISP1507, nrF52 Based Board Setup
This README will detail everything from start to finish to set up the LSI Board for programming via the Arduino IDE. It will have a troubleshooting section in case of any roadblocks.

Note: Any previous install of the sandeepmistry nRF52 library or this library should be uninstalled before beginning this guide.

## Table of Contents
- [Beginning Workflow Setup](https://github.com/AlexanderMcLaughlin/TestRepo#beginning-workflow-setup)
  - [Things to Download](https://github.com/AlexanderMcLaughlin/TestRepo#things-to-download)
    - [nRFgo Studio](https://github.com/AlexanderMcLaughlin/TestRepo#nrfgo-studio)
    - [Arduino IDE](https://github.com/AlexanderMcLaughlin/TestRepo#arduino-ide)
    - [Zadig.exe](https://github.com/AlexanderMcLaughlin/TestRepo#zadigexe)
    - [Segger Embedded Studio](https://github.com/AlexanderMcLaughlin/TestRepo#segger-embedded-studio)
    - [nrfutil.exe](https://github.com/AlexanderMcLaughlin/TestRepo#nrfutilexe)
- [Firmware Replacement and Driver Updating](https://github.com/AlexanderMcLaughlin/TestRepo#firmware-replacement-and-driver-updating)
  - [Initial Steps](https://github.com/AlexanderMcLaughlin/TestRepo#initial-steps)
  - [JLink Configurator Firmware Replacement](https://github.com/AlexanderMcLaughlin/TestRepo#jlink-configurator-firmware-replacement)
  - [Segger Embedded Studio Firmware Deletion](https://github.com/AlexanderMcLaughlin/TestRepo#segger-embedded-studio-firmware-deletion)
  - [Replacing Device Drivers with Zadig](https://github.com/AlexanderMcLaughlin/TestRepo#replacing-device-drivers-with-zadig)
- [Arduino IDE Setup with LSI Board JSON File](https://github.com/AlexanderMcLaughlin/TestRepo#arduino-ide-setup-with-lsi-board-json-file)
  - [Install Board Files from github Repository](https://github.com/AlexanderMcLaughlin/TestRepo#install-board-files-from-github-repository)
  - [Setup "Flash SoftDevice" Option](https://github.com/AlexanderMcLaughlin/TestRepo#setup-flash-softdevice-option)
- [Sending a DFU Package over OTA After Arduino IDE Development](https://github.com/AlexanderMcLaughlin/TestRepo#sending-a-dfu-package-over-ota-after-arduino-ide-development)
  - [Preparing the Board for the Bootloader](https://github.com/AlexanderMcLaughlin/TestRepo#preparing-the-board-for-the-bootloader)
  - [Preparing the Secure DFU Bootloader](https://github.com/AlexanderMcLaughlin/TestRepo#preparing-the-secure-dfu-bootloader)
  - [Creating the DFU Packet](https://github.com/AlexanderMcLaughlin/TestRepo#creating-the-dfu-packet)
- [Temporarily Using nRFConnect to Send OTA Updates](https://github.com/AlexanderMcLaughlin/TestRepo#temporarily-using-nrfconnect-to-send-ota-updates)

## Beginning Workflow Setup
Make sure that any SEGGER JLink software that you have on your machine currently is taken note of because this walkthrough will only be compatible with the version of SEGGER JLink installed by nRFGo Studio (v5.12f for the most recent version).
The software that will be used in this guide is as follows:
- [nRFgo Studio](https://www.nordicsemi.com/?sc_itemid=%7B23B6FAAE-0B1B-415A-B891-5B916E854AC4%7D)
  - SEGGER JLink (The version automatically installed by nRFGo Studio)
- [Arduino IDE](https://www.arduino.cc/en/Main/OldSoftwareReleases) (Not windows store version, direct download only, this walkthrough has only been tested with v1.8.5)
- [Zadig](https://zadig.akeo.ie/)
- [Segger Embedded Studio](https://www.segger.com/downloads/embedded-studio/EmbeddedStudio_ARM_Win_x64)
- [nrfutil.exe](https://github.com/NordicSemiconductor/pc-nrfutil/releases/download/v5.2.0/nrfutil.exe)

Make sure that you have access to the internet in order to complete the Arduino IDE setup, the files needed for the board will be downloaded directly from a github repository online.

## Things to Download

### nRFgo Studio
[Install nRFgo Studio](https://www.nordicsemi.com/?sc_itemid=%7B23B6FAAE-0B1B-415A-B891-5B916E854AC4%7D), during this process it will start two separate installers: one for a specific version of JLink and the other for command line utilities which will not be used in this setup.

Select the last option for installation and wait for the installer to open the JLink and Command Line Utilities installers and change the options to your liking or stick with defaults (This guide will use defaults).

Upon completion close the installer and verify that the new JLink version 5.12f is under Program Files x86>>SEGGER and the nRFgo Studio directory is located under Program Files x86>>Nordic Semiconductor.

### Arduino IDE
[Install the Arduino IDE v1.8.5](https://www.arduino.cc/en/Main/OldSoftwareReleases), if you already have another version installed just start this installer and it will replace the version you have with 1.8.5.

Upon completion close the installer and open the Arduino IDE to verify that it opens without error. 

Make sure that in Windows Explorer the "show hidden files" option is selected: In Windows Explorer select View>>Options>>Change folder and search options>>Select the view tab>>Under advanced settings select "show hidden files, folders, and drives">>Select OK.

After this verify that under C:/Users/UserName/AppData/Local/ the Arduino15 directory exists, this directory is essential and verifies that it is not the Windows Store version.

### Zadig.exe
Download [Zadig](https://zadig.akeo.ie/), verify that it works properly by opening it.

### Segger Embedded Studio
[Install Segger Embedded Studio](https://www.segger.com/downloads/embedded-studio/EmbeddedStudio_ARM_Win_x64), simply choose the defaults for installation and follow the instructions until it finishes.

### nrfutil.exe
[Install nrfutil.exe](https://github.com/NordicSemiconductor/pc-nrfutil/releases/download/v5.2.0/nrfutil.exe)

## Firmware Replacement and Driver Updating
The steps that follow should only be completed once all the above softwares have been properly installed and verified, Arduino IDE setup will follow after.

### Initial Steps
Plug in the LSI device connected via a ribbon cable to the development kit through a mini USB to any USB port on your Windows 10 machine. Wait for your computer to install initial JLink drivers for the device (this shouldn't take more than 30 seconds, but it could take longer). Only upon completion should you move on to the next step.

Open Device Manager on Windows 10 and verify that the JLink device is listed under "Universal Serial Bus Controllers" and not "Universal Serial Bus Devices" only at this step. If it is under controllers continue, otherwise, right click on the device and select "Uninstall" and then select "Delete Drivers" in the resulting dialog box.

### JLink Configurator Firmware Replacement
Under C:/Program Files x86/SEGGER/JLink_V512f/ run JLinkConfig.exe to start JLink Configurator.
Right click on your LSI device and select "Replace Firmware" Once this is completed the dialog box should close automatically. Then close the application.

### Segger Embedded Studio Firmware Deletion
Open Segger Embedded Studio
***WHEN PROMPTED BY SEGGER EMBEDDED STUDIO IN THE NEXT STEP PLEASE SELECT NO***
From the "Target" top menu dropdown select "Connect J-Link" then again under the "Target" top menu dropdown select "Erase All" Note: this will remove any custom bootloaders and applications from the board.
Close Segger Embedded Studio.

### Replacing Device Drivers with Zadig
Run zadig.exe and select the Options dropdown and click "List All Devices". Select J-Link (Interface 2) from the dropdown and change the driver that will replace its current one with "WinUSB (v6.1...". Click "Replace Driver" and wait for the process to finish. Once this is done you can verify that it was successful by selecting J-Link (Interface 2) from the dropdown and seeing that its driver is now "WinUSB (v6.1...".

This will leave the board in a type of Arduino IDE development mode where typical J-Link operations are suspended because we have replaced the driver using Zadig. This can be reversed by going to Device Manager on Windows 10ge

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
  - SoftDevice: S132
  - Low Frequency Clock: Crystal Oscillator
  - Port: <The current USB Port that your Development Kit/LSI Board Setup is plugged into (Should be COM#)>
  - Programmer: J-Link
  
Your board files should be set up, but you need to install the Flash SoftDevice option in order for the compilation process to be successful.

Note: We have been experiencing some problems with the compatibility of the update of softdevice from v2.0.1 to v6.1.1 (in order to add OTA support to this project with the SDK's version of the bootloader) and accessing the functions of the SoftDevice. In order to make a DFU packet to send OTA firmware updates the following library needs to be used, however, it is incompatible with the BLEPeripheral library due to its SoftDevice version, a fix for this is currently in progress, but a new BLE library may need to be used instead: `https://raw.githubusercontent.com/AlexanderMcLaughlin/TestRepo/master/package_nRF5_new_SD_index.json`

### Setup "Flash SoftDevice" Option
- Download this [nRF5FlashSoftDevice.jar](https://github.com/sandeepmistry/arduino-nRF5/releases/download/tools/nRF5FlashSoftDevice.jar) file
- Navigate to C:/Documents/Arduino/tools/
- Add a new folder here /nRF5FlashSoftDevice and go inside
- Add a new folder here /tool and go inside
- Move nRF5FlashSoftDevice.jar to this location

Close and reopen the Arduino IDE to add the menu option to the IDE.

Select the tools dropdown and select "nRF5 Flash SoftDevice" from menu with your choice of example sketch. **This step will take a while, wait until you get the following message:** `Shutdown Command Invoked`

After this simply press the "Verify" and "Upload" options in the main GUI of the Arduino IDE. Again, **this step will take a while.** This will verify the sketch and upload it to the board. Upon successful completion the following message will be displayed: `Shutdown Command Invoked`

You should now be able to program the board using the Arduino IDE, several examples are provided under File>>Examples>> and "Examples for LSI Board"

This is simply for debugging and testing purposes using the Arduino IDE, once this step is completed you can begin to create a DFU packet and prepare the board for sending.

## Sending a DFU Package over OTA After Arduino IDE Development
Now that you've finished developing in the Arduino IDE you're ready to push an update! 

### Preparing the board for the Bootloader
Up until now you've been using the Arduino IDE to develop on the LSI board, now it is time to get it prepped for Segger Embedded Studio. The following steps will get your board where you need it to be:

 - Go into Device Manager (Right click on the Windows button in the bottom left and select Device Manager)
 - Right Click on the device under "Universal Serial Bus Devices" and find "J-Link" (If "J-Link" is located under "Universal Serial Bus controllers" then you should be able to use J-Link with your board already, this is unlikely, though, if you were just developing in the Arduino IDE) and select "uninstall" and check "delete drivers" when a dialog box pops up.
 - Unplug and plug back in the USB for the development board connected to the LSI board then wait for the J-Link drivers to install
 - Open "J-Link Configurator" (JLinkConfig.exe) under the JLink version installed by nRFGo Studio (Likely v5.12) under "Program Files x86\SEGGER\" (Note: If nothing shows up in J-Link Configurator then go into Device Manager in Windows 10 and right click on the J-Link Driver under Universal Serial Bus Devices and click "uninstall" and select the "delete driver" option when prompted then unplug and plug back in the device and try opening J-Link Configurator again)
 - Right click on the device and select "Replace Firmware", close J-Link Configurator, once this is completed you can move on to the next steps.

### Preparing the Secure DFU Bootloader 
To prepare the board to accept DFU packets you must add the Secure DFU Bootloader using Segger Embedded Studio that you downloaded earlier.

First download the following SDK package `https://drive.google.com/file/d/1rlGMto_WFFcX71MbsDeLX7PgYsDqlvYn/view` then unzip it to an easy-to-navigate-to directory.

Generate your own key using nrfutil by doing the following:
 - Navigate to the directory you saved nrfutil.exe to
 - execute this command to generate the private key: `nrfutil.exe keys generate "<Location of key>\private.key` Note: This key must be named private.key
 - execute this command to generate the public key: `nrfutil.exe keys display --key pk --format code --out_file "<Location of public key>\dfu_public_key.c" "<Location of private key>\private.key"` Note: This key must be named dfu_public_key.c

Note: The location of your public and private keys can vary, just make sure you keep track of them both

After you have both your public and private keys saved, open the SDK package that you unzipped to an easy-to-navigate-to directory and once inside navigate to `<Location of SDK>\nRF5_SDK_15.3.0_59ac345_bare_minimum\nRF5_SDK_15.3.0_59ac345_wip\examples\dfu\` replace this dfu_public_key.c with the dfu_public_key.c that you just generated.

Then again inside of `<Location of SDK>\nRF5_SDK_15.3.0_59ac345_bare_minimum\nRF5_SDK_15.3.0_59ac345_wip\examples\dfu\secure_bootloader\pca10040_ble\ses\` and replace this dfu_public_key.c with the dfu_public_key.c that you just generated.

Now finally you should open up the `secure_bootloader_ble_s132_pca10040.emproject` file located inside `<Location of SDK>\nRF5_SDK_15.3.0_59ac345_bare_minimum\nRF5_SDK_15.3.0_59ac345_wip\examples\dfu\secure_bootloader\pca10040_ble\ses\` using segger embedded studio, if this type of file is not already opened by SES by default.

**Before doing this next step please note that it is human instinct to press yes on a dialog box when it pops up, but please suppress that instinct for the next step and ANY TIME FROM HERE ON OUT you are told by Segger Embedded studio that there is a firmware update for your device PLEASE CLICK NO**

After this you should click "Target" on the top menu and then click "Connect J-Link" **This is your time to shine, when the dialog box pops up, read the dialog box and SELECT NO.**

After you've done this successfully, Click on "Build" on the top menu and then select "Build secure_bootloader_ble_s132_pca10040" this should take about a minute and then everything should light up green on the bottom.

Now simply select "Target" from the top menu and then click "Download secure_bootloader_ble_s132_pca10040" and just like that you've got the DFU secure bootloader, the lights on your board should now be lighting up in magenta to signify that the board is in DFU mode!

The board will remain in DFU mode until initially sent a DFU packet, after it has been given one it will automatically begin executing that application. After putting an application on the board in order to put this board into DFU mode to update its firmware using OTA DFU you should hold the button on the board down and power cycle the board (unplug the board then plug back in or other method) while keeping the button pressed down, the button will stay a solid magenta to indicate it is in DFU mode, it will turn teal to indicate that it has been connected to, and then it will execute an application when it recieves a valid DFU packet. 

This DFU packet will be generated in the next step and is the most important part of the integration process.

In order to get the DFU packet onto the board follow the app documentation.

### Creating the DFU Packet
In order to get the hex file that we will be turning into a DFU packet simply go to the Arduino IDE and press the Verify button in the top lefthand corner (Verify your settings match those described in the Arduino IDE setup section). Let it compile everything and wait for the line that tells you how much program storage space has been used. Look for the line above that which should look similar to:
`"C:\Users\<usrname>\AppData\Local\Arduino15\packages\sandeepmistry\tools\gcc-arm-none-eabi\5_2-2015q4/bin/arm-none-eabi-objcopy" -O ihex  "C:\Users\<usrname>\AppData\Local\Temp\arduino_build_857899/<Arduino .ino file name>.elf" "C:\Users\<usrname>\AppData\Local\Temp\arduino_build_857899/<Arduino .ino file name>.hex"`

Navigate to ` C:\Users\<usrname>\AppData\Local\Temp\arduino_build_857899/<Arduino .ino file name>.hex ` and copy this file to a different directory that's easier to find. (This step is not really necessary but it makes everything a lot easier to write in command prompt.

Next navigate to the directory that you saved nrfutil.exe earlier in this tutorial. Then to generate the package execute the following command:
`nrfutil.exe pkg generate --application <Location and name of the hex file> --application-version-string "0.0.1" --hw-version 52 --sd-req 0xB7 --key-file <Location and name of private.key> <Location and name of zip file to create>`

Note: the key-file should correspond to the dfu_public_key.c that you generated earlier inside of the SDK files used with Segger Embedded Studio to add the DFU Secure Bootloader talked about above.

Once you have executed this command your DFU package should be completed!

Simply upload that zip packet to the app, put the board in DFU mode, and upload it. This will change the application on the board, and also keep the DFU Secure Bootloader.

## Temporarily Using nRFConnect to Send OTA Updates
Download the nRFConnect app to your phone, upload the DFU zip packet you just generated to your phone, if using iPhone email is the easiest method. Put the board in DFU mode (Hold down the button while power cycling the board) then connect to "DFUTarg" and click "Connect", select the DFU button on the right-hand side, Select your zip packet (if not listed, add using the plus button in the top right) and press the play button on the bottom. After it finishes uploading, the board will automatically exit DFU mode and begin executing the Application. You have just completed an OTA device firmware update!
