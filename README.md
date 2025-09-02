# Integrative Bioinformatics Conference Workshop: RDM Meets Cloud Workshop

This hands-on workshop introduces cloud computing and data management using the de.NBI Cloud infrastructure with a focus on metagenomic data analysis.

## Workshop Overview

In this workshop, you will learn how to:
- Set up and manage virtual machines (VMs) in the de.NBI Cloud using SimpleVM
- Work with different storage options (volumes and object storage)
- Analyze metagenomic data using cloud resources
- Transfer data efficiently between local and cloud, and between cloud environments

## Prerequisites

### For Workshop Participants
- A modern web browser (Chrome, Firefox, or Edge recommended)
- Basic familiarity with Linux command line
- de.NBI Cloud account (instructions provided in Part 1)

### Software Pre-installed in the Workshop VMs
The following tools are available in the workshop VMs via conda environment:
- **mash**: Fast genome and metagenome distance estimation
- **GNU parallel**: For parallel job execution
- **fasterq-dump**: SRA Toolkit for data conversion
- **minio client (mc)**: Object storage operations
- **OpenStackClient**: For advanced cloud operations

## Workshop Structure

This workshop is divided into five progressive sections:

1. **[Part 1: First Contact with the de.NBI Cloud](Part1.md)**
   - Creating a de.NBI Cloud account
   - Setting up SSH keys
   - Joining the workshop project
   - Understanding the tools and prerequisites

2. **[Part 2: Verify your VM Properties and Tools](Part2.md)**
   - Logging into your VM via Guacamole (browser-based access)
   - Verifying VM resources (CPU, RAM)
   - Testing pre-installed tools
   - Installing additional software

3. **[Part 3: Basic Data Management in the Cloud](Part3.md)**
   - Creating and mounting volumes for persistent storage
   - Downloading and analyzing public metagenomic data from SRA
   - Working with S3 object storage using minio client
   - Running parallel analyses on cloud resources

4. **[Part 4: Data Transfer](Part4.md)**
   - Using scp, sshfs, and rsync for data transfer
   - Transferring data between local and cloud environments
   - Working with volumes for large data transfers

5. **[Part 5: Using the Object Storage](Part5.md)**
   - Creating OpenStack application credentials
   - Setting up S3 credentials
   - Uploading data to object storage
   - Advanced object storage operations

## Key Concepts Covered

- **SimpleVM**: Simplified interface for managing virtual machines in the de.NBI Cloud
- **Volumes**: Persistent block storage that can be attached to VMs
- **Object Storage (S3)**: Scalable storage for unstructured data
- **Data Gravity**: Processing data close to where it's stored to minimize transfer overhead
- **Metagenomic Analysis**: Searching for pathogenic sequences in metagenomic datasets

## Support and Resources

- de.NBI Cloud Wiki: <https://cloud.denbi.de/wiki/>
- de.NBI Cloud Portal: <https://cloud.denbi.de/portal/>
- Workshop invitation link: Available in Part 1

## Notes

- All VMs in this workshop are accessed via browser (Guacamole) - no local SSH client required
- The workshop uses SimpleVM interface (not the full OpenStack Horizon dashboard)
- Data and tools are pre-configured in the workshop VMs 

