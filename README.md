# SARS-CoV-2-Wastewater-sequencing-Analysis-Pipeline
Instructions on analysis of amplicon-based sequencing using paragon genomic kit on a nextseq550 

------------
# Packages required:
- cutadapt 3.5 or above
- bwa 0.7.17 or above
- samtools 1.10
- fgbio 1.4 or above
- freyja newest version
- ivar 1.3.1 or above
- BaseSpace Sequence Hub CLI tool 
- bcl2fastq 2.19.0.316 or above

# Demultiplexing Sequence Data
The genomics center will share the run with you via your basespace account, which you must accept (run & project). Download the finished sequencing run using Basespace CLI. We usually download and demultiplex the data on scratch, then moved the fastq's to our personal folder for analysis.
```
bs list runs
```
Find the run id you want to analyze and download the run to the server
```
bs download run -i {runID} -o {output_directory_name}
```
We need to demultiplex the data and generate fastq files. You will need the samplesheet for the run to do this.
```
./autobcl2fastq.sh {downloaded_basespace_run_folder} {output_directory_name} {samplesheet} Y151,I8,I8,Y151
```
This will demultiplex and generate the fastqs.
```
cd {fastq_folder}/{samplerun_prefix} 
```
```
mv */* .
```
```
mkdir {monthyear_runletter} 
```
Depending on the number of samples, you can either move all the fastqs into one new folder or break up the fastqs to different folders, which you will process on different nodes.
```
mv *.fastq.gz {monthyear_runletter_folder} 
```
Move the folder containing the fastqs to your directory for analysis. Make sure you have the SC2 reference fasta, the bwa indices of the SC2 reference, primer_tab file, which is used to mask primers sequences used to generate the amplicon-based genomes.

# Processing Raw Sequencing Data
```
./sentinal.sh {monthyear_runletter_folder} {output_directory_name} {SC2 bwa indices} {primer_tab_file}
```
This script will trim/filter the fastqs, map them to the SC2 genome, mask primers, call variants, determine coverage and depth.
You will need to transfer the cov folder, depth folder and all_filtered.tsv file to computer that has R.

# Estimating SC2 Variant Abundances using Freyja
You need to move your bam files from your filtered_bam folder to a new folder. I usually make a folder called bam and move the bam files there. Next you need to move the frejya scripts and barcode file to the bam folder.
```
./frey1.sh
```
You need to update the barcode file periodically, so you need to change the frey2.sh script at the "--barcode {jan-bc.csv}" part and give it the new barcode file.
```
./frey2.sh
```
Make a new folder called demix and move all the demix files to the demix folder.
```
freyja aggregate --output {samplerunname.tsv} ./
```
This will create the frejya file that you will need to further process to generate the plots.
Transfer the aggregate file to your computer and replace the sample names with the description names. Save the file or files. The aggregate file has all the samples results, so we can split the results based on site and create a site specific file. Transfer the new file/s back to the server to the demix folder.
```
./frey3.sh
```
This will generate the SC2 estimated abundances plots. 

# Sample Statistics and SC2 Variant Profiles in R


