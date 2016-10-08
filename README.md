#dUMR
dUMR(differentially Under Methylated Regions among normal tissues and pan-cancers) identifies reference UMRs from normal samples and cancer samples based on whole genome bisulfite sequencing data.

![workflow](https://github.com/methylation/dUMR/blob/master/imgs/dUMR.png "foo")

###Step1. Methylation calling
For each whole genome bisulfite sequencing sample, BSMAP v2.90 was used to align reads to reference genome(hg19) after trimming low quality bases and adapter sequence. [1]

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

Then `methratio.py` in BSMAP was used to calculate methyaltion ratio at single base resolution. Then the CpGs which covered with less than 4 reads was removed in downstream analysis.
```
methratio.py -g -u *.bam -o *.bed -d hg19.fa -s ./samtools
```
* -g combined CpG methylation ratio on both strands 
* -u aligned file
* -o methyaltion ratio file [OUTPUT]
* -d reference genome
* -s path of SAMTools

###Step2. Identification of UMR
Under Methylated Regions were defined as including at least 4 consecutive hypomethylated CpGs and mean methylation level less than 0.1 [2]. For each WGBS methylome, UMRs were identified by perl script `pattern`.
the command line was in the following:
```
pattern -i *.wig -o pattern/ -n name
```
* -i 
* -o 
* -n 

###Step3. Identification of reference UMR

```
refumr -p UM -path ./software/iBSTools_v1.1.0/ -w wig_list.txt -o ref_UM
```

###Step4. Aberrant DNA methylation in the cancer genomes

```
dmr -r ref_UM/ref_UM.bed -rh 1 -w1 file_list_1.txt  -w2 file_list_2.txt -o diff
```

###Reference
[1] Xi Y, Li W. BSMAP: whole genome bisulfite sequence MAPping program[J]. BMC bioinformatics, 2009, 10(1): 1.
[2] Jeong M, Sun D, Luo M, et al. Large conserved domains of low DNA methylation maintained by Dnmt3a[J]. Nature genetics, 2014, 46(1): 17-23.
