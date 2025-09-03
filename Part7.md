# Section 7: Advanced Data Transfer Methods (Optional)

This section covers advanced data transfer techniques for moving data
efficiently between cloud instances, object storage containers, and
hybrid environments. These methods are particularly useful for
large-scale data processing workflows and collaborative research
projects.

## 7.1 Advanced Object Storage Operations

### 7.1.1 Cross-region data replication

**Use Case**: You're collaborating with researchers at different de.NBI
sites (Bielefeld, Heidelberg, Geissen, Berlin, etc.) and need to share
large metagenomic datasets. Cross-region replication ensures data
availability and reduces transfer times.

**How it works**: Data is automatically synchronized between different
de.NBI cloud regions, providing redundancy and faster access for
collaborators. i Advantages:

\- **Disaster recovery**: If one region is unavailable, data remains
accessible

\- **Collaboration**: Multiple teams can work on the same datasets
simultaneously

``` bash
# Configure multiple regions
mc config host add UniBi https://openstack.cebitec.uni-bielefeld.de:8080/ <ACCESS-KEY> <SECRET-KEY>
mc config host add UniHe https://denbi-cloud.bioquant.uni-heidelberg.de:8080/ <ACCESS-KEY> <SECRET-KEY>

# Replicate data between regions
mc mirror ibworkshop/YOUR_CONTAINER_NAME UniHe/BACKUP_CONTAINER/
```

### 7.1.2 Batch operations with minio client

**Use Case**: You have hundreds of sequencing files from different
experiments that need to be organized and uploaded to object storage.
Manual upload would take hours and be error-prone.

**How it works**: Minio client can process multiple files simultaneously
using wildcards and patterns, with built-in progress monitoring and
error handling.

**Advantages:**

\- **Time savings**: Upload hundreds of files in one command instead of
individual uploads

\- **Error reduction**: Automated pattern matching reduces human errors

\- **Progress tracking**: Monitor large transfers with real-time
progress bars

``` bash
# Upload multiple file types with wildcards
mc cp ~/data/*.{fastq,fna,fa} ibworkshop/YOUR_CONTAINER_NAME/raw_data/

# Download files matching specific patterns
mc find ibworkshop/YOUR_CONTAINER_NAME --name "*.fastq.gz" --exec "mc cp {} ~/downloads/"

# Sync directories with progress monitoring
mc mirror --progress ~/local_data ibworkshop/YOUR_CONTAINER_NAME/synced_data/
```

### 7.1.3 Data versioning and lifecycle management

**Use Case**: You're processing metagenomic data through multiple
pipelines and parameters, and need to track changes and potentially roll
back to previous results if the runs fail.

**How it works**: Object storage can maintain multiple versions of the
same file, allowing you to track changes over time and restore previous
versions when needed.

**Advantages**:

\- **Reproducibility**: Track exactly which version of the pipeline or
analysis was used in each iteration

\- **Collaboration**: Multiple researchers can work on the same dataset
without conflicts

\- **Compliance**: Maintain audit trails for research data management

``` bash
# Enable versioning on a bucket
mc version enable ibworkshop/YOUR_CONTAINER_NAME

# List all versions of a file
mc ls --versions ibworkshop/YOUR_CONTAINER_NAME/file.txt

# Restore a specific version
mc cp --version-id "abc123" ibworkshop/YOUR_CONTAINER_NAME/file.txt ./restored_file.txt
```

## 7.2 Advanced SSH-based Transfers

### 7.2.1 Using rsync for efficient transfers

**Use Case**: You're regularly updating large datasets (like reference
genomes or annotation files) or need to make regular daily or weekly
backups.

**How it works**: Rsync compares file timestamps and sizes, transferring
only the differences between source and destination. This is especially
efficient for incremental backups and dataset updates.

**Advantages**:

\- **Time efficiency**: Updates take minutes instead of hours for large
datasets

\- **Resume capability**: Interrupted transfers can be resumed from
where they left off

\- **Compression**: Built-in compression reduces transfer size and time

``` bash
# Sync directories between instances
rsync -avz -e "ssh -p YOUR_PORT" ubuntu@YOUR_VM_IP:/mnt/volume/data/ ~/local_backup/

# Transfer with compression and progress
rsync -avzP -e "ssh -p YOUR_PORT" ~/large_dataset/ ubuntu@YOUR_VM_IP:/mnt/volume/

# Exclude certain file types
rsync -avz --exclude="*.tmp" --exclude="*.log" -e "ssh -p YOUR_PORT" ~/data/ ubuntu@YOUR_VM_IP:/mnt/volume/
```

