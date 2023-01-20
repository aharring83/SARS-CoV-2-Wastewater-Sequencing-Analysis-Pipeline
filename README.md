# SARS-CoV-2-Wastewater-sequencing-Analysis-Pipeline
Analysis of amplicon-based SC2 sequencing using Paragons kit on a Nextseq550 

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
Move the folder containing the fastqs to your directory for analysis. Make sure you have the SC2 reference fasta, the bwa indices of the SC2 reference, primer_tab file, which is used to mask primers sequences.

# Processing Raw Sequencing Data
I installed the packages in the base conda env for the computation group.
```
source ~opt/miniconda/bin/activate
```
```
./sentinal.sh {monthyear_runletter_folder} {output_directory_name} {SC2 bwa indices} {primer_tab_file} {freyja_barcode}
```
This script will trim/filter the fastqs, map them to the SC2 genome, mask primers, call and estimate variant abundances, determine coverage and depth.
You will need to transfer the cov folder, depth folder, freyja aggregate tsv and all_filtered.tsv file to computer that has R.

# Generating Freyja SC2 Variant Abundances Plots

Transfer the aggregate tsv file to your computer and replace the sample names with the description names. Save the file or files. The aggregate file has all the samples results, so we can split the results based on site and create a site specific file. Transfer the new file/s back to the server to the demix folder.
```
./freyplot.sh
```
This will generate the SC2 estimated abundances plots. 

# Sample Statistics and SC2 Variant Profiles in R
- You will need the cov files, depth files and all_filtered.tsv files. You will also need the covid.txt, voc_omicron.txt and a description file (this is similar to the samplesheet).
- The covid.txt, description file, voc_omicron.txt and all_filtered.tsv are required for the voc_annotation.rmd script. This will generate the SC2 variant profile. Remember to open the all_filtered.tsv file in excel and save it prior to running the script.
- The description file, covid.txt, depth folder and cov folder are needed for the sentinel_reports.rmd script. This will generate the sample statistics.
- The R markdown scripts and necessary files are in the folder called Rscripts.



