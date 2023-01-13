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

# Demultiplexing sequencing data
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

# Estimating SC2 variant abundances using Freyja



# Sample statistics and SC2 variant profiles in R


