# Section 3: Basic data management in the cloud

Once your compute environment is set up, the question is how to access your data
for your analysis. In this short section we want to load some external data into
our virtual environment. Further, we will also search for more metagenomic
datasets via object storage.

## 3.1 Create a volume to store data in

1. Inspect what block storage is available on your virtual instance by typing:
   ``` bash
   df -h 
   ```
2. Check the current disk usage and available space.
3. In the SimpleVM portal, navigate to the Volumes section and create a volume for your VM.
   Enter your name without whitespace (Example: Max Mustermann -> MaxMusterman) as the volume name 
   and provide 10 GB as the volume size.
   ![](figures/createVolume.png)

4. Attach this volume to your instance by opening the pull down menu of your volume and
   clicking the green attach icon. Then select your virtual machine in the pop up menu and
   click the attach button.
   ![](figures/attachVolume.png)

5. Activate the newly created and attached volume in your VM:
   ``` bash
   lsblk
   sudo mkfs.ext4 /dev/vdc
   sudo mkdir /mnt/volume   
   sudo mount /dev/vdc /mnt/volume
   sudo chown ubuntu:ubuntu /mnt/volume
   lsblk   
   ```
6. Navigate to the newly created volume and activate the conda environment:
   ``` bash
   cd /mnt/volume
   ```
   Additionally, we will install a small helper tool to access S3 storage directly:
   ``` bash
   wget https://dl.min.io/client/mc/release/linux-amd64/mc
   ```
   Move it to a folder where other binaries usually are stored:
   ``` bash
   sudo mv mc /usr/local/bin/
   ```
   Change file permissions:
   ``` bash
   sudo chmod a+x /usr/local/bin/mc
   ```
   
## 3.2 Public available data

In our use case, we want to search for some pathogenic factors in metagenomic data.
Metagenomic data is public available e.g. in the Short Read Archive (SRA) hosted by the
National Center for Biotechnology Information. We can search here for specific data sets, e.g.
for metagenomic data that were generated with a specific sequencing instrument for a specific 
type of experiment.

1. Go to:
   https://www.ncbi.nlm.nih.gov/sra
2. And enter in the search field the following search pattern:
   ``` bash
   blood culture AND ((((((Oxford Nanopore[Platform]) AND METAGENOMIC[Source]) AND WGS[Strategy]) AND SINGLE[Layout]) AND RANDOM[Selection]))
   ```
3. Let's have a look on (one of) the first result:
   https://www.ncbi.nlm.nih.gov/sra/?term=SRR24962458

4. These data sets can be downloaded in different ways, e.g. using the public available
   FTP server:
   ``` bash
   wget ftp://ftp.sra.ebi.ac.uk/vol1/srr/SRR249/058/SRR24962458/SRR24962458
   ```
5. This data is still in a special format that needs to be converted in order to be inspected
   for specific virulence factors:
   ``` bash
   fasterq-dump --skip-technical SRR24962458
   ```
6. We will also download a sequence information of known pathogens. We created a mash index out of selected genomes
   that were classified as  "greatest threat to human health" by the World Health Organisation (WHO) in 2017:
   https://www.who.int/news/item/27-02-2017-who-publishes-list-of-bacteria-for-which-new-antibiotics-are-urgently-needed 
   Please download the index:
   ``` bash
   wget https://openstack.cebitec.uni-bielefeld.de:8080/simplevm-workshop/genomes.msh
   ```
7. Now we will screen the metagenomic data for parts of sequences belonging to those known pathogens:
   ``` bash
   mash screen -p 12 genomes.msh SRR24962458.fastq
   ```   
## 3.3 Cloud enabled data
Using FTP for file transfer is not the ideal choice, as it lacks features such as encryption, compression,
and error checking, making it slower and less secure than alternative protocols. In cloud environments,
the S3 protocol has become a de facto standard because it offers high-performance, secure, and reliable object
storage with built-in features such as versioning, bucket policies, and multi-part uploads, making it well-suited
for big data transfer and analytics workloads. For this reason, many scientific important data is already provided in 
some cloud object storage, for instance at the AWS cloud by Amazon.

