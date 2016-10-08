#dUMR
dUMR(differentially Under Methylated Regions among normal tissues and pan-cancers) identifies reference UMRs from normal samples and cancer samples based on whole genome bisulfite sequencing data.

![workflow](https://github.com/methylation/dUMR/blob/master/imgs/dUMR.png "foo")

###Step1. Methylation calling
For each whole genome bisulfite sequencing sample, BSMAP v2.90 was used to align reads to reference genome(hg19) after trimming low quality bases and adapter sequence. 

```
bsmap -a *_R1.fastq -b *_R2.fastq -d hg19.fa  -L 100 -R -p 4 -o *.bam
```
* -a the first pair-end read
* -b the second pair-end read
* -d reference genome
* -L map the frist N bases of the read
* -R print corresponding reference sequence 
* -p numbers of thread
* -o aligned file [OUTPUT]

Then `methratio.py` in BSMAP was used to calculate methyaltion ratio at single base resolution.
```
methratio.py -g -u *.bam -o *.bed -d hg19.fa -s ./samtools
```
* -g combined CpG methylation ratio on both strands 
* -u aligned file
* -o methyaltion ratio file [OUTPUT]
* -d reference genome
* -s path of SAMTools

###Step2. Identification of UMR

```
pattern 
```

###Step3. Identification of reference UMR

```
refpattern
```

###Step4. Aberrant DNA methylation in the cancer genomes


```
dmr
```
