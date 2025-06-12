# **Dual ESP32 Modbus TCP to Wi-Fi Bridge**

This repository contains the KiCad project files and design documentation for a custom PCB that acts as an industrial data bridge. The board reads data from a PLC via Modbus TCP over Ethernet and transmits it to a web server via Wi-Fi.

## **High-Level Functionality**

The board is designed around a dual-ESP32 architecture to handle network tasks concurrently and without interference:

* **ESP32 \#1 (Modbus Master):**  
  * Connects to an industrial network via a hardwired Ethernet (RJ45) port.  
  * Communicates with a PLC using the Modbus TCP protocol to read register values.  
  * Transfers the retrieved data internally to the second ESP32.  
* **ESP32 \#2 (Web Client):**  
  * Receives data from the first ESP32 via a high-speed UART link.  
  * Connects to a local Wi-Fi network.  
  * Formats and sends the data to a specified web server endpoint using HTTPS POST requests.  
  * Drives a small I2C OLED display for real-time status monitoring (e.g., IP address, connection status, last sync time).

## **Key Hardware Features**

* **Dual ESP32-WROOM-32D Sockets:** For parallel processing of networking tasks.  
* **Robust Power Supply:** On-board 24V to 3.3V DC-DC buck converter to draw power directly from a PLC or industrial power rail.  
* **Wired & Wireless Connectivity:** Features a Hanrun HR911105A RJ45 port for Ethernet and integrated Wi-Fi/Bluetooth on the ESP32s.  
* **On-Board Debugging:** Includes FTDI programming headers, manual boot/reset buttons, and debug jumpers with status LEDs for each ESP32.  
* **I/O & Expansion:** Provides breakout headers for unused GPIO pins for future expansion and an I2C header for the status display.  
* **High-Density Design:** Utilizes 0402 package size for passive components to maintain a small form factor.

## **Full Documentation**

This README provides a brief overview. For complete technical details, including the full schematic specification, component list, bill of materials (BOM), and PCB layout guidelines, please refer to the comprehensive design document:

➡️ View the Complete Design & Manufacturing Specification  
(Note: Replace the link above with the actual URL to your hosted specification document.)

## **Project Files**

* **/Hardware**: Contains the KiCad project files (.pro, .sch, .kicad\_pcb).  
* **/Documentation**: Contains the full design specification PDF and other supporting documents.  
* **/Firmware**: Contains example Arduino/ESP-IDF source code for both ESP32 modules.  
* **/Gerbers**: Contains the final manufacturing files for PCB fabrication.

*This project is designed for industrial applications where reliability and clear diagnostics are critical. Please review the full documentation before fabrication or assembly.*
