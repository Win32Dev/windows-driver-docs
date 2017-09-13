---
title: Transport Bus Driver FAQ
description: The following are common questions, scenarions, and issues that a driver developer may encounter when developing a bus driver to support Bluetooth functionality.
ms.assetid: 7189EB3B-E071-4145-8308-EFA6D4E89D4B
ms.author: windowsdriverdev
ms.date: 04/20/2017
ms.topic: article
ms.prod: windows-hardware
ms.technology: windows-devices
---

# Transport Bus Driver FAQ


The following are common questions, scenarions, and issues that a driver developer may encounter when developing a bus driver to support Bluetooth functionality.

## <span id="my_serial_bus_driver_encountered_some_error._what_does_it_mean_"></span><span id="MY_SERIAL_BUS_DRIVER_ENCOUNTERED_SOME_ERROR._WHAT_DOES_IT_MEAN_"></span>My serial bus driver encountered some error. What does it mean?


Code 10-49: An [explanation of error codes generated by Device Manager](http://support.microsoft.com/kb/310123).

Code 51: When a serial bus driver has dependent controller drivers (as defined in the ACPI table), the system will load the serial bus driver, but it will not get started until all of its dependent drivers have started successfully. When the controller driver is absent or not loaded successfully, a code 51 error is triggered.

Code 52 = Unsigned driver.

Driver signing is described in greater detail in these topics:

-   [Driver Signing](https://msdn.microsoft.com/library/windows/hardware/ff544865)
-   [Driver Signing Requirements for Windows](http://msdn.microsoft.com/windows/hardware/gg487317)

## <span id="Why_is_my_serial_bus_driver_not_getting_any_IOCTLs_from_the_Bluetooth_core_stack_"></span><span id="why_is_my_serial_bus_driver_not_getting_any_ioctls_from_the_bluetooth_core_stack_"></span><span id="WHY_IS_MY_SERIAL_BUS_DRIVER_NOT_GETTING_ANY_IOCTLS_FROM_THE_BLUETOOTH_CORE_STACK_"></span>Why is my serial bus driver not getting any IOCTLs from the Bluetooth core stack?


A serial bus driver may have enabled idle capabilities but not implemented the power control handling mechanisms discussed in section and its subtopics. In this situation, a Bluetooth HID device (mouse or keyboard) will not be able to wake the stack (from D2 to D0). It is recommended to turn off this idle capability until basic Bluetooth functions and power control handling mechanisms have been implemented.

## <span id="Are_there_Bluetooth_Windows_Logo_Tests_"></span><span id="are_there_bluetooth_windows_logo_tests_"></span><span id="ARE_THERE_BLUETOOTH_WINDOWS_LOGO_TESTS_"></span>Are there Bluetooth Windows Logo Tests?


The Windows HCK (Hardware Certification Kit) contains a set of Bluetooth-specific tests that must be passed to receive certification. Bluetooth driver developers should run these tests during development to ensure their driver passes them in order to receive certification and have proper support.

## <span id="Should_UART_settings_be_preserved_and_restored_among_the_different_power_state_transitions_"></span><span id="should_uart_settings_be_preserved_and_restored_among_the_different_power_state_transitions_"></span><span id="SHOULD_UART_SETTINGS_BE_PRESERVED_AND_RESTORED_AMONG_THE_DIFFERENT_POWER_STATE_TRANSITIONS_"></span>Should UART settings be preserved and restored among the different power state transitions?


Since the Bluetooth transport driver is aware of and has control over the remote UART controller’s settings (including its settings after powering on), the Bluetooth transport driver should reset both the remote and local UART controllers before entering a state in which power could be lost. Thus, when power is restored, the settings on both the remote UART (Bluetooth side) and the local UART (Host side) controllers can be in sync. The UART driver has control over the local UART controller only and will be responsible to restore to its settings before powering off. The Bluetooth transport driver can also query the UART driver regarding its power-up settings (since UART driver set them initially using the ACPI table), cache and set them when entering some Dx state.

## <span id="What_happens_if_the_bus_driver_receives_a_wake_notification_while_the_stack_is_in_the_process_of_entering_D2_"></span><span id="what_happens_if_the_bus_driver_receives_a_wake_notification_while_the_stack_is_in_the_process_of_entering_d2_"></span><span id="WHAT_HAPPENS_IF_THE_BUS_DRIVER_RECEIVES_A_WAKE_NOTIFICATION_WHILE_THE_STACK_IS_IN_THE_PROCESS_OF_ENTERING_D2_"></span>What happens if the bus driver receives a wake notification while the stack is in the process of entering D2?


In this situation, the bus driver should complete the D2 transition, and then proceed to the wake mechanism. The serial bus driver is expected to do this synchronization (e.g. it can queue the wake request and start the wake process only after the D2 transition has completed).

## <span id="How_should_a_bus_driver_identify_its_ACPI_resource_of_multiple_occurrences__such_as_multiple_GPIOs_"></span><span id="how_should_a_bus_driver_identify_its_acpi_resource_of_multiple_occurrences__such_as_multiple_gpios_"></span><span id="HOW_SHOULD_A_BUS_DRIVER_IDENTIFY_ITS_ACPI_RESOURCE_OF_MULTIPLE_OCCURRENCES__SUCH_AS_MULTIPLE_GPIOS_"></span>How should a bus driver identify its ACPI resource of multiple occurrences, such as multiple GPIOs?


There is no way to tag a resource, GPIO or otherwise. This is why the resource order is important. The device driver should establish a convention (e.g. first GPIO resource to appear in the list is for BT\_WAKE and the second one to appear is for HOST\_WAKE).

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20[bltooth\bltooth]:%20Transport%20Bus%20Driver%20FAQ%20%20RELEASE:%20%283/20/2017%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")



