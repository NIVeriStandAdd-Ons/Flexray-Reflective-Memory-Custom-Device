## Flexray Reflective Memory Custom Device ##

**Flexray Reflective Memory Custom Device** is a custom device built to synchronize the sample and data sharing of multiple PXI systems via Flexray. It was built with a specific use case in mind, clocking the PCL at and Flexray bus at 4kHz and decimating a model by 4 to achieve a virtual 1kHz rate with zero transfer delay tics.

Do not use more than one instance of this custom device on a single target, as no protection has been built in for hardware trigger line conflicts or timing conflicts.

### Instructions ###

Using the provided example NI VeriStand Project and database:

To use the provided example NIVS Project, you must add the FlexRefMemDB.xml to your xnet database aliases and download it to your real time targets using the NI-XNET database editor.

The provided NI VeriStand project contains a system definition with two targets, each target has this timing and sync custom device added to it, and the signals from the provided database added to it. SigA through SigD. SigA and SigC are configured for both targets to be sync'd before model execution, and sigB and sigD are configured to be sync'd after model execution. The read/write access for all signals are inverted between targets, as expected.

Each target has its PCL timing source set to the custom device, as can be observed on the controller page.

For demo purposes, this system definition has an Analog Out channel configured on each target. The data being provided to the AO channel is what a 4x decimated model would see at its input. To accomplish this, a procedure was created under each target to sample the SigA channel every 4th iteration and place its data into the 'Example Model Input' user channel, example like a 4x decimated model would see. This 'example model input' user channel was then mapped to AO.

Each target has its chassis hardware timing source set to the timing and sync custom device (coming from the flexray board), as can be observed on the chassis page. This drives the rest of the system's hardware from the FlexRay board. 

You can then use an oscilloscope to measure the time delay between the AO updates of each target.

#### New sysdef Setup ####
1. Add this custom device to the system def.
1. For a target to use this custom device as its timing source, select the controller page and change "primary control loop timing source" to "custom device timing" at the bottom, and then pick this custom device in the drop down list. Also, set the execution mode to "low latency"
1. For DAQ to by sync'd to the FlexRay timing, select the chassis page and change "Clock source" to "RTSI/PXI_TRIG 1".
1. Select the custom device and specify what interface, database, and cluster you wish to use. Make sure your database is set to 250uS cycle time
1. Use the XNET database editor to download your database to your targets.
1. select the custom device and add channels as appropriate for your configuration.
1. After adding channels, select each channel and say wether you want it sync'd before or after model execution. The ideal setup would your model inputs would be sync'd before and your model outputs would be sync'd after. To avoid delays, make sure all your targets have the same signal set to before or after.
1. Add your models and specify a decimation of 4.
1. Map your model i/o to your flex ray signals as needed.

### LabVIEW Version ###

LabVIEW 2010

### Built Availability ###

No builds are provided

### Quality, Limitations ###

This was created as a proof of concept and never used in a real system. It was created in 2010 and has not been tested since or validated against real world requirements. Use with caution.

### Dependencies ###

NI XNET

### License ###

*This repository and any materials provided by NI therein are provided AS IS. NI DISCLAIMS ANY AND ALL LIABILITIES FOR AND MAKES NO WARRANTIES, EITHER EXPRESS OR IMPLIED, INCLUDING WITHOUT LIMITATION ANY WARRANTIES OF MERCHANTABILITY, FITNESS FOR  PARTICULAR PURPOSE, OR NON-INFRINGEMENT OF INTELLECTUAL PROPERTY. NI shall have no liability for any direct, indirect, incidental, punitive, special, or consequential damages for your use of the repository or any materials contained therein.*