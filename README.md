# Phase Locked Loop IC Design using sky130 PDK (VSD Workshop)

![image](https://user-images.githubusercontent.com/100680231/156886272-6601af21-d688-46f3-8a68-8ee786c4b9a7.png)

This is a documentation repo for the workshop on PLL Design using Google's Skywater 130nm PDK, conducted by VLSI System Design (VSD). Following is the report on the 2 day workshop - what was taught & the labs I performed.

## Contents
1. [Intro to PLL](#PL)
2. [Intro to Phase Frequency Detector](#PD)
3. [Intro to Charge Pump](#CP)
4. [Intro to Voltage Controlled Oscillator & Frequency Divider](#VCO)
5. [Tools Used](#TU)
6. [Specifications of the PLL](#Spec)
7. [Pre-Layout Circuit Design and Simulations](#Prelay)
8. [Layout Design](#Lay)
9. [Post-Layout Parasitics Extraction & Simulations](#Postlay)
10. [Tapeout](#Tape)
11. [Acknowledgements](#ack)
12. [Author](#auth)

## Day 1: PLL Theory
Day 1 was about the theory behind PLLs, how they work, what subcircuits do they contains etc.
<a name="PL"></a>
## 1. Intro to PLL
PLL or Phase Locked Loop is an essential circuit in modern electronoics that basically generates a clock signal with a very pure frequency spectrum. These clock signals are the backbone of any digital circuit and are essential for their working, because of which they need to be very accurate and have minimum diviation from the ideal reference clock signal specifications. This is where PLLs use feedback control mechanisms to dynamically control the output clock signal to be an exact match of the reference clock signal in terms of phase. The main parts of any PLL are - Phase Frequency Detector, Charge Pump, Low Pass Filter & Voltage Controlled Oscillator. Some Clock Multiplier PLLs have Frequency Divider as an additional block in the feedback loop.

![image](https://user-images.githubusercontent.com/100680231/156895970-2062d23e-2d02-4fe1-bf5e-585cce560fd7.png)
<a name="PD"></a>
## 2. Intro to Phase Frequency Detector
A phase frequency detector acts as the error generator in the PLL feedback loop. It has two inputs - one of the reference signal and other of the output signal. The circuit detects and "measures" the phase difference between the two signals and creates a proportional output signal. A simple XOR gate can work a phase detector, but its problem is that it cannot distinguish if the output is lagging or leading with respect to the reference signal. To rectify this, another circuit is introduced which gives separate "UP" & "DOWN" signals based on the lagging and leading behaviour of the output. As it turns out this circuit also can detect the frequency difference between the two signals.

![image](https://user-images.githubusercontent.com/100680231/156895974-03481b5a-ec7e-400b-8ba8-7014a0f3cb32.png)
<a name="CP"></a>
## 3. Intro to Charge Pump
Charge pumps are nothing but capacitor charging circuits that are needed to convert the digital clock signal coming from the output of the PFD into an analog signal for the voltage controlled oscillator. Charge pumps can be designed as following:

![image](https://user-images.githubusercontent.com/100680231/156895978-c29d7c4a-8346-4355-bfce-79900159e482.png)

Two current sources can be placed and can be enabled using the UP and DOWN signals coming from the PFD. UP signal enables the charging of the capacitor while DOWN enables the discharging. If the average UP signal is higher than the DOWN signal, the overall voltage of the capacitor increases and vice versa. This tells the VCO to either speed up or slow down the output of the VCO.
<a name="VCO"></a>
## 4. Intro to Voltage Controlled Oscillator & Frequency Divider
VCOs are just as the name suggests. They are oscillators which can be controlled using the input voltage provided. The easieest VCOs to implement are the Ring Oscillators which involve multiple inverters to create the osciilations. The time period of the oscillator frequency is decided using the formula 
```
T = 2*t*n     where, T = Time period of the oscillations
                     t = Delay of the individual inverters
                     n = Total number of inverters
```
![image](https://user-images.githubusercontent.com/100680231/156895982-c41bf7b9-df38-4704-b2ef-b581de3dce1c.png)

The Frequency Divider is a circuit that divides the input frequency by 2 and presents it at the output. This circuit is needed when designing a clock multiplier PLL. Frequency dividers can be as simple as a D Flip-Flop with a inverted output fed to the D input.

![image](https://user-images.githubusercontent.com/100680231/156895995-549ebb75-de02-4597-9097-67f00a9d6dfe.png)
<a name="TU"></a>
## 5. Tools Used
We used the following open source tools for this workshop:
- ngspice: This is a open source spice simulator which was used to simulate the pre and post layout PLL circuits.
- magic: This is a open source IC layout design software. The software contains the basic blocks required to build a realizable IC.
<a name="Spec"></a>
## 6. Specifications of the PLL
|Parameter|Value|
|--------------|--------------|
|Corner|TT (Typical Typical)|
|Supply|1.8V|
|Temperature|Room Temperature (~25C)|
|Mode|VCO Mode and PLL Mode|
|Input|F<sub>min</sub> = 5MHz; F<sub>max</sub> = 12.5MHz|
|Multiplier|8x|
|Jitter(RMS)|< ~20ns|
|Duty Cycle|50%|

## Day 2: Circuit Design & Simulations
<a name="Prelay"></a>
## 7. Pre-Layout Circuit Designs & Simulations
- For all the circuits, in order to create a circuit file for simulations, one needs to include sky130.lib file containing the CMOS parameter information for the 130nm process node. This can be done by:
```
.include spice_lib/sky130.lib
```
- After this, we can create all the subcircuit files to simulate individually.
- In order to simulate the .cir files, we have to use the terminal as follows:
```
ngspice <filename>.cir
```
- Following screenshots are of the .cir files of the individual subcircuits along with their simulation results.
### Frequecy Divider

![image](https://user-images.githubusercontent.com/100680231/156896011-86980c13-1a97-49e9-8eb4-be6c6365a7ed.png)

![image](https://user-images.githubusercontent.com/100680231/156891531-18d2c7b5-e126-4afa-8900-d134bacde5fb.png)
![image](https://user-images.githubusercontent.com/100680231/156891589-c3590479-bc78-423a-8383-d4f81fe7f40d.png)

### Phase Frequency Detector

![image](https://user-images.githubusercontent.com/100680231/156896020-08db5959-882d-4eea-bdd8-5c984751b1cd.png)

![image](https://user-images.githubusercontent.com/100680231/156892014-ca0bad7b-b6fd-4dec-9609-1579c30b2167.png)
![image](https://user-images.githubusercontent.com/100680231/156892027-8d5b1856-90f8-4389-81b4-55d81084f30e.png)
![image](https://user-images.githubusercontent.com/100680231/156892033-eb9621a0-3bd1-4919-98f1-bb76bf0d8e45.png)

### Charge Pump

![image](https://user-images.githubusercontent.com/100680231/156896029-fd40c878-e98e-415a-bd29-1354f33d37a8.png)

![image](https://user-images.githubusercontent.com/100680231/156892097-8a8f559d-e1cf-4d4d-a275-81e81c63ce90.png)

This is the simulation result when there is no UP or DOWN signal present. In other words, this shows the leakage current from the MOSFETs charging the output capacitor:

![image](https://user-images.githubusercontent.com/100680231/156892107-3fded466-dd48-47ef-82cf-7980c42dace8.png)

This is the simulation result when UP & DOWN signals are present:

![image](https://user-images.githubusercontent.com/100680231/156892109-6e521dfd-8f59-4ad7-b065-bc7a36ae5da1.png)

### Voltage Controlled Oscillator

![image](https://user-images.githubusercontent.com/100680231/156896048-847783ae-4dde-4d7d-8f98-1f8fad8a6082.png)

![image](https://user-images.githubusercontent.com/100680231/156892141-17f90b36-4653-46f2-861c-dfd750540846.png)
![image](https://user-images.githubusercontent.com/100680231/156892146-c1e868c6-7168-4836-a584-ed5a6f1f30f0.png)

### PLL Pre-Layout Circuit

This is a combination of all the above subcircuits, hence the PLL_Prelayout.cir file will mostly contain the copy-pasted contents of all the previous .cir files.

![image](https://user-images.githubusercontent.com/100680231/156892190-e1d1e2b2-3a83-4c8d-ace6-b1e2450bbf26.png)
![image](https://user-images.githubusercontent.com/100680231/156892234-4fd733a1-7ba6-45fa-9a6b-81a274fbf102.png)
![image](https://user-images.githubusercontent.com/100680231/156892242-65fa1614-ed97-41fa-b420-0b1a4be1013a.png)
<a name="Lay"></a>
## 8. Layout Design
For layout designs using magic tool, one needs to add the sky130 technology files in order to design for that specific technology node. We can do that by:
```
magic -T sky130A.tech <filename>.mag
```
### Frequency Divider
![image](https://user-images.githubusercontent.com/100680231/156892488-ed51016a-08bc-4c93-8e31-a49f129a873a.png)

### Phase Frequency Detector
![image](https://user-images.githubusercontent.com/100680231/156892509-c4db8903-2189-49ba-85b3-a0f93fa0d6c9.png)

### Charge Pump
![image](https://user-images.githubusercontent.com/100680231/156892527-73e229e8-79ff-4017-a7d3-eb666495276f.png)

### Voltage Controlled Oscillator
![image](https://user-images.githubusercontent.com/100680231/156892542-a62882f3-6f46-40b2-a558-90524bdf70ad.png)

### PLL
![image](https://user-images.githubusercontent.com/100680231/156892565-20415625-19a1-447b-a22f-a763780405d9.png)
<a name="Postlay"></a>
## 9. Post-Layout Parasitics Extraction & Simulations
### Parasitics Extraction
In order to extract parasitics from the finished layout, we need to first open the .mag file and then in the command line type the following in order:
```
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
This will extract a spice file with parasitics that we can readily use to simulate the circuit with the parasitics corrosponding to the designed layout. Following is an example which was carried out for all subcircuit files.

![image](https://user-images.githubusercontent.com/100680231/156892829-13b768f3-b27f-421a-a2fe-773737b4d4c4.png)

Following are the extracted parasitics from individual subcircuits:

### Frequency Divider
![image](https://user-images.githubusercontent.com/100680231/156892950-0c64ba87-af21-477c-8311-f9af9bd701cf.png)

### Phase Frequency Detector
![image](https://user-images.githubusercontent.com/100680231/156893066-61c64cf7-b2cf-407a-849c-c9389ba4242c.png)

### Charge Pump
![image](https://user-images.githubusercontent.com/100680231/156893160-6db61339-d522-4244-b692-453a410eec04.png)

### Voltage Controlled Oscillator
![image](https://user-images.githubusercontent.com/100680231/156893218-844633ba-4460-41f0-8adc-a4e0d1163bbb.png)

### PLL
![image](https://user-images.githubusercontent.com/100680231/156893356-40d16285-376c-4f29-a1df-334c307d72dc.png)

### Post Layout Simulations
Post layout simulations will be more accurate than the pre layout simulations as they include the layout parasitics which are present when the IP is actually fabricated on silicon. Post layout simualtions are carried out using a separate .cir file with a ```.include <filename>.spice``` statement in it to include the spice file with parasitics. Following are the simulation results for the PLL post layout:

![image](https://user-images.githubusercontent.com/100680231/156893769-9530f18c-b714-431b-a6d0-cfe637488791.png)

These are all the signals of the PLL:

![image](https://user-images.githubusercontent.com/100680231/156893609-cab9eb31-9182-4b55-97ae-13bf6ec1903d.png)

these are the zoomed waveforms of the PLL:

![image](https://user-images.githubusercontent.com/100680231/156893621-204360c8-bfa7-467d-8f03-2db804df8c79.png)

These are the reference and output clocks of the PLL:

![image](https://user-images.githubusercontent.com/100680231/156893612-4b6ba516-298b-493f-8d34-ec5716b48437.png)

Zoomed version of the reference and output waveforms of the PLL:

![image](https://user-images.githubusercontent.com/100680231/156893630-62a0ac2a-7260-4a13-8f0a-fdd268beb7c0.png)
<a name="Tape"></a>
## 10. Tapeout
Tapeout means to prepare your IP and integrate it with the other parts of your IC to send it for fabrication. Tapeout includes addition of other miscellaneous things such as I/O pads, communication standards(I2C/SPI/UART etc.) and other peripherals. after all of this is done, we can extract the fabrication file, known as .gds file from the magic layout tool. This file is then sent to the fab.

![image](https://user-images.githubusercontent.com/100680231/156894913-0f114ad7-858c-4628-86d3-5d343ad327f1.png)

For our design we use an open source "vessel" of sorts called the "Caravel SoC", which can house our IP along with the pre laid out IPs that are required for an SoC, such as:
- Processor
- I/O Pads
- Memory
- Communication Standards
- Testing mechanisms
- Power Ports

We can insert our IP in the user project area of the SoC to incorporate it into the SoC. For this:
- We need to first download the GDS file of the User Project Area. 
- Then we can open the GDS file in magic tool along with the IP layout.
- No it is just a matter of connecting the user designed IP with the appropriate pins provided to the user project area while also keeping parasitics/ noise/ ESD etc. parameters in mind. 
- Once the layout and integration of the IP is complete, we can create the GDS file and send it for fabrication.
<a name="ack"></a>
## 11. Acknowledgements
- [Lakshmi S.](https://www.linkedin.com/in/lakshmis96/), MS, Giorgia Institute of Technology
- [Kunal Ghosh](https://www.linkedin.com/in/kunal-ghosh-vlsisystemdesign-com-28084836/), Co-founder, VLSI System Design (VSD) Corp. Pvt. Ltd.
<a name="auth"></a>
## 12. Author
- [Kanad Mainkar](https://www.linkedin.com/in/kanad-mainkar-854b631a4/), B.E. Instrumentation & Control, Vishwakarma Institute of Technology
