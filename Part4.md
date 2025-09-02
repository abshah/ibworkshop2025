# Section 4: Data Transfer

We have now set up everything to actually transfer some data.
There are different tools for copying files from/to the de.NBI Cloud each for different use cases. Under Linux you can choose between different command line tools. Some of them can also be used with a graphical user interface (GUI) by using a file manager. For Windows users it may be best to use a GUI tool like WinSCP. Some good Linux tools for this use case are:

    1. scp -- secure copy (remote file copy program)
    2. sshfs -- filesystem client based on ssh
    3. rsync -- a fast, versatile, remote (and local) file-copying tool

## 4.1 Simple transfer using scp

In the image of the SimpleVM instances there is already some data located in your HOME directory. We will demonstrate how to copy data between your local machine and the cloud instance, or between different cloud instances if you have multiple VMs.

``` bash
 # Navigate to the data directory
 cd ~/data/genomes
 
 # Copy files to a remote instance
 # Replace YOUR_PORT with your actual SSH port (shown in SimpleVM Instance Overview)
 # Replace YOUR_VM_IP with your VM's IP address
 scp -i ~/.ssh/*.pem -P YOUR_PORT *.fna ubuntu@YOUR_VM_IP:~/data_remote
 ```
![](figures/scp.png)

 You should now see how the data is copied. To verify that the data is present on the remote instance:

``` bash
 # Connect to the remote instance
 ssh -i ~/.ssh/*.pem -p YOUR_PORT ubuntu@YOUR_VM_IP
 
 # Check the transferred files
 cd data_remote
 ls -la
 ```
![](figures/scp_remote.png)

## 4.2 Using volumes for data transfer

If you need to transfer large amounts of data, you should use volumes as described in [Section 3.1](Part3.md#31-create-a-volume-to-store-data-in). 

Once you have a volume mounted at `/mnt/volume` (as shown in Section 3.1), you can transfer data to it:

``` bash
# Example: Copy data to your mounted volume
scp -i ~/.ssh/*.pem -P YOUR_PORT local_file.txt ubuntu@YOUR_VM_IP:/mnt/volume/

# Or copy from the volume to your local machine
scp -i ~/.ssh/*.pem -P YOUR_PORT ubuntu@YOUR_VM_IP:/mnt/volume/file.txt ./
```

**Note**: Replace `YOUR_PORT` with the actual SSH port assigned to your VM, and `YOUR_VM_IP` with your VM's IP address. You can find these values in the SimpleVM Instance Overview page.

Back to [Section 3](Part3.md) | Next to [Section 5](Part5.md)
