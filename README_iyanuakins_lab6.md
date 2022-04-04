#### __README : EDITED COMMAND HISTORY DURING MSBI32400_LAB6__
#### __Date Run: Wed, 3/2/2022__
#### __Topic: Making bams & calling variants__
#### __Tools used: samtools , bwa, bcftools__

***
- A new directory `lab6` was created with its subdirectories `data`, `bin`, `source`,`doc` and `results` within my home directory using:

    `mkdir -p lab6/{data,bin,source,doc,results}`

***  
- I changed my working directroty to `~/lab6/data` and exported my path using:
    
    `export PATH=$PATH:/home/iyanuakins/samtools`

- Then confirmed my path with: `echo $PATH`

- And generated a fastq file from the a sample.bam file in the class directory:

    `samtools fastq -t /project2/msbi32400/bds-files/chapter-11-alignment/NA12891_CEU_sample.bam > NA12891_CEU_sample.fastq`

- Still within `cd ~/lab6/data` I downloaded a reference genome using:

    `wget http://hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/chr1.fa.gz` 
    
- Then extracted it by running: `gunzip chr1.fa.gz`
    
- I loaded the "Burrow- Wheeler's alignment (bwa)" module from the cluster by running: `module load bwa`
  
- Then bwa is used to implement an algorithm that will create an index for chr1 by running: `bwa index -a bwtsw chr1.fa`

***
- ABOUT THE BWA-MEM (Burrows-Wheeler Alignment Maximal Exact Matches Tool)
    BWA is a software package for mapping low-divergent sequences against a large reference genome, such as the human genome. It consists of three algorithms: BWA-backtrack, BWA-SW and BWA-MEM. The BWA-MEM is the latest (first published in 2012), fastest and most accurate algorithm.

    The BWA-MEM algorithm is an algorithm that finds super-maximal exact matches (SMEMs). The algorithm works by seeding alignments with maximal exact matches (MEMs) and then extending seeds with the affine-gap Smith-Waterman algorithm (SW). The BWA-MEM algorithm performs local alignment and may produce multiple primary alignments for different part of a query sequence. This feature is called split alignment together with the long-read support, these are crucial features for long sequences. BWA-MEM is generally recommended for high-quality queries. 

    BWA-MEM tolerates more errors given longer alignment. It is suggested that it may work well given 2% error for an 100bp alignment, 3% error for a 200bp, 5% for 500bp and 10% for 1000bp or longer alignment. BWA-MEM is able to find chimera.
    
    References:
    
    http://bio-bwa.sourceforge.net/
    
    http://bio-bwa.sourceforge.net/bwa.shtml

   A screenshot of the bwa mem options can be seen below:
   
![](https://uchicago.box.com/shared/static/7whpe5o9ye39wji7qgrh2qt0b4nruh0g.png)

***

- A sam file was generated from aligning the reference genome with the .fastq file by running: 

  `bwa mem -R '@RG\tID:MSBI32400 _test\tSM:NA12891_CEU_sample' chr1.fa NA12891_CEU_sample.fastq > NA12891_CEU_sample.sam`
  
- The header of the sam file was viewed using:

  `head NA12891_CEU_sample.sam`
  
- Samtools was then used to create another index for the reference genome (chr1.fa) to prepare it for further manipulation with samtools by running:

   `time samtools faidx ~/lab6/data/chr1.fa`

***
- I converted the fastq file to bam, sorted it and indexed the sorted bam with:

   `samtools view -bt ~/lab6/data/chr1.fa.fai ~/lab6/data/NA12891_CEU_sample.sam > ~/lab6/results/NA12891_CEU_sample.bam`
   
   `samtools sort -o ~/lab6/results/NA12891_CEU_sample_sorted.bam ~/lab6/results/NA12891_CEU_sample.bam`
   
   `samtools index ~/lab6/results/NA12891_CEU_sample_sorted.bam`
   
- A glimpse of the header for the sorted bam was displayed by running: 

   `samtools view -H ~/lab6/results/NA12891_CEU_sample_sorted.bam`

***
- I changed the `cd` from the `~/lab6/data` directory and to the `~/lab6/results` directory:

- I checked out the manual for the samtools command with: `samtools`

- Then I loaded the exisitng bcftools in the cluster using this: `module load bcftools`

- Using variant calling and mpileup from bcf tools, I piped the output of the mpileup between the reference genome (stored in `~/lab6/data`) and the sorted bam. Then used bcftools to call and save the called variant with this command: 

    `bcftools mpileup -f ../data/chr1.fa NA12891_CEU_sample_sorted.bam | bcftools call -mv > NA12891_CEU_sample_sorted_var.raw.vcf`

- Then filtered out all variants with a quality less than 20 using this command:

    `bcftools filter -e 'QUAL<20' NA12891_CEU_sample_sorted_var.raw.vcf > NA12891_CEU_sample_sorted_var.flt.vcf`
    
- I used `bcftools view -c1 NA12891_CEU_sample_sorted_var.raw.vcf` to view the raw file. I noticed that all variants had lines that started with "chr1".

    And `bcftools view -c1 NA12891_CEU_sample_sorted_var.flt.vcf` to view the filtered file. I noticed that all variants were called with PASS.  

- So, I used `grep '^chr1' NA12891_CEU_sample_sorted_var.raw.vcf | wc -l` to check out how many variants (lines that start with chr1) were in the raw .vcf file. The result was __736__.

- Then I used `grep '^chr1' NA12891_CEU_sample_sorted_var.flt.vcf | wc -l` to check out how many variants (lines that start with chr1) were called with "PASS" in the filtered .vcf file. The result was __689__.

- This means that __47__ (736 - 689) variants were with a quality less than 20 and thus filtered out of the raw vcf. 

- In conclusion,__689__ variants were called in the final VCF and they were called with PASS. The ouput of the counted variants can be seen below:

![](https://uchicago.box.com/shared/static/w14sqmq81h69ih9givu5xsqo6juzid65.png)
