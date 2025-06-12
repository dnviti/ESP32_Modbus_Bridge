# **Complete Design & Manufacturing Specification: Dual ESP32 Modbus-Wi-Fi Bridge**

Document Version: 2.3  
Date: June 12, 2025  
Project Revision: 0.1

## **1\. Schematic Design Specification**

This document provides the detailed, step-by-step connection instructions for creating the complete schematic for the Dual ESP32 Modbus-Wi-Fi Bridge, based on project revision 0.1.

### **1.1. Footprint Assignment**

Before proceeding to PCB layout, every component listed below must be assigned a correct physical footprint in the EDA software. An incorrect footprint will result in a board that cannot be assembled. The recommended footprints are provided for each component.

### **1.2. Part 1: Shared Board Components (The Foundation)**

This section details the main 24V to 3.3V power supply unit that powers the entire board.

**Component List:**

* **J1:** Conn\_Screw\_Terminal\_1x02 (Footprint: TerminalBlock:TerminalBlock\_bornier-2\_P5.08mm)  
* **U3:** LM2596S-3.3 (Footprint: Package\_TO\_SOT\_SMD:TO-263-5\_TabPin3)  
* **C2:** 100uF Electrolytic Capacitor (Footprint: Capacitor\_SMD:CP\_Elec\_6.3x5.4mm, verify voltage rating \> 24V)  
* **C1:** 220uF Electrolytic Capacitor (Footprint: Capacitor\_SMD:CP\_Elec\_8x10.5mm)  
* **D1:** D\_Schottky (1N5822 SMD equivalent) (Footprint: Diode\_SMD:D\_SMA)  
* **L1:** 33uH Inductor (Footprint: Inductor\_SMD:L\_Bourns\_SRR1260, verify current rating)  
* **D2:** LED (Footprint: LED\_SMD:LED\_0603\_1608Metric)  
* **R1:** 2.2kΩ Resistor (Footprint: Resistor\_SMD:R\_0603\_1608Metric)

**Connection Instructions:**

1. **24V Input:** Connect **Pin 1** of **J1** to a net named \+24V. Connect **Pin 2** of **J1** to the main GND net.  
2. **LM2596S-3.3 Input and Control:** Connect the \+24V net to **Pin 1 (VIN)** of **U3**. Place **C2** between the \+24V net and the GND net. Connect **Pin 3 (GND)** of **U3** to the GND net. Connect **Pin 5 (ON/OFF)** of **U3** directly to the GND net.  
3. **LM2596S-3.3 Output Circuit:** Connect the **Anode** of **D1** to the GND net. Connect the **Cathode** of **D1** to **Pin 2 (VOUT)** of **U3**. Connect one end of **L1** also to **Pin 2 (VOUT)** of **U3**. Connect the other end of **L1** to a new net named \+3.3V. Place **C1** between the \+3.3V net and the GND net.  
4. **Feedback Connection:** Connect **Pin 4 (Feedback)** of **U3** directly to the \+3.3V net.  
5. **Power Indicator LED:** Connect one end of **R1** to the \+3.3V net. Connect the other end of **R1** to the **Anode** of **D2**. Connect the **Cathode** of **D2** to the GND net.

### **1.3. Part 2: ESP32 \#1 (Modbus Master & Ethernet)**

**Component List:**

