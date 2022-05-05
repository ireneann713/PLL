# PLL

# Phase Locked Loop IC Design


VSD workshop - Phase Locked Loop(PLL) IC Design!

The following repo is the documentation of learnings and activities done throughout a 2-day workshop on PLL IC Design with Synopsys Tool conducted by VLSI sytem design.


# *Contents*
------------
* [Introduction to PLL](#introduction-to-pll)
  * [Phase Frequency Detector](#phase-frequency-detector)
  * [Charge Pump](#charge-pump)
  * [VCO and Frequency Divider](#vco-and-frequency-divider)
* [Tools Overview and Design Flow](#tools-overview-and-design-flow)
  * [Design Flow](#design-flow)
  * [Tool Setup](#tool-setup)
   * [SKY130 PDK](#sky130-pdk)
   * [NGSPICE](#ngspice)
   * [Magic](#magic)
* [Circuit Design and Simulation](#circuit-design-and-simulation)
  * [Specifications](#specification)
  * [Frequency Divider Circuit](#frequency-divider-circuit)
  * [PFD Circuit](#pfd-circuit)
  * [Charge Pump Circuit](#charge-pump-circuit)
  * [VCO Circuit](#vco-circuit)
  * [PLL Circuit](#pll-circuit)
* [Layout Design](#layout-design)
* [Parasitic Extraction and PLS](#parasitic-extraction-and-pls)
* [Tapeout](#tapeout)
* [Acknowledgement](#acknowledgement)




---------
## Introduction to PLL
Phase locked loops are important components in any digital circuits. It is responsible for creating a precise clock signal without any noise(frequency or phase).
![PLL-basic-structure](https://user-images.githubusercontent.com/78468534/127774506-b254b925-d629-4f40-8440-e0f332b1e57c.jpeg)

The above diagram shows basic block diagram of the PLL to be implemented.
The functionality of PLL can be decribed as two processes.
* Comparing frequency of reference and ouput(PFD)
* Adjusting frequency to match reference signal(CP and VCO)


#### Phase Frequency Detector
The phase frequency detector(PFD) is responsible for comparing the reference signal given as input and the output signal. Then it should produce output which clearly shows the difference of phase. This phase difference is not just in terms of magnitude but it should also show whether the ouput is leading or lagging behind the reference. The ouput of PFD is in digital form.
#### Charge pump
The CP converts the digital output from PFD to an analog signal. This analog signal is what would control the VCO. The analog ouput from CP is passed through a low pass filter before connecting to the VCO. This low pass filter can help smoothen the signal in addition to stabilizing the feedback loop.
#### VCO and Frequency Divider
Voltage controlled oscillators are the actual parts which produces alternating digital clock signal. The frequency of this clock signal can be controlled by input voltage, hence the name. VCO can be implemented using simple inverters.
A PLL with a frequency divider on its feedback loop is called a clock multiplier PLL. Such a PLL can make clock signals which are multiples of the reference signals.

---------
## Tools Overview and Design Flow
#### Design Flow

Design flow of the PLL IC has the following steps:
1. Specifications of the IC
2. SPICE level circuit development
3. Pre-layout simulation


#### Tool Setup

##### Synopsys Custom Design Platform
Custom Compiler provides a highly productive environment for design entry and simulation, with strong features for mixed-signal design, debug, simulation
management, analysis, and reporting.
###### PrimeWave Design Environment
PrimeWave Design Environment is a comprehensive and flexible environment for simulation setup and analysis of analog, RF, mixed-signal design, custom-digital and memory designs within the Synopsys Custom Design Platform.

###### PrimeSim HSPICE
PrimeSim HSPICE is the accurate circuit simulator and offers foundries-certified MOS device models with state-of-theart simulation and analysis algorithms. It is used for building digital MOSFET circuits using 28nm technology.

---------
## Circuit Design and Simulation

#### Specifications
As mentioned in the design flow, every design starts with determining the specifications. The specifications for PLL to be designed are:

* Corner - 'TT' (_Typical-Typical_)
* Supply Voltage - 1.8V
* Room Temperature
* VCO mode and PLL mode
* Input F<sub>min</sub>=5Mhz; F<sub>max</sub>=12.5Mhz
* Multiplier - 8x
* Jitter(RMS) < ~20ns
* Duty Cycle - 50%

#### Frequency Divider Circuit
A simple 2x frequency divider circuit can be obtained by using a single D-flipflop whose output is fed back to its input after passing through an inverter. Cascading 3 such flops can give you 8x divider.
This simple circuit can be drawn as:  
![FD_circuit](https://user-images.githubusercontent.com/78468534/127781480-b09756aa-a4ce-48e4-8164-4fad67cf1f7d.jpeg)


*Pre-layout Simulation*  
![Screenshot (369)](https://user-images.githubusercontent.com/55539862/166863633-72a58662-28c2-45b3-a321-88f870ac8c79.png)



#### PFD Circuit
The PFD circuit is designed such that, square(digital) signals with pulse width proportional to phase difference are produced at output. Also two different outputs are used to distinguish between cases when output is leading reference signal and lagging behind reference signal.

Given below are the PFD circuit..  
![PFD_circuit](https://user-images.githubusercontent.com/78468534/127782010-b21f76ed-6bed-4406-bfd0-2c5fca9838ac.jpeg)

 



*Pre-layout Simulation*  

![pfd](https://user-images.githubusercontent.com/55539862/166933171-a3bd82c0-e283-4fa2-8894-2d8dac713fac.png)


_PFD Prelayout Simulation Results_

#### Charge Pump Circuit
The charge pump circuit with modification considering the leakage current is  
![CP_circuit](https://user-images.githubusercontent.com/78468534/127782045-6a5b2df2-13fd-4456-9337-6b2af6604d05.jpeg)  
and the spice file is  



*Pre-layout Simulation*  
![CP](https://user-images.githubusercontent.com/55539862/166932949-e21f6d8b-80f9-4b10-9f90-038fbd971856.png)

_CP Prelayout Simulation Result_

#### VCO Circuit
The VCO circuit is realised with a current starved 3 inverter circuit. Using this method, the delay of inverter can be controlled and thereby the frequency of output clock.
                          frequency of clock = 1/(2 * delay of inverter * no: of inverters)
                          
The circuit is:  
![VCO_circuit](https://user-images.githubusercontent.com/78468534/127782614-ed6b8289-cf29-4cd9-bfe7-078176fe6c26.jpeg)


*Pre-layout Simulation*  
 
_VCO Prelayout Simulation Result_
![VCO](https://user-images.githubusercontent.com/55539862/166933100-1a0f26af-1e69-477d-a64e-75740f84ede0.png)

#### PLL Circuit
The final PLL circuit is the joining of all the other blocks. However in addition to individual simulations, the PLL as a whole also need to be simulated. For this we create the spice file by calling the individual circuits as subcircuits.  
![PLL_spice](https://user-images.githubusercontent.com/78468534/127783331-469f1c70-0ca4-421c-a20b-e7afc3ff4296.jpeg)

*Pre-layout Simulation*  
![PLL_prelay_sim](https://user-images.githubusercontent.com/78468534/127783351-b3ff364a-3691-485e-b655-543d0290a1a2.jpeg)
![PLL_prelay_graph](https://user-images.githubusercontent.com/78468534/127783355-32111d1c-00c2-4697-a09f-596713cf9852.jpeg)  
_PLL simulation results_

---------

## Acknowledgement
* [Kunal Ghosh](https://github.com/kunalg123), Co-founder,VSD
* [Lakshmi S](https://github.com/lakshmi-sathi), Instructor - [8x PLL Clock Multiplier IP](https://github.com/lakshmi-sathi/avsdpll_1v8)
