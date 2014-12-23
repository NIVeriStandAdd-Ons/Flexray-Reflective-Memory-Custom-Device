This demo shows the hardware synchronization and data sharing. There is no 'model' or math implemented in this demo. If you need that... I can shove this into a custom device.

Since NI-XNET does not currently support "in-cycle" updates of FlexRay frames, this custom device relies on the FlexRay bus being 'overclocked' to 4kHz and the model decimation set to 4 so that it can perform properly at 1kHz. Because of this, this is in no way shape or form compatible with any one else's FlexRay reflective memory bus and should only be used as a demo between two NI systems.

Based upon the FlexRay standard, each frame is 64 bits, so it can hold one double or two floats. So:
If we operate at 4kHz in "overclocked" mode, we can fit 10 frames. The reflective memory ring will max out at 10 doubles or 20 floats.
If we operated at 1kHz with "in-cycle" updates, we can fit 41 frames. The reflective memory ring will max out at 41 doubles or 82 floats.

This is an inherit limit of FlexRay, not our software or hardware. Also, please let us know how this progresses so we can evaluate if implementing "in-cycle" updates becomes a priority.

Equipment required:
1. Oscilloscope with two inputs and probes
2. 2 PXI chassis with CLK10 BNC's on the back
3. 2 PXI RT controllers
4. 2 M or X series DAQ cards (E series will not work!)
5. 2 FlexRay cards
6. Coaxial cable with BNC connectors
7. FlexRay cable
8. 2 DAQ cables
9. 2 SCB-68 breakout boxes
10. 2 spare pieces of wire

Hardware setup:
1. Connect the CLK10 out from one chassis to the other chassis' CLK10 in with the BNC cable.
2. Insert the DAQ card and FlexRAY card into the PXIs
3. Cable the FlexRay cards' port1 together
4. Cable the DAQ cards' first left most (first) connector to SCB-68 breakout boxes
5. Using your 2 pieces of spare wire, add a wire to pin 22 of each DAQ board (this is AO0)
6. Connect the probes of the oscilloscope to the wires of each SCB-68

Software setup:
1. Install LV RT 2010 and XNET 1.3.1 to the targets
2. Add the database to the aliases defined by XNET. To do this open the database editor and open the database. 
3. Deploy the database to the targets. Select file -> manage databases and deploy it to the targets by typing in their IP, selecting connect, then deploy
4. In MAX, rename the DAQ board in each target to "Dev1"
5. Open the provided project
6. Right click each target and change the IP to be whatever your targets' IP is

FIRST VI TO USE is the Sync Test.VI. This VI will simply check the synchronization of the two targets by playing a sinewave over the FlexRay data and outputting that to AO0. You can then check to make sure the sinewave is synchronized on the two targets by looking at the oscilloscope.
1. Open Sync Test.VI under each target
2. Change one of the Sync Test.VI front panel buttons (each VI needs to have this setting be opposite)
3. Press run on each VI
4. Set the oscilloscope to 2V for each channel and a time period of 500uS. Set the trigger level to around 1.5V
5. Observe the sinewaves on the oscilloscope. Are they synchronized? Do they drift from eachother? If they do, double check your hardware setup.

Once you've proven the systems are synchronized, use the "Ref Mem.vi" to show synchronized reflective memory operation over FlexRay.
1. Open Ref Mem.VI under each target
2. Change one of the Ref Mem.VI front panel buttons (each VI needs to have this setting be opposite)
3. Press run on each VI
4. Observe as your increase the value for output data on each VI's front panel, you see the same value for input data arrive on the other VI.
5. Observe that you can increase the value for output data on one of the VIs and see this value sent to hardware from both DAQ cards at the exact same time (verify with oscilloscope)