* **U1:** ESP32-WROOM-32D (Footprint: RF\_Module:ESP32-WROOM-32)  
* **U5:** LAN8720A (Footprint: Package\_QFN:QFN-24-1EP\_4x4mm\_P0.5mm\_EP2.6x2.6mm)  
* **Y1:** SG-7050CAN 50MHz (Footprint: Oscillator:Oscillator\_SMD\_5032-4Pin\_5.0x3.2mm)  
* **J5:** Hanrun HR911105A (Footprint: Connector\_RJ:RJ45\_Hanrun\_HR911105A\_Horizontal)  
* **J4:** Conn\_01x06\_Male (FTDI ESP32\#1) (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_1x06\_P2.54mm\_Vertical)  
* **SW1:** SW\_Push (U1\_BOOT) (Footprint: Button\_Switch\_SMD:SW\_SPST\_PTS645)  
* **C3, C4, C5, C6, C7, C8, C9, C10, C11:** Capacitors (See schematic for values and assign footprints like C\_0402, C\_0805)  
* **R2, R3, R4, R5, R6, R8, R9:** Resistors (See schematic for values and assign footprints like R\_0402)  
* **FB1:** Ferrite\_Bead (Footprint: Inductor\_SMD:L\_0603\_1608Metric)  
* **Debug Interface Components:** See Section 1.6.1  
* **GPIO Breakout Components:** See Section 1.7.1

**Connection Instructions:**

1. **ESP32 \#1 Power and Decoupling:** Connect the **VCC** and **GND** pins of **U1** to the \+3.3V and GND nets. Place **C3 (10uF)** and **C4 (100nF)** between VCC and GND, close to the module.  
2. **ESP32 \#1 Programming Header (FTDI):** Place **J4**. Connect its **GND** to GND, **TXD** to **GPIO3** of **U1**, and **RXD** to **GPIO1** of **U1**. Leave **VCC** and **CTS** unconnected.  
3. **ESP32 \#1 Auto-Reset and Manual Boot Circuit:** Connect a **100nF capacitor** between the **DTR pin** of **J4** and the **EN** pin of **U1**. Connect **R2 (10kΩ)** between the **EN** pin and \+3.3V. Connect **SW1** between the **GPIO0** pin of **U1** and GND. Connect **R3 (10kΩ)** between the **GPIO0** pin and \+3.3V.  
4. **ESP32 \#1 Strapping Pin:** Connect **R4 (10kΩ)** between the **GPIO12** pin of **U1** and GND.  
5. **Ethernet Sub-system (LAN8720A, Oscillator, RJ45):**  
   * **Clock Generation (Y1):** Connect the **VCC pin** of **Y1** to \+3.3V and place **C11 (100nF)** between this pin and GND. Connect the **ST pin** of **Y1** to \+3.3V. Connect the **GND pin** of **Y1** to GND. Connect the **OUT pin** of **Y1** to **Pin 5 (XTAL1/CLKIN)** of **U5**.  
   * **ESP32 to LAN8720A Interface:** Connect **Pin 14 (nINT/REFCLKO)** of **U5** to **GPIO0** of **U1**. Connect **GPIO23** of **U1** to **Pin 13 (MDC)** of **U5**. Connect **GPIO18** of **U1** to **Pin 12 (MDIO)** of **U5**. For RMII Transmit: U1 GPIO21 \-\> U5 Pin 16 (TXEN); U1 GPIO19 \-\> U5 Pin 17 (TXD0); U1 GPIO22 \-\> U5 Pin 18 (TXD1). For RMII Receive: U1 GPIO25 \<- U5 Pin 8 (RXD0); U1 GPIO26 \<- U5 Pin 7 (RXD1); U1 GPIO27 \<- U5 Pin 11 (CRS\_DV).  
   * **LAN8720A (U5) Support Circuitry:** Connect **R5 (12.1kΩ)** from **Pin 24 (RBIAS)** of **U5** to GND. Place **C7 (1uF)** and **C8 (470pF)** in parallel between **Pin 6 (VDDCR)** of **U5** and GND. For analog power filtering, connect the \+3.3V net to **FB1**, creating a \+3.3V\_Filtered net. Connect this filtered net to **Pin 1 (VDD2A)** and **Pin 19 (VDD1A)** of **U5**. Place **C10 (100nF)** from VDD2A to GND, and **C6 (100nF)** from VDD1A to GND.  
   * **RJ45 Jack (J5) and Termination:** For differential pairs: U5 Pin 21 (TXP) \-\> J5 Pin 1 (TD+); U5 Pin 20 (TXN) \-\> J5 Pin 2 (TD-); U5 Pin 23 (RXP) \-\> J5 Pin 3 (RD+); U5 Pin 22 (RXN) \-\> J5 Pin 6 (RD-). For DC bias, connect one side of all four **49.9Ω resistors (R6, R8, R9, and one more)** to the \+3.3V\_Filtered net, and connect the other side of each resistor individually to the PHY's differential pair pins (TXP, TXN, RXP, RXN). For center-tap termination, connect jack pins **4 (TCT)** and **5 (RCT)** together, then connect this node through **C9 (1000pF)** to a chassis ground point.

### **1.4. Part 3: ESP32 \#2 (Web Client)**

**Component List:**

* **U2:** ESP32-WROOM-32D (Footprint: RF\_Module:ESP32-WROOM-32)  
* **J2:** Conn\_01x06\_Male (FTDI ESP32\#2) (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_1x06\_P2.54mm\_Vertical)  
* **SW2:** SW\_Push (U2\_BOOT) (Footprint: Button\_Switch\_SMD:SW\_SPST\_PTS645)  
* **C13, C12, C14:** 10uF, 100nF, 100nF (Footprints: Capacitor\_SMD:C\_0805\_2012Metric, Capacitor\_SMD:C\_0402\_1005Metric)  
* **R10, R11, R12:** 10kΩ Resistors (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
* **Debug Interface Components:** See Section 1.6.2  
* **GPIO Breakout Components:** See Section 1.7.2

**Connection Instructions:**

1. **ESP32 \#2 Power and Decoupling:** Connect the **VCC** and **GND** pins of **U2** to the \+3.3V and GND nets. Place its dedicated **C13 (10uF)** and **C12 (100nF)** decoupling capacitors between its VCC and GND pins.  
2. **ESP32 \#2 Programming Header (FTDI):** Place **J2**. Connect its **GND** to GND, **TXD** to **GPIO3** of **U2**, and **RXD** to **GPIO1** of **U2**. Leave **VCC** and **CTS** unconnected.  
3. **ESP32 \#2 Auto-Reset and Manual Boot Circuit:** Connect **C14 (100nF)** between the **DTR pin** of **J2** and the **EN** pin of **U2**. Connect **R10 (10kΩ)** between the **EN** pin and \+3.3V. Connect **SW2** between the **GPIO0** pin of **U2** and GND. Connect **R11 (10kΩ)** between the **GPIO0** pin and \+3.3V.  
4. **ESP32 \#2 Strapping Pin:** Connect **R12 (10kΩ)** between the **GPIO12** pin of **U2** and GND.

### **1.5. Part 4: Inter-Module Communication**

* Connect the **GPIO17** pin of **ESP32 \#1 (U1)** to the **GPIO16** pin of **ESP32 \#2 (U2)**.  
* Connect the **GPIO16** pin of **ESP32 \#1 (U1)** to the **GPIO17** pin of **ESP32 \#2 (U2)**.

### **1.6. Part 5: I2C OLED Display Interface**

* **Component List:**  
  * **J11:** Conn\_01x04\_Male (I2C Connector) (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_1x04\_P2.54mm\_Vertical)  
  * **R13:** 4.7kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
  * **R18:** 4.7kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
* **Connection Instructions:**  
  1. **Display Connector (J11):** Connect **Pin 1 (GND)** to GND, **Pin 2 (VCC)** to \+3.3V, **Pin 3 (SCL)** to **GPIO22** of **U2**, and **Pin 4 (SDA)** to **GPIO21** of **U2**.  
  2. **I2C Pull-up Resistors:** Connect **R18** between the SCL line and \+3.3V. Connect **R13** between the SDA line and \+3.3V.

### **1.7. Part 6: Debug Interfaces**

#### **1.7.1. ESP32 \#1 Debug Interface**

* **Component List:**  
  * **J6:** Conn\_01x02\_Male (U1\_DEBUG) (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_1x02\_P2.54mm\_Vertical)  
  * **R16:** 10kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
  * **D4:** LED (Footprint: LED\_SMD:LED\_0402\_1005Metric)  
  * **Q2:** NPN Transistor (MMBT2222A) (Footprint: Package\_TO\_SOT\_SMD:SOT-23)  
  * **R14:** 1kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
  * **R17:** 4.7kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
* **Connection Instructions:**  
  1. **Debug Jumper:** Connect **Pin 1** of **J6** to \+3.3V, and **Pin 2** to **GPIO15** of **U1**.  
  2. **Pull-down Resistor:** Connect **R16** between **GPIO15** of **U1** and GND.  
  3. **LED Driver:** Connect **R14** between \+3.3V and the **Anode** of **D4**. Connect the **Cathode** of **D4** to the **Collector** of **Q2**. Connect the **Emitter** of **Q2** to GND. Connect **R17** between **GPIO15** of **U1** and the **Base** of **Q2**.

#### **1.7.2. ESP32 \#2 Debug Interface**

* **Component List:**  
  * **J9:** Conn\_01x02\_Male (U2\_DEBUG) (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_1x02\_P2.54mm\_Vertical)  
  * **R\_DBG2\_PD:** 10kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
  * **D3:** LED (Footprint: LED\_SMD:LED\_0402\_1005Metric)  
  * **Q1:** NPN Transistor (MMBT2222A) (Footprint: Package\_TO\_SOT\_SMD:SOT-23)  
  * **R\_DBG2\_LED:** 1kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
  * **R\_DBG2\_BASE:** 4.7kΩ Resistor (Footprint: Resistor\_SMD:R\_0402\_1005Metric)  
* **Connection Instructions:**  
  1. **Debug Jumper:** Connect **Pin 1** of **J9** to \+3.3V, and **Pin 2** to **GPIO13** of **U2**.  
  2. **Pull-down Resistor:** Connect R\_DBG2\_PD between **GPIO13** of **U2** and GND.  
  3. **LED Driver:** Connect R\_DBG2\_LED between \+3.3V and the **Anode** of **D3**. Connect the **Cathode** of **D3** to the **Collector** of **Q1**. Connect the **Emitter** of **Q1** to GND. Connect R\_DBG2\_BASE between **GPIO13** of **U2** and the **Base** of **Q1**.

### **1.8. Part 7: General Purpose I/O (GPIO) Breakouts**

#### **1.8.1. ESP32 \#1 GPIO Breakout**

* **Component List:**  
  * **J7 (GPIO\_EXT\_U1):** Conn\_02x05\_Male (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_2x05\_P2.54mm\_Vertical)  
* **Connection Instructions:**  
  * Connect **Pin 1** of **J7** to \+3.3V; **Pin 2** to GND.  
  * **J7 Pin 3:** U1 GPIO4  
  * **J7 Pin 4:** U1 GPIO5  
  * **J7 Pin 5:** U1 GPIO13  
  * **J7 Pin 6:** U1 GPIO14  
  * **J7 Pin 7:** U1 GPIO13  
  * **J7 Pin 8:** U1 GPIO32  
  * **J7 Pin 9:** U1 GPIO33  
  * **J7 Pin 10:** GND

#### **1.8.2. ESP32 \#2 GPIO Breakout**

* **Component List:**  
  * **J8 (GPIO\_EXT\_U2):** Conn\_02x08\_Male (Footprint: Connector\_PinHeader\_2.54mm:PinHeader\_2x08\_P2.54mm\_Vertical)  
* **Connection Instructions:**  
  * Connect **Pin 1** of **J8** to \+3.3V; **Pin 2** to GND.  
  * **J8 Pin 3:** U2 GPIO4  
  * **J8 Pin 4:** U2 GPIO5  
  * **J8 Pin 5:** U2 GPIO13  
  * **J8 Pin 6:** U2 GPIO14  
  * **J8 Pin 7:** U2 GPIO15  
  * **J8 Pin 8:** U2 GPIO18  
  * **J8 Pin 9:** U2 GPIO19  
  * **J8 Pin 10:** U2 GPIO23  
  * **J8 Pin 11:** U2 GPIO25  
  * **J8 Pin 12:** U2 GPIO26  
  * **J8 Pin 13:** U2 GPIO27  
  * **J8 Pin 14:** U2 GPIO32  
  * **J8 Pin 15:** U2 GPIO33  
  * **J8 Pin 16:** GND
