# PSOC&trade; 6 MCU: Motion sensor interfacing using I2C

This example demonstrates how to interface PSOC&trade; 6 MCU with a [BMI160 motion sensor](https://www.bosch-sensortec.com/products/motion-sensors/imus/bmi160.html) or [BMI270 motion sensor](https://www.bosch-sensortec.com/products/motion-sensors/imus/bmi270) over an I2C interface within a FreeRTOS task using the [BMI160 motion sensor library](https://github.com/Infineon/sensor-motion-bmi160) or [BMI270 motion sensor library](https://github.com/Infineon/sensor-motion-bmi270). This example reads the raw motion data and estimates the orientation of the board.

[View this README on GitHub.](https://github.com/Infineon/mtb-example-psoc6-motion-sensor-freertos)

[Provide feedback on this code example.](https://cypress.co1.qualtrics.com/jfe/form/SV_1NTns53sK2yiljn?Q_EED=eyJVbmlxdWUgRG9jIElkIjoiQ0UyMzE4NjQiLCJTcGVjIE51bWJlciI6IjAwMi0zMTg2NCIsIkRvYyBUaXRsZSI6IlBTT0MmdHJhZGU7IDYgTUNVOiBNb3Rpb24gc2Vuc29yIGludGVyZmFjaW5nIHVzaW5nIEkyQyIsInJpZCI6InNtcngiLCJEb2MgdmVyc2lvbiI6IjIuMi4wIiwiRG9jIExhbmd1YWdlIjoiRW5nbGlzaCIsIkRvYyBEaXZpc2lvbiI6Ik1DRCIsIkRvYyBCVSI6IklDVyIsIkRvYyBGYW1pbHkiOiJQU09DIn0=)


## Requirements

- [ModusToolbox&trade;](https://www.infineon.com/modustoolbox) v3.1 or later (tested with v3.4)
- Board support package (BSP) minimum required version: 4.0.0
- Programming language: C
- Associated parts: All [PSOC&trade; 6 MCU](https://www.infineon.com/cms/en/product/microcontroller/32-bit-psoc-arm-cortex-microcontroller/psoc-6-32-bit-arm-cortex-m4-mcu) parts


## Supported toolchains (make variable 'TOOLCHAIN')

- GNU Arm&reg; Embedded Compiler v11.3.1 (`GCC_ARM`) – Default value of `TOOLCHAIN`
- Arm&reg; Compiler v6.22 (`ARM`)
- IAR C/C++ Compiler v9.50.2 (`IAR`)

## Supported kits (make variable 'TARGET')

- [PSOC&trade; 62S3 Wi-Fi Bluetooth&reg; Prototyping Kit](https://www.infineon.com/CY8CPROTO-062S3-4343W) (`CY8CPROTO-062S3-4343W`) – Default value of `TARGET`
- [PSOC&trade; 6 Bluetooth&reg; LE Pioneer Kit](https://www.infineon.com/CY8CKIT-062-BLE) (`CY8CKIT-062-BLE`)
- [PSOC&trade; 6 Wi-Fi Bluetooth&reg; Prototyping Kit](https://www.infineon.com/CY8CPROTO-062-4343W) (`CY8CPROTO-062-4343W`)
- [PSOC&trade; 6 Wi-Fi Bluetooth&reg; Pioneer Kit](https://www.infineon.com/CY8CKIT-062-WIFI-BT) (`CY8CKIT-062-WIFI-BT`)
- [PSOC&trade; 6 Bluetooth&reg; LE Prototyping Kit](https://www.infineon.com/CY8CPROTO-063-BLE) (`CY8CPROTO-063-BLE`)
- [PSOC&trade; 62S1 Wi-Fi Bluetooth&reg; Pioneer Kit](https://www.infineon.com/CYW9P62S1-43438EVB-01) (`CYW9P62S1-43438EVB-01`)
- [PSOC&trade; 62S1 Wi-Fi Bluetooth&reg; Pioneer Kit](https://www.infineon.com/CYW9P62S1-43012EVB-01) (`CYW9P62S1-43012EVB-01`)
- [PSOC&trade; 62S2 Wi-Fi Bluetooth&reg; Pioneer Kit](https://www.infineon.com/CY8CKIT-062S2-43012) (`CY8CKIT-062S2-43012`)
- [PSOC&trade; 62S4 Pioneer Kit](https://www.infineon.com/CY8CKIT-062S4) (`CY8CKIT-062S4`)
- [PSOC&trade; 64 "Secure Boot" Wi-Fi Bluetooth&reg; Pioneer Kit](https://www.infineon.com/CY8CKIT-064B0S2-4343W) (`CY8CKIT-064B0S2-4343W`)
- [PSOC&trade; 62S2 Evaluation Kit](https://www.infineon.com/CY8CEVAL-062S2) (`CY8CEVAL-062S2`, `CY8CEVAL-062S2-LAI-4373M2`)
- [PSOC&trade; 6 AI Evaluation Kit](https://www.infineon.com/CY8CKIT-062S2-AI) (`CY8CKIT-062S2-AI`)


## Hardware setup


### Interfacing details

There are primarily two ways of interfacing the motion sensors (BMI160/BMI270) with the PSOC&trade; 6 MCU in this code example:

- **Shields:** [CY8CKIT-028-EPD](https://www.infineon.com/cms/en/product/evaluation-boards/cy8ckit-028-epd) or [CY8CKIT-028-TFT](https://www.infineon.com/cms/en/product/evaluation-boards/cy8ckit-028-tft) or [SHIELD_XENSIV_A](https://www.infineon.com/SHIELD_XENSIV_A) *(on supported kits only):* You can plug in the shield to the PSOC&trade; 6 MCU kit and configure the `INTERFACE_USED` and `IMU_INTERRUPT_CHANNEL` macros in the *motion_task.h* file as described in the [Operation](#operation) section

   > **Note:** This method of interfacing is not supported by all the kits mentioned in the [Supported kits](#supported-kits-make-variable-target) section. See **Table 1** for supported interfaces for each supported kit and the corresponding interrupt pin interfacing details

- **Direct connection:** Interface the BMI160 or BMI270 motion sensor over the I2C interface per the schematic diagram shown in **Figure 1**

   - Connect the *I2C_SDA* and *I2C_SCL* pins to the kit's I2C pins. See the kit user guide for more details on the kit's I2C configuration

   - Motion sensors provide two interrupt channels (*INT1* and *INT2*) to which various interrupt events can be assigned. Connect either *INT1* or *INT2* pin with a PSOC&trade; 6 MCU GPIO pin - define the `INTERFACE_USED` macro as `CUSTOM_INTERFACE`, and configure the `IMU_INTERRUPT_CHANNEL` and `CUSTOM_INTERRUPT_PIN` macros as described in the [Operation](#operation) section

   > **Note:** Motion sensors have selectable I2C slave addresses. See the [Design and implementation](#design-and-implementation) section to ensure the I2C slave address before configuring the sensor

   **Figure 1. BMI160 or BMI270 custom interfacing diagram**

   ![](images/bmi160-custom-interfacing.png)

**Table 1** lists the supported interfaces for each supported kit and the corresponding interrupt pin interfacing details.

> **Note:** For the functionality of this code example, you need to use only one of the BMI160/BMI270 motion sensor's interrupt pins (either INT1 or INT2, not both).

- In case of shields (CY8CKIT-028-EPD, CY8CKIT-028-TFT, and SHIELD_XENSIV_A), the PSOC&trade; 6 GPIO pins that connect to the motion sensor's INT1 and INT2 pins are also shown in **Table 1**. The code example automatically configures these GPIO pins when you define the `INTERFACE_USED` macro and the details in the table are for your information only

- In case of direct interfacing (`CUSTOM_INTERFACE`), you should choose an appropriate GPIO pin to interface INT1 or INT2 (mentioned as *User-defined* in the following table)

**Table 1. BMI160/BMI270 and PSOC&trade; 6 MCU interfacing**

 Development kit | Supported interfaces (`INTERFACE_USED` macro) | PSOC&trade; 6 MCU GPIO pin interfaced with IMU's INT1 pin | PSOC&trade; 6 MCU GPIO pin interfaced with IMU's INT2 pin
 :-------------- | :--------------------- | :-------------------------- | :------------------------
 CY8CKIT-062-BLE | CY8CKIT_028_EPD <br> CY8CKIT_028_TFT <br> CUSTOM_INTERFACE | P13[1] <br> P10[2] <br> User-defined | P13[0] <br> P10[3] <br> User-defined
 CY8CPROTO-062-4343W | CUSTOM_INTERFACE | User-defined | User-defined
 CY8CKIT-062-WiFi-BT | CY8CKIT_028_EPD <br> CY8CKIT_028_TFT <br> CUSTOM_INTERFACE | P13[1] <br> P10[2] <br> User-defined | P13[0] <br> P10[3] <br> User-defined
 CY8CPROTO-063-BLE | CUSTOM_INTERFACE | User-defined | User-defined
 CY8CKIT-062S2-43012 | CY8CKIT_028_EPD <br> CY8CKIT_028_TFT <br> SHIELD_XENSIV_A <br> CUSTOM_INTERFACE | P7[6] <br> P10[2] <br> P5[7] <br> User-defined | P7[5] <br> P10[3] <br> P5[7] <br> User-defined
 CYW9P62S1-43438EVB-01 | CY8CKIT_028_EPD <br> CY8CKIT_028_TFT <br> CUSTOM_INTERFACE | P7[4] <br> P10[2] <br> User-defined | P7[5] <br> P10[3] <br> User-defined
 CYW9P62S1-43012EVB-01 | CY8CKIT_028_TFT <br> CUSTOM_INTERFACE | P6[4] <br> User-defined | P6[5] <br> User-defined
 CY8CPROTO-062S3-4343W | CUSTOM_INTERFACE | User-defined | User-defined
 CY8CKIT-064B0S2-4343W | CY8CKIT_028_EPD <br> CY8CKIT_028_TFT <br> CUSTOM_INTERFACE | P7[6] <br> P10[2] <br> User-defined | P7[5] <br> P10[3] <br> User-defined
 CY8CEVAL-062S2 <br> CY8CEVAL-062S2-LAI-4373M2 | CY8CKIT_028_EPD <br> CY8CKIT_028_TFT <br> CUSTOM_INTERFACE | P7[6] <br> P10[2] <br> User-defined | P0[5] <br> P10[3] <br> User-defined
 CY8CKIT-062S2-AI | CY8CKIT_062S2_AI | P1[5] | P0[4]

<br>

This example uses the board's default configuration. See the kit user guide to ensure that the board is configured correctly.

> **Notes:**

  - PSOC&trade; 62S1 Wi-Fi Bluetooth&reg; Pioneer Kit (CYW9P62S1-43012EVB-01) cannot be used with the CY8CKIT-028-EPD shield to evaluate the complete functionality of this example. This is because this kit does not have any GPIO connections to the D8 and D9 pins compatible with Arduino to which the motion sensor's interrupt pins interface on the CY8CKIT-028-EPD shield

    > **Note:** The PSOC&trade; 6 Bluetooth&reg; LE Pioneer Kit (CY8CKIT-062-BLE) and the PSOC&trade; 6 Wi-Fi Bluetooth&reg; Pioneer Kit (CY8CKIT-062-WIFI-BT) ship with KitProg2 installed. ModusToolbox&trade; requires KitProg3. Before using this code example, make sure that the board is upgraded to KitProg3. The tool and instructions are available in the [Firmware Loader](https://github.com/Infineon/Firmware-loader) GitHub repository. If you do not upgrade, you will see an error like "unable to find CMSIS-DAP device" or "KitProg firmware is out of date"

   - SHIELD_XENSIV_A can only be interfaced with the CY8CKIT-062S2-43012 kit in this version of the code example. Turn on the DIP switch SW2 towards SW2.11 and SW2.12 of the SHIELD_XENSIV_A to enable the BMI270 interrupts and turn off the remaining switches of SW2 to disable other sensor interrupts

   **Figure 2. SHIELD_XENSIV_A SW2 configuration to enable the BMI270 interrupts**

   <img src="images/sw2.png">


## Software setup

See the [ModusToolbox&trade; tools package installation guide](https://www.infineon.com/ModusToolboxInstallguide) for information about installing and configuring the tools package.

Install a terminal emulator if you do not have one. Instructions in this document use [Tera Term](https://teratermproject.github.io/index-en.html).

This example requires no additional software or tools.


## Using the code example


### Create the project

The ModusToolbox&trade; tools package provides the Project Creator as both a GUI tool and a command line tool.

<details><summary><b>Use Project Creator GUI</b></summary>

1. Open the Project Creator GUI tool

   There are several ways to do this, including launching it from the dashboard or from inside the Eclipse IDE. For more details, see the [Project Creator user guide](https://www.infineon.com/ModusToolboxProjectCreator) (locally available at *{ModusToolbox&trade; install directory}/tools_{version}/project-creator/docs/project-creator.pdf*)

2. On the **Choose Board Support Package (BSP)** page, select a kit supported by this code example. See [Supported kits](#supported-kits-make-variable-target)

   > **Note:** To use this code example for a kit not listed here, you may need to update the source files. If the kit does not have the required resources, the application may not work

3. On the **Select Application** page:

   a. Select the **Applications(s) Root Path** and the **Target IDE**

      > **Note:** Depending on how you open the Project Creator tool, these fields may be pre-selected for you

   b. Select this code example from the list by enabling its check box

      > **Note:** You can narrow the list of displayed examples by typing in the filter box

   c. (Optional) Change the suggested **New Application Name** and **New BSP Name**

   d. Click **Create** to complete the application creation process

</details>


<details><summary><b>Use Project Creator CLI</b></summary>

The 'project-creator-cli' tool can be used to create applications from a CLI terminal or from within batch files or shell scripts. This tool is available in the *{ModusToolbox&trade; install directory}/tools_{version}/project-creator/* directory.

Use a CLI terminal to invoke the 'project-creator-cli' tool. On Windows, use the command-line 'modus-shell' program provided in the ModusToolbox&trade; installation instead of a standard Windows command-line application. This shell provides access to all ModusToolbox&trade; tools. You can access it by typing "modus-shell" in the search box in the Windows menu. In Linux and macOS, you can use any terminal application.

The following example clones the "[mtb-example-psoc6-motion-sensor-freertos](https://github.com/Infineon/mtb-example-psoc6-motion-sensor-freertos)" application with the desired name "Motion_sensor" configured for the *CY8CPROTO-062S3-4343W* BSP into the specified working directory, *C:/mtb_projects*:

   ```
   project-creator-cli --board-id CY8CPROTO-062S3-4343W --app-id mtb-example-psoc6-motion-sensor-freertos --user-app-name Motion_sensor --target-dir "C:/mtb_projects"
   ```

The 'project-creator-cli' tool has the following arguments:

Argument | Description | Required/optional
---------|-------------|-----------
`--board-id` | Defined in the <id> field of the [BSP](https://github.com/Infineon?q=bsp-manifest&type=&language=&sort=) manifest | Required
`--app-id`   | Defined in the <id> field of the [CE](https://github.com/Infineon?q=ce-manifest&type=&language=&sort=) manifest | Required
`--target-dir`| Specify the directory in which the application is to be created if you prefer not to use the default current working directory | Optional
`--user-app-name`| Specify the name of the application if you prefer to have a name other than the example's default name | Optional

<br>

> **Note:** The project-creator-cli tool uses the `git clone` and `make getlibs` commands to fetch the repository and import the required libraries. For details, see the "Project creator tools" section of the [ModusToolbox&trade; tools package user guide](https://www.infineon.com/ModusToolboxUserGuide) (locally available at {ModusToolbox&trade; install directory}/docs_{version}/mtb_user_guide.pdf).

</details>


### Open the project

After the project has been created, you can open it in your preferred development environment.


<details><summary><b>Eclipse IDE</b></summary>

If you opened the Project Creator tool from the included Eclipse IDE, the project will open in Eclipse automatically.

For more details, see the [Eclipse IDE for ModusToolbox&trade; user guide](https://www.infineon.com/MTBEclipseIDEUserGuide) (locally available at *{ModusToolbox&trade; install directory}/docs_{version}/mt_ide_user_guide.pdf*).

</details>


<details><summary><b>Visual Studio (VS) Code</b></summary>

Launch VS Code manually, and then open the generated *{project-name}.code-workspace* file located in the project directory.

For more details, see the [Visual Studio Code for ModusToolbox&trade; user guide](https://www.infineon.com/MTBVSCodeUserGuide) (locally available at *{ModusToolbox&trade; install directory}/docs_{version}/mt_vscode_user_guide.pdf*).

</details>


<details><summary><b>Arm&reg; Keil&reg; µVision&reg;</b></summary>

Double-click the generated *{project-name}.cprj* file to launch the Keil&reg; µVision&reg; IDE.

For more details, see the [Arm&reg; Keil&reg; µVision&reg; for ModusToolbox&trade; user guide](https://www.infineon.com/MTBuVisionUserGuide) (locally available at *{ModusToolbox&trade; install directory}/docs_{version}/mt_uvision_user_guide.pdf*).

</details>


<details><summary><b>IAR Embedded Workbench</b></summary>

Open IAR Embedded Workbench manually, and create a new project. Then select the generated *{project-name}.ipcf* file located in the project directory.

For more details, see the [IAR Embedded Workbench for ModusToolbox&trade; user guide](https://www.infineon.com/MTBIARUserGuide) (locally available at *{ModusToolbox&trade; install directory}/docs_{version}/mt_iar_user_guide.pdf*).

</details>


<details><summary><b>Command line</b></summary>

If you prefer to use the CLI, open the appropriate terminal, and navigate to the project directory. On Windows, use the command-line 'modus-shell' program; on Linux and macOS, you can use any terminal application. From there, you can run various `make` commands.

For more details, see the [ModusToolbox&trade; tools package user guide](https://www.infineon.com/ModusToolboxUserGuide) (locally available at *{ModusToolbox&trade; install directory}/docs_{version}/mtb_user_guide.pdf*).

</details>


## Operation

If using a PSOC&trade; 64 "Secure" MCU kit (like CY8CKIT-064B0S2-4343W), the PSOC&trade; 64 device must be provisioned with keys and policies before being programmed. Follow the instructions in the ["Secure Boot" SDK user guide](https://www.infineon.com/dgdlac/Infineon-PSoC_64_Secure_MCU_Secure_Boot_SDK_User_Guide-Software-v07_00-EN.pdf?fileId=8ac78c8c7d0d8da4017d0f8c361a7666) to provision the device. If the kit is already provisioned, copy-paste the keys and policy folder to the application folder.

1. Interface the motion sensor (BMI160/BMI270) with the kit. See the [Hardware setup](#hardware-setup) section for the interfacing details

2. Connect the board to your PC using the provided USB cable through the KitProg3 USB connector

3. Open a terminal program and select the KitProg3 COM port. Set the serial port parameters to 8N1 and 115200 baud

4. Specify the motion sensor configuration details in the *motion_task.h* file as follows:

   1. Modify the `INTERFACE_USED` macro based on how you interface the motion sensor. To know the interfaces supported with your kit, see **Table 1**. For the shields, define the macro as `CY8CKIT_028_EPD`, or `CY8CKIT_028_TFT`, `SHIELD_XENSIV_A`, or `CY8CKIT_062S2_AI`. If you connect the sensor directly with the PSOC&trade; 6 MCU, define the macro as `CUSTOM_INTERFACE`

      > **Note:** If you use a kit that is not a pioneer kit (pioneer kits have headers compatible with Arduino), choose `CUSTOM_INTERFACE` for this macro

   2. Specify the interrupt channel (1 or 2) of the motion sensor that you want to use in this example using the `IMU_INTERRUPT_CHANNEL` macro. By default, this macro is set to '1', which corresponds to the INT1 pin

   3. If you use a custom interface (`INTERFACE_USED` defined as `CUSTOM_INTERFACE`), specify the GPIO pin of PSOC&trade; 6 MCU that interfaces with the BMI160/270 motion sensor's interrupt pin (INT1 when `IMU_INTERRUPT_CHANNEL` is 1; INT2 when the macro is 2) by using the `CUSTOM_INTERRUPT_PIN` macro

      Example:
      ```
      #define CUSTOM_INTERRUPT_PIN      (P10_0)
      ```

5. Program the board using one of the following:

   <details><summary><b>Using Eclipse IDE</b></summary>

      1. Select the application project in the Project Explorer

      2. In the **Quick Panel**, scroll down, and click **\<Application Name> Program (KitProg3_MiniProg4)**
   </details>


   <details><summary><b>In other IDEs</b></summary>

   Follow the instructions in your preferred IDE.

   </details>


   <details><summary><b>Using CLI</b></summary>

     From the terminal, execute the `make program` command to build and program the application using the default toolchain to the default target. The default toolchain is specified in the application's Makefile but you can override this value manually:
      ```
      make program TOOLCHAIN=<toolchain>
      ```

      Example:
      ```
      make program TOOLCHAIN=GCC_ARM
      ```
   </details>

6. After programming, the application starts automatically. Confirm that the terminal application displays the code example title and the initial orientation.

   **Figure 3. Terminal showing the initial orientation**

   ![](images/terminal-output.png)

   > **Note:** If the terminal displays an error message, check the connection of the motion sensor or EPD/TFT/SHIELD_XENSIV_A shield with the kit.

   The accelerometer sensor data is used to estimate the board’s spatial orientation. The terminal application display shows one of the six orientation states: *DISP_UP*, *DISP_DOWN*, *TOP_EDGE*, *BOTTOM_EDGE*, *LEFT_EDGE*, and *RIGHT_EDGE*. Side views of the pioneer kit with the EPD shield for each orientation state are shown as follows:

   **Figure 4. Orientation states**

   ![](images/orientation-states.png)

7. Change the orientation of the motion sensor and confirm that the orientation is updated on the same line in the terminal application as illustrated as follows:

   **Figure 5. Terminal output for orientation states**

   ![](images/updated-terminal-output.png)


## Debugging

You can debug the example to step through the code.


<details><summary><b>In Eclipse IDE</b></summary>

Use the **\<Application Name> Debug (KitProg3_MiniProg4)** configuration in the **Quick Panel**. For details, see the "Program and debug" section in the [Eclipse IDE for ModusToolbox&trade; user guide](https://www.infineon.com/MTBEclipseIDEUserGuide).

> **Note:** **(Only while debugging)** On the CM4 CPU, some code in `main()` may execute before the debugger halts at the beginning of `main()`. This means that some code executes twice – once before the debugger stops execution, and again after the debugger resets the program counter to the beginning of `main()`. See [PSOC&trade; 6 MCU: Code in main() executes before the debugger halts at the first line of main()](https://community.infineon.com/docs/DOC-21143) to learn about this and for the workaround.

</details>


<details><summary><b>In other IDEs</b></summary>

Follow the instructions in your preferred IDE.

</details>


## Design and implementation


### BMI160 sensor

[BMI160 motion sensor](https://www.bosch-sensortec.com/products/motion-sensors/imus/bmi160.html) is a low-power inertial measurement unit (IMU) providing 3-axis acceleration and 3-axis gyroscopic measurements. It is present on both the E-INK display shield (CY8CKIT-028-EPD) and the TFT display shield (CY8CKIT-028-TFT). PSOC&trade; 6 MCU interfaces with this sensor and reads the accelerometer data from which the orientation is computed and displayed on the terminal application.

The BMI160 motion sensor is interfaced with PSOC&trade; 6 MCU using an I2C interface and up to two interrupt pins. BMI160 has a hardware-selectable I2C slave address, depending on the logic driven on the SDO pin. On the EPD and TFT shields, the SDO pin is pulled to GND, which selects the slave address 0b1101000 (0x68).

BMI160 provides two interrupt channels (INT1 and INT2) to which various interrupt events can be assigned. In this example, the orientation interrupt is assigned to the interrupt pin that is configured using the `IMU_INTERRUPT_CHANNEL` macro. See the [BMI160 datasheet](https://www.bosch-sensortec.com/products/motion-sensors/imus/bmi160.html) for details on interrupt outputs.

The BMI160 motion sensor's interrupt output (INT1 or INT2 based on the `IMU_INTERRUPT_CHANNEL` macro) is configured to provide a rising-edge signal with a pulse width of 5 ms. The motion sensor is configured to provide an interrupt when the orientation is changed. Raw accelerometer data is read and processed on the orientation interrupt to compute the orientation.


### BMI270 sensor

[BMI270 motion sensor](https://www.bosch-sensortec.com/products/motion-sensors/imus/bmi270.html) is an ultra-low-power IMU providing a 6-axis sensor that combines a 16-bit tri-axial gyroscope and a 16-bit tri-axial accelerometer. It is present on both the XENSIV&trade; Sensor Shield (SHIELD_XENSIV_A) and the PSOC&trade; 6 AI Evaluation Kit (CY8CKIT-062S2-AI). PSOC&trade; 6 MCU interfaces with this sensor and reads the accelerometer data from which the orientation is computed and displayed on the terminal application.

The BMI270 motion sensor is interfaced with PSOC&trade; 6 MCU using an I2C interface and up to two interrupt pins. BMI270 has a hardware-selectable I2C slave address, depending on the logic driven on the SDO pin. On the SHIELD_XENSIV_A, the SDO pin is pulled high, which selects the slave address 0b1101001 (0x69).

BMI270 provides two interrupt channels (INT1 and INT2) to which various interrupt events can be assigned. In this example, the interrupt is assigned to the interrupt pin that is configured using the `IMU_INTERRUPT_CHANNEL` macro. See the [BMI270 datasheet](https://www.bosch-sensortec.com/products/motion-sensors/imus/bmi270.html) for details on interrupt outputs.

In the BMI270 interrupt configuration, the accelerometer interrupt is used instead of the orientation interrupt since the orientation interrupt is now a legacy feature and not implemented in the [BMI270 motion sensor library](https://github.com/Infineon/sensor-motion-bmi270).

On PSOC&trade; 6 MCU, the GPIO pin connected to the BMI160/BMI270 motion sensor's interrupt pin (INT1 or INT2 based on the `IMU_INTERRUPT_CHANNEL` macro) is configured as an input pin to detect rising-edge interrupts. The GPIO pin is automatically chosen by the example depending upon the combination of the kit, shield, and the interrupt channel being used. The shield (or custom interface) being used and the desired interrupt channel to be used can be configured in the *motion_task.h* file as described earlier.


### Code example execution flow

The main function initializes the BSP and the retarget-io library, and creates the motion sensor task. The task initializes the motion sensor and configures the interrupt. The task reads the accelerometer data, computes the orientation, displays it on the UART console, and then waits indefinitely for a task notification. Upon receiving an interrupt from the motion sensor, the ISR is invoked which notifies the motion sensor task. Then, the task reads the motion sensor data, updates the orientation state, and waits for the notification again. This operation continues indefinitely.


### Resources and settings

An SCB-based resource in I2C mode is configured with the help of I2C HAL APIs to implement the I2C master interface to BMI160/BMI270 sensor. The I2C clock frequency is set to 1 MHz. Configuration of the motion sensor and acquiring the accelerometer information are performed over this interface.

**Table 2. Application resources**

 Resource  |  Alias/object     |    Purpose
 :-------- | :-------------    | :------------
 SCB (I2C) (HAL) | kit_i2c                 | I2C master driver to communicate with the BMI160 motion sensor
 UART (HAL)      | cy_retarget_io_uart_obj | UART HAL object used by retarget-io for the debug UART port
 GPIO (HAL)      | IMU_INTERRUPT_PIN    | Motion sensor interrupt pin

<br>


## Related resources

Resources  | Links
-----------|----------------------------------
Application notes | [AN228571](https://www.infineon.com/AN228571) – Getting started with PSOC&trade; 6 MCU on ModusToolbox&trade; <br>[AN215656](https://www.infineon.com/AN215656) – PSOC&trade; 6 MCU: Dual-CPU system design
Code examples | [Using ModusToolbox&trade;](https://github.com/Infineon/Code-Examples-for-ModusToolbox-Software) on GitHub
Device documentation | [PSOC&trade; 6 MCU datasheets](https://documentation.infineon.com/html/psoc6/bnm1651211483724.html) <br> [PSOC&trade; 6 technical reference manuals](https://documentation.infineon.com/html/psoc6/zrs1651212645947.html)
Development kits | Select your kits from the [Evaluation board finder](https://www.infineon.com/cms/en/design-support/finder-selection-tools/product-finder/evaluation-board)
Libraries on GitHub | [mtb-pdl-cat1](https://github.com/Infineon/mtb-pdl-cat1) – PSOC&trade; 6 Peripheral Driver Library (PDL) <br> [mtb-hal-cat1](https://github.com/Infineon/mtb-hal-cat1) – Hardware Abstraction Layer (HAL) library <br> [retarget-io](https://github.com/Infineon/retarget-io) – Utility library to retarget STDIO messages to a UART port <br> [freeRTOS](https://github.com/Infineon/freertos) – A port of FreeRTOS kernel for PSOC&trade; 6 MCUs
Middleware on GitHub  | [psoc6-middleware](https://github.com/Infineon/modustoolbox-software#psoc-6-middleware-libraries) – Links to all PSOC&trade; 6 MCU middleware
Tools  | [ModusToolbox&trade;](https://www.infineon.com/modustoolbox) – ModusToolbox&trade; software is a collection of easy-to-use libraries and tools enabling rapid development with Infineon MCUs for applications ranging from wireless and cloud-connected systems, edge AI/ML, embedded sense and control, to wired USB connectivity using PSOC&trade; Industrial/IoT MCUs, AIROC&trade; Wi-Fi and Bluetooth&reg; connectivity devices, XMC&trade; Industrial MCUs, and EZ-USB&trade;/EZ-PD&trade; wired connectivity controllers. ModusToolbox&trade; incorporates a comprehensive set of BSPs, HAL, libraries, configuration tools, and provides support for industry-standard IDEs to fast-track your embedded application development

<br>


## Other resources

Infineon provides a wealth of data at [www.infineon.com](https://www.infineon.com) to help you select the right device, and quickly and effectively integrate it into your design.


## Document history

Document title: *CE231864* – *PSOC&trade; 6 MCU: Motion sensor interfacing using I2C*

 Version | Description of change
 ------- | ---------------------
 1.0.0   | New code example
 1.1.0   | Added support for new kits
 2.0.0   | Major update to support ModusToolbox&trade; v3.0 and BSPs v4.X <br>This version is not backward compatible with previous versions of ModusToolbox&trade;
 2.1.0   | Added support for SHIELD_XENSIV_A XENSIV&trade; sensor shield with CY8CKIT-062S2-43012
 2.2.0   | Added support for CY8CKIT-062S2-AI kit

<br>


All referenced product or service names and trademarks are the property of their respective owners.

The Bluetooth&reg; word mark and logos are registered trademarks owned by Bluetooth SIG, Inc., and any use of such marks by Infineon is under license.

PSOC&trade;, formerly known as PSoC&trade;, is a trademark of Infineon Technologies. Any references to PSoC&trade; in this document or others shall be deemed to refer to PSOC&trade;.

---------------------------------------------------------

© Cypress Semiconductor Corporation, 2020-2025. This document is the property of Cypress Semiconductor Corporation, an Infineon Technologies company, and its affiliates ("Cypress").  This document, including any software or firmware included or referenced in this document ("Software"), is owned by Cypress under the intellectual property laws and treaties of the United States and other countries worldwide.  Cypress reserves all rights under such laws and treaties and does not, except as specifically stated in this paragraph, grant any license under its patents, copyrights, trademarks, or other intellectual property rights.  If the Software is not accompanied by a license agreement and you do not otherwise have a written agreement with Cypress governing the use of the Software, then Cypress hereby grants you a personal, non-exclusive, nontransferable license (without the right to sublicense) (1) under its copyright rights in the Software (a) for Software provided in source code form, to modify and reproduce the Software solely for use with Cypress hardware products, only internally within your organization, and (b) to distribute the Software in binary code form externally to end users (either directly or indirectly through resellers and distributors), solely for use on Cypress hardware product units, and (2) under those claims of Cypress's patents that are infringed by the Software (as provided by Cypress, unmodified) to make, use, distribute, and import the Software solely for use with Cypress hardware products.  Any other use, reproduction, modification, translation, or compilation of the Software is prohibited.
<br>
TO THE EXTENT PERMITTED BY APPLICABLE LAW, CYPRESS MAKES NO WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, WITH REGARD TO THIS DOCUMENT OR ANY SOFTWARE OR ACCOMPANYING HARDWARE, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  No computing device can be absolutely secure.  Therefore, despite security measures implemented in Cypress hardware or software products, Cypress shall have no liability arising out of any security breach, such as unauthorized access to or use of a Cypress product. CYPRESS DOES NOT REPRESENT, WARRANT, OR GUARANTEE THAT CYPRESS PRODUCTS, OR SYSTEMS CREATED USING CYPRESS PRODUCTS, WILL BE FREE FROM CORRUPTION, ATTACK, VIRUSES, INTERFERENCE, HACKING, DATA LOSS OR THEFT, OR OTHER SECURITY INTRUSION (collectively, "Security Breach").  Cypress disclaims any liability relating to any Security Breach, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any Security Breach.  In addition, the products described in these materials may contain design defects or errors known as errata which may cause the product to deviate from published specifications. To the extent permitted by applicable law, Cypress reserves the right to make changes to this document without further notice. Cypress does not assume any liability arising out of the application or use of any product or circuit described in this document. Any information provided in this document, including any sample design information or programming code, is provided only for reference purposes.  It is the responsibility of the user of this document to properly design, program, and test the functionality and safety of any application made of this information and any resulting product.  "High-Risk Device" means any device or system whose failure could cause personal injury, death, or property damage.  Examples of High-Risk Devices are weapons, nuclear installations, surgical implants, and other medical devices.  "Critical Component" means any component of a High-Risk Device whose failure to perform can be reasonably expected to cause, directly or indirectly, the failure of the High-Risk Device, or to affect its safety or effectiveness.  Cypress is not liable, in whole or in part, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any use of a Cypress product as a Critical Component in a High-Risk Device. You shall indemnify and hold Cypress, including its affiliates, and its directors, officers, employees, agents, distributors, and assigns harmless from and against all claims, costs, damages, and expenses, arising out of any claim, including claims for product liability, personal injury or death, or property damage arising from any use of a Cypress product as a Critical Component in a High-Risk Device. Cypress products are not intended or authorized for use as a Critical Component in any High-Risk Device except to the limited extent that (i) Cypress's published data sheet for the product explicitly states Cypress has qualified the product for use in a specific High-Risk Device, or (ii) Cypress has given you advance written authorization to use the product as a Critical Component in the specific High-Risk Device and you have signed a separate indemnification agreement.
<br>
Cypress, the Cypress logo, and combinations thereof, ModusToolbox, PSoC, CAPSENSE, EZ-USB, F-RAM, and TRAVEO are trademarks or registered trademarks of Cypress or a subsidiary of Cypress in the United States or in other countries. For a more complete list of Cypress trademarks, visit www.infineon.com. Other names and brands may be claimed as property of their respective owners.
