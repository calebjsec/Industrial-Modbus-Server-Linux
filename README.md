# Modbus-Linux-Firewall-Project

This project demonstrates the setup of a Modbus server on a Linux machine, the configuration of firewall rules for security, and the creation of a web panel for monitoring and managing Modbus communication. The project includes detailed steps, images, and documentation on compliance with SOX and NERC CIP standards.

## Table of Contents
- [Introduction](#introduction)
- [Setting Up the Modbus Server](#setting-up-the-modbus-server)
- [Firewall Configuration](#firewall-configuration)
- [Setting Up the Web Panel](#setting-up-the-web-panel)
- [Securing the Web Panel](#securing-the-web-panel)
- [Conclusion](#conclusion)

## Introduction
This project involves setting up a Modbus server on a Linux server, applying firewall rules to secure the server, and creating a web panel to manage Modbus communication. This setup aligns with best practices for cybersecurity and regulatory compliance.

## Setting Up the Modbus Server

### 1.1 Install the Modbus Server

**Using modbus-server:**
```bash
sudo apt-get update
sudo apt-get install libmodbus-dev modbus-server
