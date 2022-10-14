# inStrain tutorial

In this tutorial we are going to run inStrain using the genome database we created in the previous tutorial and the reads we downloaded during setup.

Check the inStrain documentation for more information about inStrain https://instrain.readthedocs.io/en/latest/

## Step 1) Generate .bam files

InStrain takes .bam files as input, which are files representing metagenomic reads mapped to a .fasta file. Let's generate those as so:

```
$ cd ~

$ samp_list="ERR599092 ERR599140"; time for f in ${samp_list}; do bowtie2 -x OGdb.fasta.bt2 -1 reads/$f\_1.fastq.gz -2 reads/$f\_2.fastq.gz --no-unal -S $f.OGdb.sam -p 6; done
```

This should take about 90 minutes or so. You should also see an output like the following:

```
169336043 reads; of these:
  169336043 (100.00%) were paired; of these:
    151401530 (89.41%) aligned concordantly 0 times
    15596272 (9.21%) aligned concordantly exactly 1 time
    2338241 (1.38%) aligned concordantly >1 times
    ----
    151401530 pairs aligned concordantly 0 times; of these:
      46797 (0.03%) aligned discordantly 1 time
    ----
    151354733 pairs aligned 0 times concordantly or discordantly; of these:
      302709466 mates make up the pairs; of these:
        300893440 (99.40%) aligned 0 times
        1526945 (0.50%) aligned exactly 1 time
        289081 (0.10%) aligned >1 times
11.15% overall alignment rate
28107661 reads; of these:
  28107661 (100.00%) were paired; of these:
    25421947 (90.44%) aligned concordantly 0 times
    2358017 (8.39%) aligned concordantly exactly 1 time
    327697 (1.17%) aligned concordantly >1 times
    ----
    25421947 pairs aligned concordantly 0 times; of these:
      7124 (0.03%) aligned discordantly 1 time
    ----
    25414823 pairs aligned 0 times concordantly or discordantly; of these:
      50829646 mates make up the pairs; of these:
        50552375 (99.45%) aligned 0 times
        231837 (0.46%) aligned exactly 1 time
        45434 (0.09%) aligned >1 times
10.07% overall alignment rate
```

## Step 2) Decide on inStrain parameters

