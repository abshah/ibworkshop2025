# Integrative Bioinformatics Conference Workshop: RDM Meets Cloud Workshop

This short hands-on tutorial covers the CLI commands used for data management in the de.NBI Cloud session.

## Prerequisites

- System based on Linux or OSX or Windows Subsystem for Linux
- Required software packages 
  - Python > 3.6  
  - openssh   
  - pip
- [OpenstackClient](https://pypi.org/project/python-openstackclient/)
- [SwiftClient](https://pypi.org/project/python-swiftclient/) (not mandatory)
- Openstack API access 

## What will happen...

The goal of this small session is to set up a virtual machine on de.NBI cloud. One time using SimpleVM and another time using the full fledged openstack system (horizon). We will then use both of these instances to transfer data into the cloud using different techniques and storage options. This Session is divided into different parts:

1. [Section 1](Part1.md) covers setting up a LifeScience Account and starting a VM for the first time. 
2. [Section 2](Part2.md) will introduce how to verify the running instance. 
3. In [Section 3](Part3.md) we will start the second VM using the horizon interface. 
4. [Section 4](Part4.md) covers simple data transfer and volumes. 
5. In [Section 5](Part5.md) we will use the S3 Object Storage. 

