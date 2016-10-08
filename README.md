#dUMR
dUMR(differentially Under Methylated Regions among normal tissues and pan-cancers) identifies reference UMRs from normal samples and cancer samples based on whole genome bisulfite sequencing data.

![workflow](https://github.com/methylation/dUMR/blob/master/imgs/dUMR.png "foo")

###Dependencies
dUMR requires a working of Perl,Python and R on Linux running environment. These scripts could be executable by using `chmod 755 command` to change permissions or using `perl command` directly.

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
* -s path of `SAMTools`

###Step2. Identification of UMR
Under Methylated Regions were defined as including at least 4 consecutive hypomethylated CpGs and mean methylation level less than 0.1 [2]. For each WGBS methylome, UMRs were identified by perl script `umr` and the command line was in the following:
```
umr -i *.wig -o pattern/ -n name
```
* -i input methylation level file(.wig)
* -o output UMRs file
* -n sample name

###Step3. Identification of reference UMR
`refumr` was used to identify high confidence and reference under methylated regions from mutiple methylomes.
```
refumr -p UM -path ./ -w wig_list.txt -o ref_UM
```
* -p identifies reference pattern type:UM
* -path the path of `umr`
* -w wig file lists, each row is a methylome
* -o reference UMRs out directory

###Step4. Aberrant DNA methylation in the cancer genomes
`dmr` was used to identify differentially reference UMRs in the cancer genomes.

```
dmr -r ref_UM/ref_UM.bed -rh 1 -w1 Normal_file_list.txt  -w2 Cancer_file_list.txt -o diff
```
* -r reference UMRs 
* -rh headline
* -w1 wig file lists of group 1, each row is a methylome
* -w2 wig file lists of group 2, each row is a methylome

###Reference
[1] Xi Y, Li W. BSMAP: whole genome bisulfite sequence MAPping program[J]. BMC bioinformatics, 2009, 10(1): 1.

[2] Jeong M, Sun D, Luo M, et al. Large conserved domains of low DNA methylation maintained by Dnmt3a[J]. Nature genetics, 2014, 46(1): 17-23.