```
$ inStrain profile -h
usage: inStrain profile [-o OUTPUT] [--use_full_fasta_header] [--force_compress] [-p PROCESSES] [-d] [-h] [--version] [-l MIN_READ_ANI] [--min_mapq MIN_MAPQ] [--max_insert_relative MAX_INSERT_RELATIVE]
                        [--min_insert MIN_INSERT] [--pairing_filter {non_discordant,paired_only,all_reads}] [--priority_reads PRIORITY_READS] [--detailed_mapping_info] [-c MIN_COV] [-f MIN_FREQ] [-fdr FDR]
                        [-g GENE_FILE] [-s [STB [STB ...]]] [--mm_level] [--skip_mm_profiling] [--database_mode] [--min_scaffold_reads MIN_SCAFFOLD_READS] [--min_genome_coverage MIN_GENOME_COVERAGE]
                        [--min_snp MIN_SNP] [--store_everything] [--scaffolds_to_profile SCAFFOLDS_TO_PROFILE] [--rarefied_coverage RAREFIED_COVERAGE] [--window_length WINDOW_LENGTH] [--skip_genome_wide]
                        [--skip_plot_generation]
                        bam fasta

REQUIRED:
  bam                   Sorted .bam file
  fasta                 Fasta file the bam is mapped to

I/O PARAMETERS:
  -o OUTPUT, --output OUTPUT
                        Output prefix (default: inStrain)
  --use_full_fasta_header
                        Instead of using the fasta ID (space in header before space), use the full header. Needed for some mapping tools (including bbMap) (default: False)
  --force_compress      Force compression of all output files (default: False)

SYSTEM PARAMETERS:
  -p PROCESSES, --processes PROCESSES
                        Number of processes to use (default: 6)
  -d, --debug           Make extra debugging output (default: False)
  -h, --help            show this help message and exit
  --version             show program's version number and exit

READ FILTERING OPTIONS:
  -l MIN_READ_ANI, --min_read_ani MIN_READ_ANI
                        Minimum percent identity of read pairs to consensus to use the reads. Must be >, not >= (default: 0.95)
  --min_mapq MIN_MAPQ   Minimum mapq score of EITHER read in a pair to use that pair. Must be >, not >= (default: -1)
  --max_insert_relative MAX_INSERT_RELATIVE
                        Multiplier to determine maximum insert size between two reads - default is to use 3x median insert size. Must be >, not >= (default: 3)
  --min_insert MIN_INSERT
                        Minimum insert size between two reads - default is 50 bp. If two reads are 50bp each and overlap completely, their insert will be 50. Must be >, not >= (default: 50)
  --pairing_filter {non_discordant,paired_only,all_reads}
                        How should paired reads be handled?
                        paired_only = Only paired reads are retained
                        non_discordant = Keep all paired reads and singleton reads that map to a single scaffold
                        all_reads = Keep all reads regardless of pairing status (NOT RECOMMENDED; See documentation for deatils)
                         (default: paired_only)
  --priority_reads PRIORITY_READS
                        The location of a list of reads that should be retained regardless of pairing status (for example long reads or merged reads). This can be a .fastq file or text file with list of read
                        names (will assume file is compressed if ends in .gz (default: None)

READ OUTPUT OPTIONS:
  --detailed_mapping_info
                        Make a detailed read report indicating deatils about each individual mapped read (default: False)

VARIANT CALLING OPTIONS:
  -c MIN_COV, --min_cov MIN_COV
                        Minimum coverage to call an variant (default: 5)
  -f MIN_FREQ, --min_freq MIN_FREQ
                        Minimum SNP frequency to confirm a SNV (both this AND the FDR snp count cutoff must be true to call a SNP). (default: 0.05)
  -fdr FDR, --fdr FDR   SNP false discovery rate- based on simulation data with a 0.1 percent error rate (Q30) (default: 1e-06)

GENE PROFILING OPTIONS:
  -g GENE_FILE, --gene_file GENE_FILE
                        Path to prodigal .fna genes file. If file ends in .gb or .gbk, will treat as a genbank file (EXPERIMENTAL; the name of the gene must be in the gene qualifier) (default: None)

GENOME WIDE OPTIONS:
  -s [STB [STB ...]], --stb [STB [STB ...]]
                        Scaffold to bin. This can be a file with each line listing a scaffold and a bin name, tab-seperated. This can also be a space-seperated list of .fasta files, with one genome per .fasta
                        file. If nothing is provided, all scaffolds will be treated as belonging to the same genome (default: [])

READ ANI OPTIONS:
  --mm_level            Create output files on the mm level (see documentation for info) (default: False)
  --skip_mm_profiling   Dont perform analysis on an mm level; saves RAM and time; impacts plots and raw_data (default: False)

PROFILE OPTIONS:
  --database_mode       Set a number of parameters to values appropriate for mapping to a large fasta file. Will set: --min_read_ani 0.92 --skip_mm_profiling --min_genome_coverage 1 (default: False)
  --min_scaffold_reads MIN_SCAFFOLD_READS
                        Minimum number of reads mapping to a scaffold to proceed with profiling it (default: 1)
  --min_genome_coverage MIN_GENOME_COVERAGE
                        Minimum number of reads mapping to a genome to proceed with profiling it. MUST profile .stb if this is set (default: 0)
  --min_snp MIN_SNP     Absolute minimum number of reads connecting two SNPs to calculate LD between them. (default: 20)
  --store_everything    Store intermediate dictionaries in the pickle file; will result in significantly more RAM and disk usage (default: False)
  --scaffolds_to_profile SCAFFOLDS_TO_PROFILE
                        Path to a file containing a list of scaffolds to profile- if provided will ONLY profile those scaffolds (default: None)
  --rarefied_coverage RAREFIED_COVERAGE
                        When calculating nucleotide diversity, also calculate a rarefied version with this much coverage (default: 50)
  --window_length WINDOW_LENGTH
                        Break scaffolds into windows of this length when profiling (default: 10000)

OTHER  OPTIONS:
  --skip_genome_wide    Do not generate tables that consider groups of scaffolds belonging to genomes (default: False)
  --skip_plot_generation
                        Do not make plots (default: False)
```

## Step 3) Run inStrain profile

```
$ samp_list="ERR599092 ERR599140"; for f in ${samp_list}; do inStrain profile $f\.OGdb.sam OGdb.fasta --database_mode -o $f\_OGdb.IS -s OGdb.stb; done
```

You should see a series of things happen, including:

* The .sam file being converted into a sorted .bam file
* inStrain profile will be run
* Lots and lots and lots of warnings will be spilled
* Lots and lots of plotting junk will be printed to the screen

At the end should see:

```
$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$

..:: inStrain profile finished ::..

Output tables........ /home/ubuntu/ERR599140_OGdb.IS/output/
Figures.............. /home/ubuntu/ERR599140_OGdb.IS/figures/
Logging.............. /home/ubuntu/ERR599140_OGdb.IS/log/

See documentation for output descriptions - https://instrain.readthedocs.io/en/latest/

$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$
```

This process could take as long as 3 hours, depending on whether you use `--skip_plots` or not

## Step 4) Check out the results

## Step 5) Run inStrain compare

```
$ time inStrain compare -i *OGdb.IS -s OGdb.stb --database_mode -p 6 -o OGdb_compare.RC
```

This should only take a couple of minutes to run