### 7.2.2 SSHFS for seamless file system access

**Use Case**: You need to access files on multiple VMs simultaneously,
or want to use local tools (like text editors or visualization software)
to work with remote data without downloading everything.

**How it works**: SSHFS creates a virtual file system that makes remote
directories appear as local folders, allowing transparent access to
remote data.

**Advantages**:

\- **Transparency**: Work with remote files as if they were local

\- **Real-time access**: Changes on remote systems are immediately
visible locally

\- **Tool compatibility**: Use any local software with remote data

``` bash
# Mount remote directory locally
sshfs -p YOUR_PORT ubuntu@YOUR_VM_IP:/mnt/volume ~/remote_mount

# Now you can access remote files as if they were local
ls ~/remote_mount
cp ~/remote_mount/file.txt ~/local_dir/
```

### 7.2.3 Parallel transfers with GNU parallel

**Use Case**: You have thousands of sequencing files that need to be
transferred to the cloud, and sequential transfer would take days.

**How it works**: GNU parallel manages multiple transfer processes
simultaneously, utilizing available bandwidth and CPU cores efficiently.

**Advantages**:

\- **Speed**: Transfer multiple files simultaneously, utilizing more
resources, and improving transfer throughput.

\- **Monitoring**: Track progress of all transfers simultaneously

``` bash
# Create a list of files to transfer
find ~/data -name "*.fastq" > files_to_transfer.txt

# Transfer files in parallel (4 at a time)
parallel -j 4 -a files_to_transfer.txt scp -i ~/.ssh/*.pem -P YOUR_PORT {} ubuntu@YOUR_VM_IP:/mnt/volume/
```

## 7.3 Hybrid Transfer Methods

### 7.3.1 Object storage to VM transfers

**Use Case**: You need to analyze large genomic datasets stored in
object storage without exhausting VM disk space, or want to stream data
directly into bioinformatics pipelines.

**How it works**: Minio client can stream data directly from object
storage to processing tools, or pipe processed results back to storage
without intermediate files.

**Advantages**: - **Storage efficiency**: Process data without using VM
disk space - **Speed**: Stream data directly to processing pipelines -
**Cost savings**: Reduce VM storage costs by keeping data in object
storage - **Scalability**: Process datasets larger than available VM
storage

``` bash
# Download from object storage to VM
mc cp ibworkshop/YOUR_CONTAINER_NAME/large_file.fastq.gz ~/

# Stream data directly to processing pipeline
mc cat ibworkshop/YOUR_CONTAINER_NAME/data.fastq.gz | gunzip | head -n 1000

# Upload processed results back to object storage
cat processed_results.txt | mc pipe ibworkshop/YOUR_CONTAINER_NAME/results/processed_results.txt
```

### 7.3.2 Multi-step data pipelines

**Use Case**: You have a complex bioinformatics workflow that involves
downloading sequencing data, processing it with multiple analysis tools,
and uploading results. Automating this workflow saves time and reduces
errors.

**How it works**: Scripts can orchestrate entire data processing
workflows, from initial data retrieval to final result storage, with
automatic cleanup.

**Advantages**: - **Automation**: Reduce manual intervention and human
errors - **Reproducibility**: Ensure consistent processing across
multiple datasets - **Efficiency**: Process multiple datasets in
sequence without manual steps - **Resource management**: Automatic
cleanup prevents storage bloat

``` bash
#!/bin/bash
# Example pipeline: Download → Process → Upload

# 1. Download data from object storage
mc cp ibworkshop/YOUR_CONTAINER_NAME/raw_data/*.fastq.gz ~/processing/

# 2. Process data
for file in ~/processing/*.fastq.gz; do
    base=$(basename $file .fastq.gz)
    gunzip -c $file | mash screen -p 4 genomes.msh - > ~/processing/${base}_results.txt
done

# 3. Upload results back to object storage
mc cp ~/processing/*_results.txt ibworkshop/YOUR_CONTAINER_NAME/processed_results/

# 4. Clean up local files
rm ~/processing/*.fastq.gz ~/processing/*_results.txt
```

