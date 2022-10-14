# dRep Tutorial
In this tutorial we are going to dereplicate the set of ocean genomes downloaded during the [setup](README.md) steps.

Check the dRep readthedocs for more information: https://drep.readthedocs.io/en/latest/

## Step 1) Verify everything is set up correctly

dRep relies on a number of different dependencies to run correctly. Before running dRep it's always best to make sure everything is installed correctly using the following command:

```
$ dRep check_dependencies
```

You should see an output like so:

```
mash.................................... all good        (location = /var/lib/miniconda3/envs/ebame/bin/mash)
nucmer.................................. all good        (location = /var/lib/miniconda3/envs/ebame/bin/nucmer)
checkm.................................. !!! ERROR !!!   (location = None)
ANIcalculator........................... !!! ERROR !!!   (location = None)
prodigal................................ all good        (location = /var/lib/miniconda3/envs/ebame/bin/prodigal)
centrifuge.............................. !!! ERROR !!!   (location = None)
nsimscan................................ !!! ERROR !!!   (location = None)
fastANI................................. all good        (location = /var/lib/miniconda3/envs/ebame/bin/fastANI)
```

As you can see, the programs `checkm`, `ANIcalculator`, `centrifuge`, and `nsimscan` are not properly installed, but the rest are working. For what we're doing this is fine.

## Step 2) Decide on your dereplication parameters

Check the help with:

```
$ dRep dereplicate -h
```

You should see an output like so:

