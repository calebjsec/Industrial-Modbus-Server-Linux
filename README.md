# Industrial Modbus Server Setup with Firewall Security and Web Management on Linux Server

This project demonstrates the setup of a Modbus server on a Linux (Ubuntu) VPS, configuring firewall rules for security, and creating a web panel for monitoring and managing Modbus communication. The project includes detailed steps, images, and documentation on compliance with SOX and NERC CIP standards.

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Setting Up the Modbus Server](#setting-up-the-modbus-server)
- [Firewall Configuration](#firewall-configuration)
- [Setting Up the Web Panel](#setting-up-the-web-panel)
- [Securing the Web Panel](#securing-the-web-panel)
- [Conclusion](#conclusion)
- [Additional Documentation](#additional-documentation)

## Introduction
This project involves setting up a Modbus server on a Linux server, applying firewall rules to secure the server, and creating a web panel to manage Modbus communication. This setup aligns with best practices for cybersecurity and regulatory compliance.

## Prerequisites

### 1. Hardware Requirements
- **Linux Server:** A Linux server (Ubuntu preferred) with:
  - **CPU:** 1 GHz or higher
  - **RAM:** 2 GB or more
  - **Disk Space:** 10 GB free space
- **Network Configuration:** Static IP address for consistent network communication.

### 2. Software Requirements
- **Operating System:** Ubuntu 20.04 LTS or later.

### 3. Required Python Packages
- **pymodbus:** For Modbus server functionality.
- **Flask:** For web panel.

### 4. Install Required Software
Open a terminal and run the following commands:

```bash
# Update package lists
sudo apt-get update

# Install Python3 and pip
sudo apt-get install python3 python3-pip

# Install required Python packages
pip3 install pymodbus flask
```
### Setting Up the Modbus Server

**1.1 Install the Modbus Server**

**Using modbus-server**

````bash
# Update package lists
sudo apt-get update

# Install modbus-server and dependencies
sudo apt-get install libmodbus-dev modbus-server
````
**Using Python and pymodbus**

**Install pymodbus:**

````bash
pip3 install pymodbus
````
Create a Modbus Server Script:

Create a file named modbus_server.py:

````bash
from pymodbus.server.sync import StartTcpServer
from pymodbus.device import ModbusDeviceIdentification
from pymodbus.datastore import ModbusSlaveContext, ModbusServerContext
from pymodbus.datastore import ModbusSequentialDataBlock

# Initialize data store
store = ModbusSlaveContext(
    di=ModbusSequentialDataBlock(0, [17]*100),
    co=ModbusSequentialDataBlock(0, [17]*100),
    hr=ModbusSequentialDataBlock(0, [17]*100),
    ir=ModbusSequentialDataBlock(0, [17]*100))
context = ModbusServerContext(slaves=store, single=True)

# Set up the server
identity = ModbusDeviceIdentification()
identity.VendorName = 'My Company'
identity.ProductCode = 'MP'
identity.VendorUrl = 'http://example.com'
identity.ProductName = 'Modbus Server'
identity.ModelName = 'Modbus Server Example'
identity.MajorMinorRevision = '1.0'

# Start the server
StartTcpServer(context, identity=identity, address=("0.0.0.0", 502))
````