## 7.4 Performance Optimization

### 7.4.1 Transfer speed optimizatioBenefitsn

**Use Case**: You're transferring terabytes of sequencing data and need
to maximize transfer speeds to meet project deadlines or minimize cloud
costs.

**How it works**: Various techniques can be combined to optimize
transfer performance, including parallel connections, compression, and
optimized encryption.

**Advantages**: - **Time savings**: Reduce transfer times by 50-80%
through optimization - **Cost reduction**: Faster transfers mean less VM
time and lower costs - **Bandwidth utilization**: Make full use of
available network capacity - **Scalability**: Techniques work for
datasets of any size

``` bash
# Use multiple connections for faster transfers
mc cp --concurrent 4 large_file.gz ibworkshop/YOUR_CONTAINER_NAME/

# Compress data during transfer
rsync -avz --compress-level=9 -e "ssh -p YOUR_PORT" ~/data/ ubuntu@YOUR_VM_IP:/mnt/volume/

# Use faster encryption algorithms
scp -c aes128-gcm@openssh.com -i ~/.ssh/*.pem -P YOUR_PORT file.txt ubuntu@YOUR_VM_IP:~/
```

### 7.4.2 Monitoring and logging

**Use Case**: You're managing large-scale genomic data transfers and
need to track progress, identify bottlenecks, and maintain audit trails
for compliance or troubleshooting.

**How it works**: Transfer tools provide detailed monitoring
capabilities, including progress tracking, speed measurement, and
comprehensive logging.

**Advantages**: - **Visibility**: Real-time monitoring of transfer
progress and speeds - **Troubleshooting**: Detailed logs help identify
and resolve issues quickly - **Compliance**: Maintain audit trails for
research data management - **Optimization**: Identify bottlenecks and
optimize transfer strategies

``` bash
# Monitor transfer progress with detailed output
mc cp --progress --json large_file.gz ibworkshop/YOUR_CONTAINER_NAME/ | jq .

# Log all transfer activities
mc cp --log-file=transfers.log ibworkshop/YOUR_CONTAINER_NAME/file.txt ~/

# Check transfer speeds
time mc cp ibworkshop/YOUR_CONTAINER_NAME/large_file.gz ~/
```

## 7.5 Data Integrity and Verification

### 7.5.1 Checksum verification

**Use Case**: You're transferring critical sequencing data and need to
ensure data integrity. Even a single bit error in genomic data can
invalidate entire analyses.

**How it works**: Checksums provide mathematical fingerprints of files,
allowing you to verify that transferred data is identical to the
original.

**Advantages**: - **Data integrity**: Ensure transferred files are
identical to originals - **Error detection**: Catch transmission errors
that could corrupt research data - **Compliance**: Meet requirements for
data integrity in research workflows - **Confidence**: Verify successful
transfers before deleting local copies

``` bash
# Generate checksums before transfer
md5sum large_file.txt > checksums.txt

# Verify after transfer
mc cat ibworkshop/YOUR_CONTAINER_NAME/large_file.txt | md5sum

# Compare with original
diff <(cat checksums.txt | cut -d' ' -f1) <(mc cat ibworkshop/YOUR_CONTAINER_NAME/large_file.txt | md5sum | cut -d' ' -f1)
```

### 7.5.2 Resume interrupted transfers

**Use Case**: You're transferring large genomic datasets and the
connection is interrupted (network issues, VM restarts, etc.). Without
resume capability, you'd have to start over from the beginning.

**How it works**: Transfer tools can resume from where they left off,
using partial files and transfer logs to avoid re-transferring already
completed portions.

**Advantages**: - **Time savings**: Resume from interruption point
instead of starting over - **Bandwidth efficiency**: Don't waste
bandwidth on already transferred data - **Reliability**: Handle network
interruptions gracefully - **Cost savings**: Avoid repeated transfer
costs for large datasets

``` bash
# Resume interrupted rsync transfers
rsync -avz --partial --progress -e "ssh -p YOUR_PORT" ~/large_dataset/ ubuntu@YOUR_VM_IP:/mnt/volume/

# Resume interrupted minio transfers
mc cp --continue ibworkshop/YOUR_CONTAINER_NAME/large_file.gz ~/
```

Back to [Section 6](Part6.md)
