# dRep and inStrain Tutorial

The aim of this tutorial is to make you familiar with using dRep to construct a genome set, and using inStrain to analyze that genome set.

See [drep.md](drep.md) for the dRep tutorial

See [instrain.md](instrain.md) for the inStrain tutorial

## Setup

The following instructions can be used to download all needed programs and files. If you are following this tutorial as part of EBAME7, I recommend that you use the `Ubuntu 22.04 (22.04)` Appliance with the `ifb.m4.2xlarge` machine.

### Step 1) Install dependencies

The following command will create a new conda environment called `ebame` with all needed dependencies

```
conda create -n ebame -c bioconda instrain drep bowtie2 -y
```

This should take about 5 minutes to run


### Step 2) Download genomes to dereplicate

We will be dereplicating a set of 2,631 draft metagenome-assembled genomes from the global oceans. Many different genome sets could be used here, but I chose this one because it is not yet dereplicated.

A link to the genomes can be found here: https://doi.org/10.6084/m9.figshare.5188273.v13

A link to the manuscript describing their creation can be found here: https://doi.org/10.1038/sdata.2017.203

Download the genomes into a new folder using the following commands:

```
$ mkdir raw_genomes

$ cd raw_genomes

$ wget https://ndownloader.figshare.com/files/8849371 -O genomes.tar.gz

$ tar -xf genomes.tar.gz
```

In total this should take around 5 minutes

### Step 3) Download metagenomic reads

We will be mapping our dereplicated genomes against two ocean metagenomes.