1. Let's see what we can find at the AWS marketplace. In a browser, navigate to:
   [AWS marketplace](https://aws.amazon.com/marketplace/search/results?trk=8384929b-0eb1-4af3-8996-07aa409646bc&sc_channel=el&FULFILLMENT_OPTION_TYPE=DATA_EXCHANGE&CONTRACT_TYPE=OPEN_DATA_LICENSES&filters=FULFILLMENT_OPTION_TYPE%2CCONTRACT_TYPE)
2. Let's search if we can find the SRA database that we used previously for FTP download by search for the term 'sra'.
3. S3 data can be directly accessed via web protocols, such as HTTPS (e.g. by using a browser or wget). However,
   for convenient data management a client that speaks S3 is necessary. We will use the minio client for this. We have 
   already downloaded the client previously.    
   ``` bash
   cd /mnt/volume   
   ```
4. Next we need to tell minio where the AWS cloud storage can be accessed and what
   the access key and secret is.
   ``` bash
   mc alias set aws https://s3.amazonaws.com "" ""
   ```
5. Let's see if we can find our data that we used previously:
   ``` bash
   mc ls --summarize aws/sra-pub-run-odp/sra/SRR24962458
   ```
6. Now lets do the same analysis as before but this time using the AWS SRA mirror. First
   we prepare a folder:
   ``` bash
   cd /mnt/volume
   mkdir aws
   cd aws
   ```
   Then we download the data:
   ``` bash
   mc cp  aws/sra-pub-run-odp/sra/SRR24962458/SRR24962458 .
   ```
   And we search again for pathogens:
   ``` bash
   fasterq-dump -Z --skip-technical --concatenate-reads SRR24962458 | mash screen -p 12 ../genomes.msh -
   ```

## 3.4 Make the analysis where the data is located

For operating on large datasets, accessing remote storage can be a bottleneck. For this reason, one of the cloud 
paradigms is called Data Gravity: The idea that data is a massive attractor and should be processed as close to
its source as possible, reducing the need for expensive data movement. For this reason, the de.NBI Cloud at Bielefeld
site holds a mirror of all metagenomic data of the SRA that we will use now.
   
1. Click on `Terminal` in the upper menu and select `New Terminal`.
   ![](figures/open_terminal.png)

2. Activate the conda environment by running:
   ``` bash
   conda activate denbi
   cd /mnt/volume   
   ```
3. Add S3 config for our public SRA mirror on our Bielefeld Cloud site:
   ``` bash
   mc alias set sra https://openstack.cebitec.uni-bielefeld.de:8080 "" ""
   ```

4. List which files are available for a specific SRA number, e.g. for `SRR3984908`:
   ``` bash
   mc ls sra/ftp.era.ebi.ac.uk/vol1/fastq/SRR398/008/SRR3984908
   ```
   
5. We will also download a sequence information of known pathogens. We created a mash index out of selected genomes
   that were classified as  "greatest threat to human health" by the World Health Organisation (WHO) in 2017:
   https://www.who.int/news/item/27-02-2017-who-publishes-list-of-bacteria-for-which-new-antibiotics-are-urgently-needed 
   Please download the index:
   ``` bash
   wget https://openstack.cebitec.uni-bielefeld.de:8080/simplevm-workshop/genomes.msh
   ```
   
6. We created a file that points to metagenomic datasets that you have found in the previous chapter.
   Download the input file via:
   ``` bash
   wget https://raw.githubusercontent.com/deNBI/simpleVMWorkshopShort/EscienceDays2025/reads.tsv
   ```
   You can inspect the file by using `cat`:
   ``` bash
   cat reads.tsv
   ```
7. We will create a directory for the output for the following command. We will place an output
   file for every SRA ID.
   ``` bash
   mkdir -p output
   ```

8. You can now run the commands from the first part with found datasets as input (this may take a while to complete):
   Create a function that we will run in parallel:
   ``` bash
   search(){ 
      left_read=$(echo $1 | cut -d ' '  -f 1);  
      right_read=$(echo $1 | cut -d ' ' -f 2); 
      sra_id=$(echo ${left_read} | rev | cut -d '/' -f 1 | rev | cut -d '_' -f 1 | cut -d '.' -f 1);
      mc cat $left_read $right_read | mash screen -p 3 genomes.msh - \
          | sed "s/^/${sra_id}\t/g"  \
          | sed 's/\//\t/' > output/${sra_id}.txt ;
   }
   ```
   <details><summary>Show Explanation</summary>
   In order to understand what this function does let's take the following datasets as an example:
   <code>
   sra/ftp.era.ebi.ac.uk/vol1/fastq/SRR643/001/SRR6439511/SRR6439511_1.fastq.gz    sra/ftp.era.ebi.ac.uk/vol1/fastq/SRR643/001/SRR6439511/SRR6439511_2.fastq.gz
   </code>
   where
      
    * `left_read` is left file (`sra/ftp.era.ebi.ac.uk/vol1/fastq/SRR643/001/SRR6439511/SRR6439511_1.fastq.gz`)
    * `right_read` is the right file (`sra/ftp.era.ebi.ac.uk/vol1/fastq/SRR643/001/SRR6439511/SRR6439511_2.fastq.gz`)
    * `sra_id` is the prefix of the file name (`SRR6439511`)
    * `mc cat` streams the files into `mash screen` which is using the sketched genomes `genomes.msh`
       to filter the datasets.
    * Both `sed`s are just post-processing the output and place every match in the `output` folder.

   </details>
   
   Export this function, so that we can use it in the next command.
   ``` bash
   export -f search
   ```
   We can run this function now on the datasets that are defined in the reads.tsv list in parallel:
   ``` bash
   cat reads.tsv | parallel search
   ```
   where
     * `reads.tsv` is a list of datasets that we want to scan.
     * `search` is the function that we want to call.

9. Optional: This command will run a few minutes. You could open a second terminal
   and inspect the cpu utilization with `htop`.
   ![](figures/htop.png)

10. Concatenate all results into one file via 
   ``` bash
   cat output/*.txt > output.tsv
   ```

Back to [Section 2](Part2.md) | Next to [Section 4](Part4.md)
