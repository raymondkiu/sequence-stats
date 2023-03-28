# sequence-stats
A fast and beginner-friendly program to generate statistics from FASTQ and FASTA files (written AWK and Bash), e.g. genome assembly sizes and GC content (%). Also manipulate sequences such as renaming contigs, extract contigs and converting FASTQ to FASTA. Written in Bash, no specific dependencies required, should run without problems in Linux OS. All in one place. Tested to analyse microbial/prokaryotic sequences only, might not be able to handle super large files (>10GB) - may be taking too long to do that.

If you are not a Python/Perl/C/C++ programmer but a newbie in bioinformatics, and would like to use a Linux-based software with no complex installation/special libraries/dependencies required, this is the right tool for you. For small microbial genomes, the efficiency of this software is comparable to Perl/Python-based tools. Should generate outcome within a few seconds for each file.

## Installation
Simply download the package and run the program in Unix environment. Source code is available in the src directory.

## Usage
Please note that sequence-stats does not handle gzipped inputs. Extensions such as .fasta, .fna or .fastq are not required.
```
Sequence-stats generates statistics from FASTQ reads or FASTA assemblies

For user manual please go to: https://github.com/raymondkiu/sequence-stats

Usage: sequence-stats [options] FASTA/FASTQ

Options:
 -a Print FASTA stats
 -q Print FASTQ stats
 -t Convert FASTQ to FASTA. Usage: ./sequence-stats -t FASTQ > NEWFILENAME
 -c Print individual contig's stats (FASTA)
 -d Dereplicate contigs in (multi)FASTA. Usage: ./sequence-stats -d FASTA > NEWFILENAME
 -n Rename contigs. Usage: ./sequence-stats -n FASTA PREFIX > NEWFILENAME
 -b Print FASTA stats in tabular format
 -r Print FASTQ stats in tabular format
 -e Extract contig(s) from FASTA sequences. Usage: ./sequence-stats -e CONTIG-IDs.txt FASTA > NEWFILENAME
 -s Print summary of FASTA tabular stats of multiple files using common suffix. Usage: ./sequence-stats -s SUFFIX > NEWFILENAME
 -f Filter FASTA sequences by length. Usage: ./sequence-stats -f FASTA 500 > NEWFILENAME
 -h Print usage and exit
 -v Print version and exit

```

#### Example 1: FASTA stats
Use option -a to generate stats for FASTA files such as this genome sequence file. This format is grep-friendly, if you want a tabular format, see the next example, use option -b. Tested for FASTA genome size > 10MB.
```
$ ./sequence-stats -a CA.fna 
Sample: CA-20.fna
Genome(bp): 2220029
Contigs: 17
GC(%): 62.76
A(%): 18.45
T(%): 18.78
G(%): 31.02
C(%): 31.73
N50: 360689
Max Contig: 898564
Min Contig: 792
N count: 10
N(%): .0004
Gap(-): 5
Uncertain(bp): 17
```

#### Example 2: FASTA stats in tabular format
```
$ ./sequence-stats -b CA.fna 
SampleID	Genome	Contigs	GC(%)	A(%)	T(%)	G(%)	C(%)	N50	Max	Min	Ncount	N(%)	Gap(-)	Uncertain(bp)	
CA-20.fna	2220029	17	62.76	18.45	18.78	31.02	31.73	360689	898564	792	10	.0004	5	17
```

#### Example 3: FASTQ stats
sequence-stats generates basic FASTQ stats, mainly for you to determine to total read counts and bases also the read length. Also available in tabular format. Tested for fastq file size >300MB.
```
$ ./sequence-stats -q V17.fastq 
Sample: V17.fastq
File size: 95M
Total bases: 37574330
Reads: 125198
Max read length: 301
Min read length: 47
Mean read length: 300.119
```

#### Example 4: Individual contig's stats
This option generates individual contig's length with contigs' IDs.
```
$ ./sequence-stats -c CA.fna 

NODE_1_length_898552_cov_25.252293	898564
NODE_2_length_360689_cov_22.619544	360689
NODE_3_length_310889_cov_25.950533	310889
NODE_4_length_236964_cov_27.060105	236964
NODE_5_length_137416_cov_24.247934	137416
NODE_6_length_82085_cov_21.715723	82085
NODE_7_length_74446_cov_30.811911	74446
NODE_8_length_43698_cov_36.907430	43698
NODE_9_length_38558_cov_20.173728	38558
NODE_10_length_22311_cov_54.678330	22311
NODE_11_length_5573_cov_99.941594	5573
NODE_12_length_2561_cov_142.282609	2561
NODE_13_length_1727_cov_27.903636	1727
NODE_14_length_1526_cov_48.884748	1526
NODE_15_length_1181_cov_28.868659	1181
NODE_16_length_1049_cov_51.163580	1049
NODE_17_length_792_cov_66.374825	792
```

## Issues
Please report any issues to the [issues page](https://github.com/raymondkiu/sequence-stats/issues).

## Citation
If you use sequence-stats for results in your publication, please cite:
* Kiu R, *sequence-stats: generate sequence statistics from FASTA and FASTQ files*, **GitHub** `https://github.com/raymondkiu/sequence-stats`, **DOI** `https://doi.org/10.6084/m9.figshare.16950775`

## License
sequence-stats is a free software licensed under [GPLv3](https://github.com/raymondkiu/sequence-stats/blob/master/LICENSE)

## Author
Raymond Kiu | Raymond.Kiu@quadram.ac.uk | [@raymond_kiu](https://twitter.com/raymond_kiu)
