# Section 7: Advanced Data Transfer Methods (Optional)

This section covers advanced data transfer techniques for moving data efficiently between cloud instances, object storage containers, and hybrid environments. These methods are particularly useful for large-scale data processing workflows and collaborative research projects.

## 7.1 Advanced Object Storage Operations

### 7.1.1 Cross-region data replication

For data redundancy and disaster recovery, you can replicate data across different regions:

``` bash
# Configure multiple regions
mc config host add UniBi https://openstack.cebitec.uni-bielefeld.de:8080/ <ACCESS-KEY> <SECRET-KEY>
mc config host add UniHe https://denbi-cloud.bioquant.uni-heidelberg.de:8080/ <ACCESS-KEY> <SECRET-KEY>

# Replicate data between regions
mc mirror ibworkshop/YOUR_CONTAINER_NAME UniHe/BACKUP_CONTAINER/
```

### 7.1.2 Batch operations with minio client

For processing multiple files efficiently:

``` bash
# Upload multiple file types with wildcards
mc cp ~/data/*.{fastq,fna,fa} ibworkshop/YOUR_CONTAINER_NAME/raw_data/

# Download files matching specific patterns
mc find ibworkshop/YOUR_CONTAINER_NAME --name "*.fastq.gz" --exec "mc cp {} ~/downloads/"

# Sync directories with progress monitoring
mc mirror --progress ~/local_data ibworkshop/YOUR_CONTAINER_NAME/synced_data/
```

### 7.1.3 Data versioning and lifecycle management

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

Rsync is more efficient than scp for large datasets as it only transfers differences:

``` bash
# Sync directories between instances
rsync -avz -e "ssh -p YOUR_PORT" ubuntu@YOUR_VM_IP:/mnt/volume/data/ ~/local_backup/

# Transfer with compression and progress
rsync -avzP -e "ssh -p YOUR_PORT" ~/large_dataset/ ubuntu@YOUR_VM_IP:/mnt/volume/

# Exclude certain file types
rsync -avz --exclude="*.tmp" --exclude="*.log" -e "ssh -p YOUR_PORT" ~/data/ ubuntu@YOUR_VM_IP:/mnt/volume/
```

### 7.2.2 SSHFS for seamless file system access

Mount remote directories as local file systems:

``` bash
# Mount remote directory locally
sshfs -p YOUR_PORT ubuntu@YOUR_VM_IP:/mnt/volume ~/remote_mount

# Now you can access remote files as if they were local
ls ~/remote_mount
cp ~/remote_mount/file.txt ~/local_dir/p
### 7.2.3 Parallel transfers with GNU parallel

For transferring many files simultaneously:

``` bash
# Create a list of files to transfer
find ~/data -name "*.fastq" > files_to_transfer.txt

# Transfer files in parallel (4 at a time)
parallel -j 4 -a files_to_transfer.txt scp -i ~/.ssh/*.pem -P YOUR_PORT {} ubuntu@YOUR_VM_IP:/mnt/volume/
```

## 7.3 Hybrid Transfer Methods

### 7.3.1 Object storage to VM transfers

Transfer data from object storage directly to VMs:

``` bash
# Download from object storage to VM
mc cp ibworkshop/YOUR_CONTAINER_NAME/large_file.fastq.gz ~/

# Stream data directly to processing pipeline
mc cat ibworkshop/YOUR_CONTAINER_NAME/data.fastq.gz | gunzip | head -n 1000

# Upload processed results back to object storage
cat processed_results.txt | mc pipe ibworkshop/YOUR_CONTAINER_NAME/results/processed_results.txt
```

### 7.3.2 Multi-step data pipelines

Create automated data processing pipelines:

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

### 7.4.1 Transfer speed optimization

``` bash
# Use multiple connections for faster transfers
mc cp --concurrent 4 large_file.gz ibworkshop/YOUR_CONTAINER_NAME/

# Compress data during transfer
rsync -avz --compress-level=9 -e "ssh -p YOUR_PORT" ~/data/ ubuntu@YOUR_VM_IP:/mnt/volume/

# Use faster encryption algorithms
scp -c aes128-gcm@openssh.com -i ~/.ssh/*.pem -P YOUR_PORT file.txt ubuntu@YOUR_VM_IP:~/
```

### 7.4.2 Monitoring and logging

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

``` bash
# Generate checksums before transfer
md5sum large_file.txt > checksums.txt

# Verify after transfer
mc cat ibworkshop/YOUR_CONTAINER_NAME/large_file.txt | md5sum

# Compare with original
diff <(cat checksums.txt | cut -d' ' -f1) <(mc cat ibworkshop/YOUR_CONTAINER_NAME/large_file.txt | md5sum | cut -d' ' -f1)
```

### 7.5.2 Resume interrupted transfers

``` bash
# Resume interrupted rsync transfers
rsync -avz --partial --progress -e "ssh -p YOUR_PORT" ~/large_dataset/ ubuntu@YOUR_VM_IP:/mnt/volume/

# Resume interrupted minio transfers
mc cp --continue ibworkshop/YOUR_CONTAINER_NAME/large_file.gz ~/
```

Back to [Section 6](Part6.md)
