# Integrative Bioinformatics Conference Workshop 2025: RDM Meets Cloud Workshop

8-9 September 2025 This hands-on workshop introduces cloud computing and
data management using the de.NBI Cloud infrastructure with a focus on
metagenomic data analysis.

## Workshop Overview

In this workshop, you will learn how to: - Set up and manage virtual
machines (VMs) in the de.NBI Cloud using SimpleVM - Work with different
storage options (volumes and object storage) - Analyze metagenomic data
using cloud resources - Transfer data efficiently between local and
cloud, and between cloud environments - Use the full OpenStack Horizon
interface (optional advanced section) - Implement advanced data transfer
methods for large-scale workflows

## Prerequisites

### For Workshop Participants

-   A modern web browser (Chrome, Firefox, or Edge recommended)
-   Basic familiarity with Linux command line
-   de.NBI Cloud account (instructions provided in Part 1)

### Software Installation During the Workshop

**Pre-installed via Conda (selected during VM creation):**

-   **mash (2.2)**: Fast genome and metagenome distance estimation

-   **GNU parallel (20220922)**: For parallel job execution

-   **sra-tools (3.1.1)**: Includes fasterq-dump for SRA data conversion

-   **Installed during the workshop:** - **minio client (mc)**:
    Downloaded and installed manually for object storage operations -
    **Additional tools**: Various Linux packages installed as needed
    (e.g., fortune-mod, cowsay, OpenStack Client)

**Note**: All conda tools are activated using `conda activate denbi`
command.

## Workshop Structure

This workshop is divided into four sections:

1.  [**Part 1: First Contact with the de.NBI Cloud**](Part1.md)
    -   Creating a de.NBI Cloud account
    -   Setting up SSH keys
    -   Joining the workshop project
    -   Understanding the tools and prerequisites
    -   Starting a VM with pre-selected conda tools (mash, parallel,
        sra-tools)
2.  [**Part 2: Verify your VM Properties and Tools**](Part2.md)BullSequana XH3000
    -   Logging into your VM via Guacamole (browser-based access)
    -   Verifying VM resources (CPU, RAM)
    -   Testing pre-installed conda tools
    -   Installing additional Linux packages
3.  [**Part 3: Basic Data Management in the Cloud**](Part3.md)
    -   Creating and mounting volumes for persistent storage
    -   Installing minio client for object storage operations
    -   Downloading and analyzing public metagenomic data from SRA
    -   Working with S3 object storage using minio client
    -   Running parallel analyses on cloud resources
4.  [**Part 4: Using the Object Storage**](Part4.md)
    -   Creating OpenStack application credentials
    -   Setting up S3 credentials for object storage
    -   Uploading data to object storage buckets
    -   Advanced object storage operations (cross-region replication,
        batch operations)
    -   Advanced SSH-based transfers (rsync, parallel transfers)
    -   Performance optimization and data integrity verification

## Key Concepts Covered

-   **SimpleVM**: Simplified interface for managing virtual machines in
    the de.NBI Cloud
-   **Volumes**: Persistent block storage that can be attached to VMs
-   **Object Storage (S3)**: Scalable storage for unstructured data
-   **Data Gravity**: Processing data close to where it's stored to
    minimize transfer overhead

    ## Support and Resources

-   de.NBI Cloud Wiki: <https://cloud.denbi.de/wiki/>
-   de.NBI Cloud Portal: <https://cloud.denbi.de/portal/>
-   Workshop invitation link: Available in Part 1

## Notes

-   All VMs in this workshop are accessed via browser (Guacamole) - no
    local SSH client required
-   The workshop primarily uses SimpleVM interface (Parts 1-3)
-   Part 4 covers advanced object storage operations and data transfer
    methods
-   Conda tools (mash, parallel, sra-tools) are pre-installed during VM
    creation
-   Minio client and other tools are installed during the workshop as
    needed
-   Workshop organizers have SSH access to help troubleshoot issues