```
usage: dRep dereplicate [-p PROCESSORS] [-d] [-h] [-g [GENOMES [GENOMES ...]]] [-l LENGTH] [-comp COMPLETENESS] [-con CONTAMINATION] [--ignoreGenomeQuality] [--genomeInfo GENOMEINFO]
                        [--checkM_method {lineage_wf,taxonomy_wf}] [--set_recursion SET_RECURSION] [--checkm_group_size CHECKM_GROUP_SIZE] [--S_algorithm {gANI,fastANI,ANIn,ANImf,goANI}] [-ms MASH_SKETCH]
                        [--SkipMash] [--SkipSecondary] [--n_PRESET {normal,tight}] [-pa P_ANI] [-sa S_ANI] [-nc COV_THRESH] [-cm {total,larger}]
                        [--clusterAlg {weighted,complete,single,median,ward,average,centroid}] [--multiround_primary_clustering] [--primary_chunksize PRIMARY_CHUNKSIZE] [--greedy_secondary_clustering]
                        [--run_tertiary_clustering] [-comW COMPLETENESS_WEIGHT] [-conW CONTAMINATION_WEIGHT] [-strW STRAIN_HETEROGENEITY_WEIGHT] [-N50W N50_WEIGHT] [-sizeW SIZE_WEIGHT] [-centW CENTRALITY_WEIGHT]
                        [-extraW EXTRA_WEIGHT_TABLE] [--gen_warnings] [--warn_dist WARN_DIST] [--warn_sim WARN_SIM] [--warn_aln WARN_ALN] [--skip_plots]
                        work_directory

positional arguments:
  work_directory        Directory where data and output are stored
                        *** USE THE SAME WORK DIRECTORY FOR ALL DREP OPERATIONS ***

SYSTEM PARAMETERS:
  -p PROCESSORS, --processors PROCESSORS
                        threads (default: 6)
  -d, --debug           make extra debugging output (default: False)
  -h, --help            show this help message and exit

GENOME INPUT:
  -g [GENOMES [GENOMES ...]], --genomes [GENOMES [GENOMES ...]]
                        genomes to filter in .fasta format. Not necessary if Bdb or Wdb already exist. Can also input a text file with paths to genomes, which results in fewer OS issues than wildcard expansion
                        (default: None)

GENOME FILTERING OPTIONS:
  -l LENGTH, --length LENGTH
                        Minimum genome length (default: 50000)
  -comp COMPLETENESS, --completeness COMPLETENESS
                        Minimum genome completeness (default: 75)
  -con CONTAMINATION, --contamination CONTAMINATION
                        Maximum genome contamination (default: 25)

GENOME QUALITY ASSESSMENT OPTIONS:
  --ignoreGenomeQuality
                        Don't run checkM or do any quality filtering. NOT RECOMMENDED! This is useful for use with bacteriophages or eukaryotes or things where checkM scoring does not work. Will only choose
                        genomes based on length and N50 (default: False)
  --genomeInfo GENOMEINFO
                        location of .csv file containing quality information on the genomes. Must contain: ["genome"(basename of .fasta file of that genome), "completeness"(0-100 value for completeness of the
                        genome), "contamination"(0-100 value of the contamination of the genome)] (default: None)
  --checkM_method {lineage_wf,taxonomy_wf}
                        Either lineage_wf (more accurate) or taxonomy_wf (faster) (default: lineage_wf)
  --set_recursion SET_RECURSION
                        Increases the python recursion limit. NOT RECOMMENDED unless checkM is crashing due to recursion issues. Recommended to set to 2000 if needed, but setting this could crash python (default:
                        0)
  --checkm_group_size CHECKM_GROUP_SIZE
                        The number of genomes passed to checkM at a time. Increasing this increases RAM but makes checkM faster (default: 2000)

GENOME COMPARISON OPTIONS:
  --S_algorithm {gANI,fastANI,ANIn,ANImf,goANI}
                        Algorithm for secondary clustering comaprisons:
                        fastANI = Kmer-based approach; very fast
                        ANImf   = (DEFAULT) Align whole genomes with nucmer; filter alignment; compare aligned regions
                        ANIn    = Align whole genomes with nucmer; compare aligned regions
                        gANI    = Identify and align ORFs; compare aligned ORFS
                        goANI   = Open source version of gANI; requires nsmimscan
                         (default: fastANI)
  -ms MASH_SKETCH, --MASH_sketch MASH_SKETCH
                        MASH sketch size (default: 1000)
  --SkipMash            Skip MASH clustering, just do secondary clustering on all genomes (default: False)
  --SkipSecondary       Skip secondary clustering, just perform MASH clustering (default: False)
  --n_PRESET {normal,tight}
                        Presets to pass to nucmer
                        tight   = only align highly conserved regions
                        normal  = default ANIn parameters (default: normal)

GENOME CLUSTERING OPTIONS:
  -pa P_ANI, --P_ani P_ANI
                        ANI threshold to form primary (MASH) clusters (default: 0.9)
  -sa S_ANI, --S_ani S_ANI
                        ANI threshold to form secondary clusters (default: 0.95)
  -nc COV_THRESH, --cov_thresh COV_THRESH
                        Minmum level of overlap between genomes when doing secondary comparisons (default: 0.1)
  -cm {total,larger}, --coverage_method {total,larger}
                        Method to calculate coverage of an alignment
                        (for ANIn/ANImf only; gANI and fastANI can only do larger method)
                        total   = 2*(aligned length) / (sum of total genome lengths)
                        larger  = max((aligned length / genome 1), (aligned_length / genome2))
                         (default: larger)
  --clusterAlg {weighted,complete,single,median,ward,average,centroid}
                        Algorithm used to cluster genomes (passed to scipy.cluster.hierarchy.linkage (default: average)

GREEDY CLUSTERING OPTIONS
These decrease RAM use and runtime at the expense of a minor loss in accuracy.
Recommended when clustering 5000+ genomes:
  --multiround_primary_clustering
                        Cluster each primary clunk separately and merge at the end with single linkage. Decreases RAM usage and increases speed, and the cost of a minor loss in precision and the inability to plot
                        primary_clustering_dendrograms. Especially helpful when clustering 5000+ genomes. Will be done with single linkage clustering (default: False)
  --primary_chunksize PRIMARY_CHUNKSIZE
                        Impacts multiround_primary_clustering. If you have more than this many genomes, process them in chunks of this size. (default: 5000)
  --greedy_secondary_clustering
                        Use a heuristic to avoid pair-wise comparisons when doing secondary clustering. Will be done with single linkage clustering. Only works for fastANI S_algorithm option at the moment
                        (default: False)
  --run_tertiary_clustering
                        Run an additional round of clustering on the final genome set. This is especially useful when greedy clustering is performed and/or to handle cases where similar genomes end up in
                        different primary clusters. Only works with dereplicate, not compare. (default: False)

SCORING CRITERIA
Based off of the formula:
A*Completeness - B*Contamination + C*(Contamination * (strain_heterogeneity/100)) + D*log(N50) + E*log(size) + F*(centrality - S_ani)

A = completeness_weight; B = contamination_weight; C = strain_heterogeneity_weight; D = N50_weight; E = size_weight; F = cent_weight:
  -comW COMPLETENESS_WEIGHT, --completeness_weight COMPLETENESS_WEIGHT
                        completeness weight (default: 1)
  -conW CONTAMINATION_WEIGHT, --contamination_weight CONTAMINATION_WEIGHT
                        contamination weight (default: 5)
  -strW STRAIN_HETEROGENEITY_WEIGHT, --strain_heterogeneity_weight STRAIN_HETEROGENEITY_WEIGHT
                        strain heterogeneity weight (default: 1)
  -N50W N50_WEIGHT, --N50_weight N50_WEIGHT
                        weight of log(genome N50) (default: 0.5)
  -sizeW SIZE_WEIGHT, --size_weight SIZE_WEIGHT
                        weight of log(genome size) (default: 0)
  -centW CENTRALITY_WEIGHT, --centrality_weight CENTRALITY_WEIGHT
                        Weight of (centrality - S_ani) (default: 1)
  -extraW EXTRA_WEIGHT_TABLE, --extra_weight_table EXTRA_WEIGHT_TABLE
                        Path to a tab-separated file with two-columns, no headers, listing genome and extra score to apply to that genome (default: None)

WARNINGS:
  --gen_warnings        Generate warnings (default: False)
  --warn_dist WARN_DIST
                        How far from the threshold to throw cluster warnings (default: 0.25)
  --warn_sim WARN_SIM   Similarity threshold for warnings between dereplicated genomes (default: 0.98)
  --warn_aln WARN_ALN   Minimum aligned fraction for warnings between dereplicated genomes (ANIn) (default: 0.25)

ANALYZE:
  --skip_plots          Dont make plots (default: False)

Example: dRep dereplicate output_dir/ -g /path/to/genomes/*.fasta
```

