# dRep and inStrain Tutorial

The aim of this tutorial is to make you familiar with using dRep to construct a genome set, and using inStrain to analyze that genome set.

See [drep.md](drep.md) for the dRep tutorial

See [instrain.md](instrain.md) for the inStrain tutorial

In case the steps below fail, intermediate files can be found here - https://zenodo.org/record/7200082#.Y0oAAi2B1qs

# Setup

The following instructions can be used to download all needed programs and files. If you are following this tutorial as part of EBAME7, I recommend that you use the `Ubuntu 22.04 (22.04)` Appliance with the `ifb.m4.2xlarge` machine.

## Step 1) Install dependencies

The following command will create a new conda environment called `ebame` with all needed dependencies

```
$ mamba create -n ebame -c bioconda instrain drep bowtie2 -y
```

This should take about 5 minutes to run. You can then activate the environment using the command

```
$ conda activate ebame
```

## Step 2) Download genomes to dereplicate

We will be dereplicating a set of 2,631 draft metagenome-assembled genomes from the global oceans. Many different genome sets could be used here, but I chose this one because it is not yet dereplicated.

A link to the genomes can be found here: https://doi.org/10.6084/m9.figshare.5188273.v13

A link to the manuscript describing their creation can be found here: https://doi.org/10.1038/sdata.2017.203

Download the genomes into a new folder using the following commands:

```
$ mkdir raw_genomes

$ cd raw_genomes

$ wget https://ndownloader.figshare.com/files/8849371 -O genomes.tar.gz

$ tar -xf genomes.tar.gz

$ cd ~
```

The download should take about 10 minutes, and decompressing the genomes should take about 1 minute

Finally, lets download the quality information for these genomes using the following command:

```
$ wget https://github.com/MrOlm/drep_instrain_tutorial/raw/main/downloads/Table3_GenomeStats.csv
```

This file (https://github.com/MrOlm/drep_instrain_tutorial/blob/main/downloads/Table3_GenomeStats.csv) was made from Table3, here: https://figshare.com/articles/dataset/The_Reconstruction_of_2_631_Draft_Metagenome-Assembled_Genomes_from_the_Global_Oceans/5188273/13?file=10217256

It just contains the completeness and contamination information for each genome that we downloaded
## Step 3) Download metagenomic reads

We will be mapping our dereplicated genomes against two ocean metagenomes. A beautiful list of ftp links to ocean metagenomes is provided here: https://merenlab.org/data/tara-oceans-mags/files/samples.txt as part of this tutorial: https://merenlab.org/data/tara-oceans-mags

For this tutorial, lets download reads from samples `MED_18_05M` and `MED_18_60M`. I believe these are two metagenomes from the Mediterranean Sea taken from the same location at two different depths.

Download the reads into a new folder using the following commands:

```
$ mkdir reads

$ cd reads

$ echo -e "ftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR599/ERR599140/ERR599140_1.fastq.gz\nftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR599/ERR599140/ERR599140_2.fastq.gz\nftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR599/ERR599092/ERR599092_1.fastq.gz\nftp://ftp.sra.ebi.ac.uk/vol1/fastq/ERR599/ERR599092/ERR599092_2.fastq.gz" > ftp_links_to_download.txt

$ for url in $(cat ftp_links_to_download.txt); do wget $url; done
```

This could take up to an hour

## Step 4) Move on to the dRep tutorial

Move to [drep.md](drep.md) for the dRep tutorial