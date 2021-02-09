# SNAQ-Seq Quality Control (SSQC) Pipeline

# Contents
* [Installation](#installation)
* [Detailed Guide](#detailed-guide)
   * [Usage](#usage)
   * [Output](#output)
* [Contributing](#contributing)

# Installation

The SSQC pipeline and all its dependencies are Linux based, typically running under Linux
operating system. You can install the SSQC pipeline and all its dependencies manually using the following steps.

1. Install the following dependencies:

    * [BWA](https://github.com/lh3/bwa)
    * [Samtools](http://www.htslib.org/download)

2. Clone or download the repository form Github
```
git clone https://github.com/tbmorrison/ssqc.git
```
__or__
```
curl -sSL -o ssqc.zip https://github.com/tbmorrison/ssqc/archive/master.zip
mkdir -p ssqc
unzip ssqc.zip -d ssqc'
```
## Usage
```
ssqc [PARAM_TSV_FILE_PATH]
[PARAM_TSV_FILE_PATH] Parameter file indicating which sequences to process
```
### Parameter file format

The paramater file is a tab seperated



  *paired-end true or false if sequence is paired end
  fastq-files *contains a list of fastq.gz files, paired ends alternate with naming convension name-R1.fastq.gz and name-R2.fastq.gz
  is_input   <integer> copies of internal standard added to sample.
  cc_expected        <integer> determined after several runs..expected count of unique CC sequences when no significant NT competition.
  nt2is </path/to/file> contains the mapping of NT to IS, base changes in lowercase
  ref </path/to/files> path to the reference genome
  seqSplit <true/false> determine if the NT and IS reads are to divided into separate bam and fastq
  multiple-lanes	true or false Not implemented. Script does not handle illumina 4 lane fastq.  Use catMultLane to concatenate lanes
  qScore	Sorting reads into NT, IS, REC, CC, &  ukn bins uses base change position, to use position for splitting qScore must be at or above (zero ignores)
  goodBaseChange	Number of base change positions required for sorting, otherwise ends up in ukn bin
  calcCov	true or false generate coverage table to be used for viral load, otherwise use NT and IS read counts
  covBed	table used by samtools bedcov for coverage analysis

Program flow: read parameters; if needed--align reads; call remRecombinants to count CC and remove recombinants; calculate NT and IS reads; create runQC table; calculate viral load
viral load calculated from total amplicon reads NT / IS * IS_INPUT
runQC uses the NT and IS CC-reads to adjust expected complexity capture (cc_expected) for competition.  Unique CC sequences calculated from -CC-counts.txt
Coverage calculated using bamstats amplicon counting method

## Output

### Key files produced
viralLoad.txt contains the estimated viral genomes present when IS was mixed with sample
runQC.txt indicates how far the complexity capture deviated from expected.

### Other files produced
<prefix>-NT-R1.fastq or <prefix>-NT-R2.fastq are the viral reads, and should be compatible with your viral assembly pipeline
-IS-R1.fastq or -IS-R2.fastq are the IS reads  
-bad-R1.fastq or -bad-R2.fastq are the recombinant reads
-IS.coverage, -cc.coverage, -NT.coverage, or -bad.coverage a table listing the reads for each amplicon, used for viral load.
-tallies.txt a table indicating occurrences and position of recombinants
-CC.txt table the source/quality of a samples CC sequences.
-CC-counts.txt a summary table created from the CC.txt file.
Bam and sam intermediate files used to create fastq and coverage.

# Contributing

We welcome contributions!
The code has room for optimization but we should settle on the final deliverable before moving to optimization. Also, I’m a hacker, so I’m happy to hear of any suggestions for code improvements. Please have a look [here](CONTRIBUTING.md) on how you can help.
