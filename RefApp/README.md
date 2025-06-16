![Reference Applications](./img/RefAppWorkflow.png)

# Reference Applications

CMSIS v6 introduces the concept of
[software layers](https://open-cmsis-pack.github.io/cmsis-toolbox/build-overview/#software-layers), enabling developers
to create flexible and portable
[reference applications](https://open-cmsis-pack.github.io/cmsis-toolbox/ReferenceApplications/) that scale across
various supported devices and boards.

## Objective

This lab demonstrates how to use CMSIS layers to build a simple HTTP web server application on the NUCLEO-F756ZG
development board. The application utilizes components from the
[MDK-Middleware](https://github.com/ARM-software/MDK-Middleware) and serves as a practical reference implementation.

## Procedure

### Create the project

1. Open a new empty instance of VS Code.
2. Go to **CMSIS View** and select “Create a New Solution”:  
   ![Create a New Solution](./img/CreateNewSolution.png)
3. In the "Create Solution" dialog, configure as follows:
   
   - Target Board: NUCLEO-F756ZG  
   - Reference Application: Network  
   - Choose a suitable base directory.  
   ![Create a New Solution](./img/CreateNetworkApp.png)
4. Click “Create”.
5. In the following "Configure Solution" dialog just press OK.  
  ![Add_SW_Layer](https://github.com/user-attachments/assets/10c16603-3904-4291-974a-689b40b5d934)
6. Open `vcpkg_configuration.json` and add the Arm GNU Toolchain using the GUI preview; enter: "arm:compilers/arm/arm-none-eabi-gcc": "14.2.1" and save the file.  
   ![Add the GCC toolchain to the vcpkg_configuraiton.json file](./img/AddGCCvcpkgConfig.png)

### Configure Project

1. Go to **CMSIS View**.
2. Press the cog wheel to open the "Manage Solution" dialog.
3. In the dialog, set the "HTTP_Server" as active project.  
   ![Context Set](./img/ContextSet.png)  
4. Modify the following project files as per
   [setup documentation](https://github.com/Open-CMSIS-Pack/vscode-cmsis-debugger/blob/main/docs/setup.md):

   In `Network.csolution.yml`, insert the following block in `- target-types\- type` section:
  
   ```yml
        target-set:
          - set: 
            debugger:
              name: ST-LINK
            images:
              - project-context: HTTP_Server.Debug
   ```

   In `Network.csolution.yml`, insert before the `- projects` section:

   ```yml
     misc:
      - for-compiler: AC6
        C-CPP:
          - -gdwarf-5
        ASM:
          - -gdwarf-5
        Link:
          - --entry=Reset_Handler
   ```

   In `HTTP_Server/HTTP_Server.cproject.yml`, add at the end:

   ```yml
     output:
       type:
        - elf
        - hex
        - map
   ```

5. In the `HTTP_Server/RTE/Network/Net_Config_ETH_0.h` file, set the Ethernet MAC address to the one printed on your
   development board box.
6. Build the project. It should build without errors or warnings.

### Open a serial Terminal

1. Go to **Device Manager** view.
2. Open a serial console connected to the ST-Link interface.  
   ![Opening a serial console](./img/DeviceManagerView.png)
3. In the pop up box, enter a baud rate of 115200.

### Start a debug session

1. Switch to the **Debug View** and select **STLink@pyOCD (launch)**.
2. Click the **Play** icon to launch the debug session.  
   ![Starting a debug session](./img/DebugView.png)

### Using the project

You can use the web server by entering the IP address that is shown in the serial console in your web browser.

Alternatively, to find the IP address programmatically:

- Open `HTTP_Server.c` and locate the line `printf("IP4: %s\n", ip_ascii);`.
- Set a breakpoint at this line.
- Add `ip_ascii` to the Watch window.
- Run the application to the breakpoint and observe the IP address.

## Conclusion

This lab illustrates the ease of setting up a reference application on a CMSIS-supported development board. Leveraging
CMSIS layers and MDK-Middleware components, developers can quickly prototype and validate embedded network
applications with minimal effort.

## Where to go next

In the next lab, you'll learn how to use VS Code extensions for working directly with [GitHub](https://github.com) from
within the IDE. it also explains how to run CI/CD tests on
[Arm Virtual Hardware](https://github.com/Arm-Examples/AVH_CI_Template) using
[GitHub Actions](https://github.com/features/actions). Please continue with the [README](../CI_CD/README.md).
