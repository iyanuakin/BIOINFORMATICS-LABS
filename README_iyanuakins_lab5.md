#ndme.### __README : EDITED COMMAND HISTORY DURING MSBI32400_LAB5__
#### __Date Run: Wed, 2/9/2022 to Sun, 2/9/2022__
#### __Topic: Probing The Genome__

***
- A new directory `lab5` was created with its subdirectories `data`, `bin`, `source`,`doc` and `results` within my home directory using:

    `mkdir -p lab5/{data,bin,source,doc,results}`

***  
- I changed my working directroty to `lab5/data` and installed "AuriCrow's data" using:
    
    `wget --no-check-certificate-O 7657.23andme.6002 https://opensnp.org/data/7657.23andme.6002?1531411209`

- The genotype file format was inspected using:
    
    `head 7657.23andme.6002`

- I counted the number of lines in the 23andme file using: 
    
    `grep -v '^#' 7657.23andme.6002 | wc -l`
    
    ___The resluting number was 638,469___ .
    
    ___Add the columns of information are: rsid, chromosome, position, and genotype___

***
- I changed my pwd to using: `cd ~/lab5/bin/` to download the software that will be used to convert files to vcf using:

    `wget https://github.com/plantimals/2vcf/releases/download/v0.4.0/2vcf_0.4.0_Linux_x86_64.tar.gz` 
    
- I created a new directory within the lab5/bin directory called by running: `mkdir 2vcf`
  
  Then I changed my directory to it using: `cd 2vcf`

   I untarred the downloaded tar file into the 2vcf directory using:
   
   `tar -zxvf ../2vcf_0.4.0_Linux_x86_64.tar.gz`
   
- The reference vcf was downloaded in lab5/bin using:

    `wget https://github.com/plantimals/2vcf/raw/develop/reference/reference.vcf.gz`
    
- Then it was converted to a vcf file using:

    `./2vcf conv --input=/home/iyanuakins/lab5/data/7657.23andme.6002 --ref=/home/iyanuakins/lab5/bin/reference.vcf.gz 23andme`
    
- The lines in the vcf file were counted using: 
    
    `zcat 7657.23andme..vcf.gz | grep -v '^#' | wc -l`
    
    The resluting number of lines was ___185,545 lines___.
    
    This is different from the number of lines counted in the original file. __The difference between the original file and the vcf version is: _452,924 lines___
    
    This is because of the different proprietary identifiers used by 23andme. The output of counting the lines in the original and vcf versions can be seen below:
    
    ![](https://uchicago.box.com/shared/static/ort09wn8ms2q9ld8rz5jd8dzzut1rq21.png)
    
    
- From my home directory, I installed snpeff using:

    `wget https://snpeff.blob.core.windows.net/versions/snpEff_latest_core.zip`

- The downloaded file was unzipped using:

    `unzip snpEff_latest_core.zip`
    
- I loaded a more recent java module using: `module load java/15.0.2` 

- Then checked the command syntax using: `java -Xmx2G -jar snpEff/snpEff.jar`


***

- From the snpEff directory in my home directory, I downloaded a reference genome database using: 

    `java -jar snpEff.jar -download -v GRCh37.75`
    
- From my lab5/results directory, I using:

    `java -Xmx2G -jar /home/iyanuakins/snpEff/snpEff.jar eff -canon -noLog GRCh37.75 /home/iyanuakins/lab5/data/7657.23andme..vcf.gz >7657.23andme.6002.snpEff.vcf &`
   
   -I viewed the annotated version using vim and searched for lines containing __stop__ by running:

    `vim 7657.23andme.6002.snpEff.vcf` and typing `/stop` in the vim text editor.
    
    Then I navigated through the search with `n`. I noticed annotations such as:
    
    - The stop_lost or stop_gained description
    
    - The severity represented as HIGH, MEDIUM or LOW
    
    - The Ensembl number
    
    - The Gene name, etc.
    
    The search output is shown below:
    
    ![](https://uchicago.box.com/shared/static/z0p8gcvtql4dwt9shyaetvbw68ef6sb3.png)
   
   
- From my lab5/results directory, I opened the summary file using: `firefox snpEff_summary.html`
    
    __This shows 3 entries (0.001%) were classified as stop_lost, and 245 entries (0.101%) were classified as stop_gained.__
    
    The output can be seen here:
    
    ![](https://uchicago.box.com/shared/static/baidyxuo2efo5evrqek33dlvnd9hm5f0.png)

***
- I changed my pwd by running: `cd ~/snpEff/data/GRCh37.75`. Then made a new directory called __clinvar__ and made it my pwd using:

    `mkdir clinvar` and `cd clinvar`
    
- I downloaded the latest GRCh37 VCFs in the .gz and .gz.tbi formats using:

    `wget https://ftp.ncbi.nlm.nih.gov/pub/clinvar/vcf_GRCh37/clinvar_20220205.vcf.gz`

    `wget https://ftp.ncbi.nlm.nih.gov/pub/clinvar/vcf_GRCh37/clinvar_20220205.vcf.gz.tbi`

- I annotated snpEff.vcf with SnpSift using:

    `java -Xmx2G -jar ~/snpEff/SnpSift.jar annotate -noLog ~/snpEff/data/GRCh37.75/clinvar/clinvar_20220205.vcf.gz ~/lab5/results/7657.23andme.6002.snpEff.vcf > ~/lab5/results/7657.23andme.6002.clinvar.snpEff.vcf`

- From `lab5/results,` viewed the annotated version using vim and searched for lines containing __CLNSIG=Pathogenic__ by running:

    `vim 7657.23andme.6002.clinvar.snpEff.vcf` and typing `/CLNSIG=Pathogenic` in the vim text editor.
    
    Then I navigated through the search with `n`. Three of the pathogenic rsIDs that I found were:
    
    - rs28940291 with gene name MFN2
    
    - rs28935470 with gene name FLNA
    
    - rs72554664 with gene name G6PD
    
    The search output is shown below:
    
    ![](https://uchicago.box.com/shared/static/eg0j0ylhzlx481i82k6p26ci8stkqp87.png)
    
    
***

- I changed my pwd to `~/lab5/data` then viewed the original version of the 23andme file using vim and searched for the required SNPs using the given rsIDs. Below are the outputs of the rsIDs, their star allele representation and screenshots of the rsID searches: 

    - rs1800462 = CC = *1/*1
    
    - rs1142345 = TT = *1/*1
    
    - rs1800460 = CC = *1/*1
    
    - rs1800584 = CC = *1/*1

    ![](https://uchicago.box.com/shared/static/immchzcsxlij0limbf7uwgq88l1ybmbc.png)