Wowza- quite the set of options! The key parameters that matter most are:

* `--S_algorithm`: This is the algorithm that will be used to run the genomic comparisons

* `-sa`: This is the ANI threshold that genomes will be clustered at

Check the dRep documentation (https://drep.readthedocs.io/en/latest/) for more information on what these mean

## Step 3) Run dRep

Run dRep using a command like the following. Feel free to adjust the parameters if you'd like to be fancy

```
$ dRep dereplicate ocean_drep_output -g raw_genomes/*.fna -p 6 --genomeInfo Table3_GenomeStats.csv
```

Once you run this command you should see lots of output from dRep updating you as it runs.

In total this dereplication should take about 30 minutes. When it's finished you should see something like:

```
    ..:: dRep dereplicate finished ::..

Dereplicated genomes................. /home/ubuntu/ocean_drep_output/dereplicated_genomes/
Dereplicated genomes information..... /home/ubuntu/ocean_drep_output/data_tables/Widb.csv
Figures.............................. /home/ubuntu/ocean_drep_output/figures/
Warnings............................. /home/ubuntu/ocean_drep_output/log/warnings.txt
```

## Step 4) Inspect output

dRep produces a number of output files, as described here: https://drep.readthedocs.io/en/latest/example_output.html

Poke around! See what's what! You can download the figures yourself using a command like:

```
$ scp -r ubuntu@193.49.167.68:ocean_drep_output/figures ./
```

Or if that's not working, you can see the results here:

https://github.com/MrOlm/drep_instrain_tutorial/tree/main/example_results/ocean_drep_output

## Step 5) Create genome database

Now that our dereplication is complete, it's time to prepare the dereplicate genome set for mapping reads. This process takes several steps:

### Step 5.1) Rename scaffolds

Rename the scaffolds in the dereplicated genome files to account for scaffolds in different genomes that have the same name. 

```
$ cd ~

$ mkdir renamed_genomes

$ cd ocean_drep_output/dereplicated_genomes/

$ for f in $(ls *.fna); do cat $f | sed  "s|>|>$f\_|g" > ~/renamed_genomes/$f; done
```
This should take about 10 seconds

### Step 5.2) Create a scaffold to bin file

A scaffold to bin for (or `.stb` file) is just a text file that lists which scaffold belongs to which bin. We can create one using the program `parse_stb.py` that comes packaged along with `dRep`. To see how to use it, run:

```
$ parse_stb.py -h

usage:

The program has two uses related to scaffold to bin (.stb) files.
.stb files should be tab-separated, with no header, and two columns: scaffold and bin

Use 1) Pass a list of genomes to generate a .stb file.

Example:
parse_stb.py --reverse -f dereplicate_genomes/* -o representitve_genomes.stb

Use 2) Pass a single .fasta file and a scaffold to bin file (.stb) to generate a number of
fasta files based on the .stb file.

Example:
parse_stb.py -f concat_genomes.fasta -s scaffold_to_bin.tsv -o genomeList_1

optional arguments:
  -h, --help            show this help message and exit
  -s STB, --stb STB     scaffold to bin file (default: None)
  -f [FASTA [FASTA ...]], --fasta [FASTA [FASTA ...]]
                        fasta file to extract scaffolds from. Will treat as compressed if ends in .gz. This can also be a single text file with a genome on each line (default: None)
  -o OUTPUT, --output OUTPUT
                        output base name (default: )
  --reverse             generate a stb from a list of genomes (default: False)
```

For this tutorial create one using the commands:

```
$ cd ~

$ parse_stb.py --reverse -f renamed_genomes/* -o OGdb.stb
```

This will create the file `OGdb.stb` (which stands for Ocean Genome Database)

### Step 5.3) Concatenate all dereplicated genomes into a single file 

Now that we have the `.stb` file to sort out which scaffold comes from which genome, lets concatenate all the genomes into a single file

```
$ cat renamed_genomes/* > OGdb.fasta
```

### Step 5.4) Create a bowtie2 index

Finally, to prepare these genomes for mapping, let's create a bowtie2 index for the .fasta file

```
$ bowtie2-build OGdb.fasta OGdb.fasta.bt2 --large-index --threads 8
```

This should take about an hour