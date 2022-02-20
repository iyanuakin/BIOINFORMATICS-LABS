#### __EDITED COMMAND HISTORY DURING MSBI32400_LAB3__
#### __Date Run: Wed, 1/26/2022 - Sun, 1/31/2022__
#### __Topic: Genes Associated with Autism, bedtools, and samtools__

***
- A new directory `lab3` was created with its subdirectories `data`, `bin`, `src`,`doc` and `results` within my home directory using:

`mkdir -p lab3/{data,bin,src,doc,results}`


***  

- The directory was changed to the class directory to copy the GenesAssociatedAutism.csv file to the lab3/data subdirectory by running the following commands: 
 
 `cd /project2/msbi32400/`
 
 `cp GenesAssociatedAutism.csv /home/iyanuakins/lab3/data`
 
***
- From the `lab3/data` directory, a view of the .csv file was displayed by running: 
`head GenesAssociatedAutism.csv` 

- Then the fourth column of the .csv file was cut and saved in a new .txt file named `genelist.txt` by using the command:

`cut -d',' -f4 GenesAssociatedAutism.csv > genelist.txt`


***
- The next steps were used to load and open a firefox window. 

`module load firefox`

`firefox`

- From the firefox window, I moved to the UCSC website (http://genome.ucsc.edu/cgi-bin/hgTables) to upload the `genelist.txt` file and get an `ncbiRefSeq` track which was then downlaoded as a bed file with the name `101AutismGenelistExons.bed`

While on the UCSC webiste, I followed all the steps on the lab 3 slides 6 & 7.


***
- Next, the new bed file `101AutismGenelistExons.bed` was moved from the Downloads subdirectory of the home directory, to the `lab3/data` directory using:

`mv ~/Downloads/101AutismGenelistExons.bed ./data/`

  
***
- The following commands were used to return to the home directory, move into the IGV directory,and run the igv software:

`cd ~`

`cd IGV_Linux_2.11.9`

`ls`

`./igv.sh`

The guidelines (in slide 10 of the lab 3 document) were followed to load the bed file from my `lab3/data` directory. 
The output showed multiple transcripts in the bed file track (highlighted as a red box) as in slide 10 of the lab 3 document. 

The output can be seen here: 

![](https://uchicago.box.com/shared/static/q74bghmi6xoziyrpocsw0c4o3dbsljga.png)

  
***
- bedtools were downloaded and set up to handle the overlapping transcripts in the downloaded bed file. The following commands were ran to download a zipped version of the bedtools file and unzip its contents into its own directory called `bedtools2`:

`wget https://github.com/arq5x/bedtools2/releases/download/v2.29.2/bedtools-2.29.2.tar.gz`

`tar -zxvf bedtools-2.29.2.tar.gz`

- The pwd was changed to the `bedtools2` directory, to compile the bedtools by running the following:

`cd bedtools2` 

`make`

- Then the bin directory in the `bedtools2` directory was moved to the home directory using: 

`cp -R bin ~/` 

- The new bin directory was copied to path and confirmed by running the following:

`export PATH=~/bin:$PATH`

`echo $?`

`echo $PATH`

`echo $?`

__N.B__: `echo $?` was used to check that the previous command ran successfully. An output of 0 confirms a successful run.

- Finally, from the `lab3/data` directory, bedtools was used to clean up the bed file by running the following commands:

`bedtools sort -i 101AutismGenelistExons.bed >101AutismGenelistExons_sort.bed`

`echo $?`

`bedtools merge -c 4 -o collapse -i 101AutismGenelistExons_sort.bed >101AutismGenelistExons_sort_merged.bed`

`echo $?`

__N.B__: `echo $?` was used to check that the previous command ran successfully. An output of 0 confirms a successful run.


***
- Afterwards, igv was loaded to compare the merged bed file as follows: 

`cd ~`

`cd IGV_Linux_2.11.9`

`ls`

`./igv.sh`

The output showed single transcript in the merged bed file track (highlighted as a red box). The output can be seen here: 

![](https://uchicago.box.com/shared/static/l8dt4ytjxqlg59jatgnxo492vgbupunc.png)


***
- The next step was to get a vcf file to inspect and manipulate using samtools. We begin by downloading the vcf file using this command:

`wget https://github.com/vsbuffalo/bds-files/raw/master/chapter-11-alignment/NA12891_CEU_sample.vcf.gz`

- The downloaded .vcf file was inspected to show more information using this command : `zcat NA12891_CEU_sample.vcf.gz | more`

-  A copy of the downloaded .vcf file was made and unzipped using: `cp NA12891_CEU_sample.vcf.gz NA12891_CEU_copy.vcf.gz` and `gunzip NA12891_CEU_copy.vcf.gz` 

- Then the original, unzipped .vcf file was inspected to count lines without comments using this command:
`zcat NA12891_CEU_sample.vcf.gz | grep -v '^#' | wc -l`

_The resulting count was 41 lines._ The output can be seen here: 

![](https://uchicago.box.com/shared/static/nax961u81vtgvapef8pvzwb9n4tvbfk0.png)


***
- The next step was to install samtools and get a bam file to manipulate with samtools. This was done by:

- Cloning the htslib repo using: `git clone --recurse-submodules git://github.com/samtools/htslib.git`

- Cloning the samtools: `git clone https://github.com/samtools/samtools.git`

- Changing the pwd to samtools and compiling it: `cd samtools/` and `make`

- Adding samtools to my environment: `export PATH=~/samtools:$PATH`

- Getting a sample .bam file from github: `wget https://github.com/vsbuffalo/bds-files/raw/master/chapter-11-alignment/NA12891_CEU_sample.bam`


***
-Atfer setting up samtools, and getting a bam file, we try out some operations.  

- Samtools was used to view the .bam file and grep was used to view sample names with this command: 

`samtools view -H NA12891_CEU_sample.bam | grep '@RG'`

`echo $?`

- A fastq was made out of the bam file using: 

`samtools fastq NA12891_CEU_sample.bam > NA12891_CEU_sample.fastq`

`echo $?`

__N.B__: `echo $?` was used to check that the previous command ran successfully. An output of 0 confirms a successful run.


- A .sam file was made out of the .bam file using: 

`time samtools view -h NA12891_CEU_sample.bam > NA12891_CEU_sample.sam`

bam -> sam took about 1 second to run.

The output can be seen here: 

![](https://uchicago.box.com/shared/static/4b7yo2dhuf374omninwgucb1n5m0i2w3.png)

- The sizes of the files in the directory were displayed using: `ll`

The output can be seen here: 

![](https://uchicago.box.com/shared/static/9lawdkh66a1ji01awif9tw687aw5olgn.png)

_This showed that the .bam file is the smallest, with the .fastq file larger than the .bam file, and the .sam file is the largest of the three_


-The .fastq file was removed to conserve space by running:

`rm NA12891_CEU_sample.fastq`


***
- The .sam file was converted to a .bam file and sorted. This was run such that the execution time was given as an output using this command:

`time samtools view -bS NA12891_CEU_sample.sam | samtools sort -o NA12891_CEU_sample_file_sorted.bam`

.sam -> sorted.bam took about 15 seconds to run. The output can be seen here: 

![](https://uchicago.box.com/shared/static/4zr3wodcjuzo6tarjhocixkul1sd2ebo.png)


- The .bam file was indexed  using this command:

`samtools index NA12891_CEU_sample_file_sorted.bam`

- The header file was checked using:

`samtools view -H NA12891_CEU_sample_file_sorted.bam`

- The list of files and their sizes were displayed by running: `ll` 

The output can be seen here: 

![](https://uchicago.box.com/shared/static/jgxh9lm6o43au0n7bk2t64z4k84npjq2.png)

_From the output we can see that sample.bam file is larger than the sorted.bam file._


***
- To conserve memory space, the following commands were ran to remove the .bam and .sam files from the directory.

`rm NA12891_CEU_sample.bam` 

`rm NA12891_CEU_sample.sam`
 
`rm NA12891_CEU_sample_file_sorted.bam` 

