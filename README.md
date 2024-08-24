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

*Hardware Requirements*
- **Linux Server:** A Linux server (Ubuntu preferred) with:
  - **CPU:** 1 GHz or higher
  - **RAM:** 2 GB or more
  - **Disk Space:** 10 GB free space
- **Network Configuration:** Static IP address for consistent network communication.

*Software Requirements*
- **Operating System:** Ubuntu 20.04 LTS or later.

*Required Python Packages*
- **pymodbus:** For Modbus server functionality.
- **Flask:** For web panel.

Install Required Software*

Open a terminal and run the following commands:

```bash
# Update package lists
sudo apt-get update

# Install Python3 and pip
sudo apt-get install python3 python3-pip

# Install required Python packages
pip3 install pymodbus flask
```
### **Setting Up the Modbus Server**

*1.1 Install the Modbus Server*

*Using modbus-server*

````bash
# Update package lists
sudo apt-get update

# Install modbus-server and dependencies
sudo apt-get install libmodbus-dev modbus-server
````

*1.2 Install pymodbus*

````bash
pip3 install pymodbus
````

## Firewall Configuration

**2.1 Running Modbus over TCP**

*Create a Modbus Server Script*
*Create a file named modbus_server.py*

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
*Save this script as modbus_server.py and run it*

````
python3 modbus_server.py
````


**2.2 Using iptables**

*-A INPUT: Appends the rule to the INPUT chain.
-p tcp: Applies to TCP packets.
-s 217.20.242.107: Source IP address of the VPN.
--dport 80: Destination port for HTTP.
-j ACCEPT: Allows the packets.*

````
# Allow Incoming SSH Connections from the VPN IP Address
sudo iptables -A INPUT -p tcp -s 217.20.242.107 --dport 22 -j ACCEPT

# Allow Incoming Modbus TCP Connections from the VPN IP Address
sudo iptables -A INPUT -p tcp -s 217.20.242.107 --dport 502 -j ACCEPT

# Allow Incoming HTTP Connections from the VPN IP Address
sudo iptables -A INPUT -p tcp -s 217.20.242.107 --dport 80 -j ACCEPT

# Allow Incoming HTTPS Connections from the VPN IP Address
sudo iptables -A INPUT -p tcp -s 217.20.242.107 --dport 443 -j ACCEPT

# Block All Other Traffic to Ports 22, 80, 443, 502
sudo iptables -A INPUT -p tcp --dport 22 -j DROP
sudo iptables -A INPUT -p tcp --dport 80 -j DROP
sudo iptables -A INPUT -p tcp --dport 443 -j DROP
sudo iptables -A INPUT -p tcp --dport 502 -j DROP

# Save IP tables & make persistent
sudo apt-get install iptables-persistent
iptables-save
````
## Setting Up the Web Panel

**3.1 Install Flask**

````
pip3 install flask
````
**3.2 Create a Web Panel**

*Create the Web Panel Script*
*Create a file named web_panel.py*

````
from flask import Flask, render_template
from pymodbus.client.sync import ModbusTcpClient

app = Flask(__name__)

@app.route('/')
def index():
    client = ModbusTcpClient('127.0.0.1', port=502)
    client.connect()
    rr = client.read_holding_registers(0, 10, unit=1)
    client.close()
    return render_template('index.html', registers=rr.registers)

if __name__ == '__main__':
    app.run(debug=True)
````

**Create the HTML Template**

*Create a directory named templates and add a file named index.html inside it:*

````
<!DOCTYPE html>
<html>
<head>
    <title>Modbus Server Panel</title>
</head>
<body>
    <h1>Modbus Holding Registers</h1>
    <ul>
    {% for reg in registers %}
        <li>{{ reg }}</li>
    {% endfor %}
    </ul>
</body>
</html>
````

*Run the Flask Application*

````
python3 web_panel.py
````

*Access the web panel at http://serveripaddress:5000 in your browser*





