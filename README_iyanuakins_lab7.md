#### __README : EDITED COMMAND HISTORY DURING MSBI32400_LAB7__
#### __Date Run: Wed, 2/23/2022__
#### __Topic: Quality Control for GWAS__
#### __Tools used: plink, R, Slurm, Shebang__

***
__Creating the working directory for the lab__

- A new directory `lab7` was created with its subdirectories `data`, `bin`, `source`,`doc` and `results` within my home directory using:

    `mkdir -p lab7/{data,bin,source,doc,results}`

***  
__Sourcing for/Setting up work files to run the lab__

- In my home directory I cloned a GitHub repository (repo) with this command:
    `git clone https://github.com/MareesAT/GWA_tutorial.git`
        
- This creates a new directory named `GWA_tutorial`. Within the newly created directory, I unzipped the `1_QC_GWAS.zip` file using: 

    `cd GWA_tutorial/`

    `unzip -q 1_QC_GWAS.zip`
    
- This creates a new directory named `1_QC_GWAS`. This directory contains all the files needed for this lab and those files will be redistributed into the lab7 sub-directories.

***
__Redistributing the Working files to lab 7 subdirectories.__

- I moved the files within the `1_QC_GWAS` directory to the sub-directories in my lab7 directory, by running the following commands from the `1_QC_GWAS` directory:

     `cd 1_QC_GWAS`
        
     `mv *.R ~/lab7/source`   (this moves all R files to the source sub-directory within the lab7 directory)

     `mv 1_Main_script_QC_GWAS.txt ~/lab7/doc` (this moves the specified file to the doc sub-directory within the lab7 directory)

     `mv *.bim ~/lab7/data` (this moves all .bim files to the data sub-directory within the lab7 directory)

     `mv *.fam ~/lab7/data` (this moves all .fam files to the data sub-directory within the lab7 directory)

     `mv *.bed ~/lab7/data`  (this moves all .bed files to the data sub-directory within the lab7 directory)

     `mv inversion.txt ~/lab7/data` (this moves the specified file to the data sub-directory within the lab7 directory)

     `mv pops_HapMap_3_r3 ~/lab7/data`  (this moves the specified file to the data sub-directory within the lab7 directory)

- Then I used `tree ~/lab7` to check that all files have been redistributed to the correct sub-directories.

- The two main tools for this lab are R and plink and I loaded their modules using:

    `module load R`

    `module load plink`

***
__INVESTIGATING MISSINGNESS PER INDIVIDUAL__

- I changed the pwd to the results sub-directory within the lab7 directory using : `cd ~/lab7/results`

- Then generated documents on the missingness per individual and per SNP using the information in the HapMap_3_r3_1 files from the data sub-directory with this command:

    `plink --bfile ../data/HapMap_3_r3_1 --missing`

- The output is two files named `plink.imiss` and `plink.lmiss` in the pwd (ie the results sub-directory). These files show the proportion of missing SNPs per individual (plink.imiss) and the proportion of missing individuals per SNP (plink.lmiss).

***
__VISUALIZING THE MISSINGNESS__

The aim was to make histograms for the missigness reported in `plink.imiss` and `plink.lmiss`.
I utilized the R script named `hist_miss.R` and made a shebang/submission script named `missingnessPlot.sh`

To achieve this:
- I moved to the source directory:
    `cd ~/lab7/source`
 
- I created the submission script uisng this command: `vim missingnessPlot.sh`

- The content of the script was as seen below:

> **NOTE:**
> __This script will be modified to create subsequent `.sh` files__. 
>
> __The difference will be in the Job # and the file path for the required .R file for the job__.

![](https://uchicago.box.com/shared/static/gnsd32xjya2t8q2p4xwng32xd9vbccb4.png)


- The `hist_miss.R` file is edited to show the file paths for the `plink.imiss` and `plink.lmiss` files and the storage path for the two histograms (__histimiss.pdf__ and __histlmiss.pdf__) to be generated.

- Here's what the R script looked like before and after making changes:

![](https://uchicago.box.com/shared/static/nrbqcaguc4zezi5t8w9u8uma6ifw9gkk.png)

- The generated pdfs can be viewed using the firefox module on the cluster with the following commands:

    `module load firefox`

    `firefox histimiss.pdf`

    `firefox histlmiss.pdf`

- Alternatively, they can be copied to your computer and viewed with your available pdf viewer.

- Still within the source directory, I changed the user permission of the missingnessPlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 missingnessPlot.sh`
    
    `sbatch missingnessPlot.sh`

> The __histimiss.pdf__ is a histogram plot of missing SNPs per individual. It is right skewed with a frequency range of `0 to >80` on the y-axis and rate of `0.0000 to 0.0020` on the x-axis.

> The __histlmiss.pdf__ is a histogram plot of missing individuals per SNP. It is right skewed with a frequency range of `0e + 00` to `1e + 06` on the y-axis and rate of `0.00 to 0.50` on the x-axis.

***
__DATA CLEANING__

- I changed directory using:
  
  `cd ~/lab7/data` 

- Then ran the following commands:

    `plink --bfile HapMap_3_r3_1 --geno 0.2 --make-bed --out HapMap_3_r3_2` (To delete SNPs with missingness > 0.2)

    `plink --bfile HapMap_3_r3_2 --mind 0.2 --make-bed --out HapMap_3_r3_3`  (To delete individuals with missingness > 0.2)

    `plink --bfile HapMap_3_r3_3 --geno 0.02 --make-bed --out HapMap_3_r3_4` (To delete individuals with missingness >0.02)

    `plink --bfile HapMap_3_r3_4 --mind 0.02 --make-bed --out HapMap_3_r3_5` (To delete  individuals with missingness >0.02.)

- These commands will generate .bed .bim .fam .hh .log extensions of the respective HapMap_3_r3 file.

***

__SEX DISCREPANCY CHECK__

In the working dataset, subjects are classified in to male or female based on their F-Value.
F values of subjects >0.8 were assigned Male and those <0.2 were assigned Female. 
Those who failed to meet this requirement were tagged as "Problem" by plink.

I used this command to check for sex discrepancies:   `plink --bfile HapMap_3_r3_5 --check-sex`
    
    
__VISUALIZING SEX DISCREPANCY__

The aim was to make histograms for the sex divisions reported in `plink.sexcheck`
I utilized the R script named `gendercheck.R` and made a shebang/submission script named `gendercheckPlot.sh`
To achieve this:

- I moved to the source directory:
    `cd ~/lab7/source`
 
- I created the submission script uisng this command: `vim gendercheckPlot.sh`

- The content of the missingnessPlot.sh was copied and modified in the new .sh file. 

- The changes were the Job # (JOB2) and the file path (~/lab7/source/) for the required .R file (`gendercheck.R`)__.

- The R file is edited to show the file path for the `plink.sexcheck` file and the storage path for the three histograms (Men_check.pdf, Gender_check.pdf, and Women_check.pdf) to be generated.

- Still within the source directory, I changed the user permission of the gendercheckPlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 gendercheckPlot.sh`
    
    `sbatch gendercheckPlot.sh`

__DELETING SEX DISCREPANCY__

- The pwd was changed to the data subdirectory. 
- One sex discrepancy was found by searching for "PROBLEM" within the plink.sexcheck file, and another file named sex_discrepancy.txt was generated to save it with this command:

    `grep "PROBLEM" plink.sexcheck | awk '{print$1,$2}'> sex_discrepancy.txt`

- The file content is shown below:

![](https://uchicago.box.com/shared/static/zydezrzi1lip0fq0yse4rtthg3uq05kd.png)

- Then the sex discrepancy was removed from the working dataset and a new one (HapMap_3_r3_6) was created with this command:

`plink --bfile HapMap_3_r3_5 --remove sex_discrepancy.txt --make-bed --out HapMap_3_r3_6`

***
__Dealing with low minor allele frequencies (MAF)__

Still within the data sub-directory I ran the following commands to select autosomal SNPs and save them in a txt file, generate another dataset without the identified autosomal SNPs, and generate the minor allele frequencies.

  awk '{if($1 >= 1 && $1 <= 22)print $2}' HapMap_3_r3_6.bim > snp_1_22.txt 

  `plink --bfile HapMap_3_r3_6 --extract snp_1_22.txt --make-bed --out HapMap_3_r3_7`

  `plink --bfile HapMap_3_r3_7 --freq --out MAF_check`


__PLOTTING MAF DISTRIBUTION__

The aim was to make a plot of MAF reported in `MAF_check`
I utilized the R script named `MAF_check.R` and made a shebang/submission script named `MAFcheckPlot.sh`

To achieve this:

- I moved to the source directory:
    `cd ~/lab7/source`
 
- I created the submission script uisng this command: `vim MAFcheckPlot.sh`

- The content of the missingnessPlot.sh was copied and modified in the new .sh file. 

- The changes were the Job # (JOB3) and the file path (~/lab7/source/) for the required .R file (`MAF_check.R`).

- The R file is edited to show the file path for the `MAF_check.frq` and the storage path for the histogram (MAF_distribution.pdf) to be generated.

- Still within the source directory, I changed the user permission of the MAFcheckPlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 MAFcheckPlot.sh`
    
    `sbatch MAFcheckPlot.sh`
    
- I moved to the data directory: `cd ~/lab7/data`

- Then removed SNPs with a low MAF with this command: `plink --bfile HapMap_3_r3_7 --maf 0.05 --make-bed --out HapMap_3_r3_8`


***

__Assessing Hardy Weinberg equilibrium (HWE)__

- Still within the data directory, the distribution of HWE p-values for all SNPs was found using: `plink --bfile HapMap_3_r3_8 --hardy`

- The genotyping rate was **0.998039** and  a file named `plink.hwe` was generated within the directory.

- A picture of the genotyping rate is shown below:

![](https://uchicago.box.com/shared/static/s48yrzcp22356jli96t9s5f8nl9a6cww.png)


- SNPs with HWE p-value below the treshold of 0.00001 were selected and saved into a file named `plinkzoomhwe.hwe` within the current directory using the command:

 awk '{ if ($9 <0.00001) print $0 }' plink.hwe>plinkzoomhwe.hwe


__PLOTTING HWE__

The aim was to make two HWE plots out of `plink.hwe` and `plinkzoomhwe.hwe`

I utilized the R script named `hwe.R` and made a shebang/submission script named `hwePlot.sh`

To achieve this:

- I moved to the source directory:
    `cd ~/lab7/source`
 
- I created the submission script uisng this command: `vim hwePlot.sh`

- The content of the missingnessPlot.sh was copied and modified in the new .sh file. 

- The changes were the Job # (JOB4) and the file path (~/lab7/source/) for the required .R file (`hwe.R`).

- The R file is edited to show the file path for `plink.hwe` and `plinkzoomhwe.hwe`, and the storage path for the two histograms (histhwe.pdf and histhwe_below_threshold.pdf) to be generated.

- Still within the source directory, I changed the user permission of hwePlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 hwePlot.sh`
    
    `sbatch hwePlot.sh`

- I moved to the data directory: `cd ~/lab7/data`

- Then filtered out variables in the control and case data with respective thresholds of 1e-6 and 1e-10 using the respective commands:

    `plink --bfile HapMap_3_r3_8 --hwe 1e-6 --make-bed --out HapMap_hwe_filter_step1`

    `plink --bfile HapMap_hwe_filter_step1 --hwe 1e-10 --hwe-all --make-bed --out HapMap_3_r3_9`
        
    
 ***
 
 __ASSESSING HETEROZYGOSITY__
 
Checks for heterozygosity are performed on a set of SNPs which are not highly correlated. Therefore, to generate a list of non -highly correlated SNPs, high inversion regions as stated on the `inversion.txt` file  were excluded. Then SNPs were pruned using these commands:

    `plink --bfile HapMap_3_r3_9 --exclude inversion.txt --range --indep-pairwise 50 5 0.2 --out indepSNP`
    
    `plink --bfile HapMap_3_r3_9 --extract indepSNP.prune.in --het --out R_check`

- __959189 variants__ were removed. as seen below:

![](https://uchicago.box.com/shared/static/u7m0nn0evzvfl1g8dbr8fnf7p3g60lj2.png)


__PLOTTING HETEROZYGOSITY DISTRIBUTION__

The aim was to make a plot out of `R_check.het`

I utilized the R script named `check_heterozygosity_rate.R` and made a shebang/submission script named `check_heterozygosity_ratePlot.sh`

To achieve this: 

- I moved to the source directory:
    `cd ~/lab7/source`
 
- I created the submission script uisng this command: `vim check_heterozygosity_ratePlot.sh`

- The content of the missingnessPlot.sh was copied and modified in the new .sh file. 

- The changes were the Job # (JOB5) and the file path (~/lab7/source/) for the required .R file (`check_heterozygosity_rate.R`).

- The R file is edited to show the file path for `R_check.het`, and the storage path for the file (heterozygosity.pdf) to be generated.

- Still within the source directory, I changed the user permission of check_heterozygosity_ratePlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 check_heterozygosity_ratePlot.sh`
    
    `sbatch check_heterozygosity_ratePlot.sh`


__REMOVE HETEROZYGOSITY RATE OUTLIERS__

A list of individuals who deviate more than 3 standard deviations from the heterozygosity rate mean was generated and saved into the file `fail-het-qc.txt`

I utilized the R script named `heterozygosity_outliers_list.R` and made a shebang/submission script named `heterozygosity_outliers_listPlot.sh`

To achieve this still within the source directory:
 
- I created the submission script uisng this command: `vim heterozygosity_outliers_listPlot.sh`

- The content of the missingnessPlot.sh was copied and modified in the new .sh file. 

- The changes were the Job # (JOB6) and the file path (~/lab7/source/) for the required .R file (`heterozygosity_outliers_list.R`).

- The R file is edited to show the file path for `R_check.het`, and the storage path for the file (fail-het-qc.txt) to be generated.

- Still within the source directory, I changed the user permission of heterozygosity_outliers_listPlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 heterozygosity_outliers_listPlot.sh`
    
    `sbatch heterozygosity_outliers_listPlot.sh`

- __2 OUTLIERS__ were found as seen below:

![](https://uchicago.box.com/shared/static/6vlycdoussf6cgva7a1t9ki89gjgeqf6.png)

- I moved to the results directory using: `cd ~/lab7/results`

- To make the fail-het-qc.txt file plink compatible removed all quotation marks from the file and selected only the first two columns using: 

    `sed 's/"// g' fail-het-qc.txt | awk '{print$1, $2}'> het_fail_ind.txt`


- I moved to the data directory using: `cd ~/lab7/data 

Removed outliers enlisted in `het_fail_ind.txt` using: 

    `plink --bfile HapMap_3_r3_9 --remove ~/lab7/results/het_fail_ind.txt --make-bed --out HapMap_3_r3_10`


***

__ASSESSING CRYPTIC RELATEDNESS BETWEEN DATASETS__

- Based on the assumption that the dataset has random population samples, all individuals with the pihat threshold of 0.2 and above were excluded using: 

    `plink --bfile HapMap_3_r3_10 --extract indepSNP.prune.in --genome --min 0.2 --out ~/lab7/results/pihat_min0.2`

    `awk '{ if ($8 >0.9) print $0 }' ~/lab7/results/pihat_min0.2.genome> ~/lab7/results/zoom_pihat.genome`
    
    
- I utilized the R script named `Relatedness.R` and made a shebang/submission script named `RelatednessPlot.sh`

To achieve this still within the source directory:
 
- I created the submission script uisng this command: `vim RelatednessPlot.sh`

- The content of the missingnessPlot.sh was copied and modified in the new .sh file. 

- The changes were the Job # (JOB7) and the file path (~/lab7/source/) for the required .R file (`Relatedness.R`).

- The R file is edited to show the file path for `zoom_pihat.genome` and `pihat_min0.2`, and the storage path for the files (
hist_relatedness.pdf, relatedness.pdf, zoom_relatedness.pdf) to be generated.

- Still within the source directory, I changed the user permission of RelatednessPlot.sh to give owner and group full permissions and submitted it to Slurm with these commands:

    `chmod 770 RelatednessPlot.sh`
    
    `sbatch RelatednessPlot.sh`
    
- I moved to the data directory using: `cd ~/lab7/data`    

- The individuals without parents in the dataset known as Founders were excluded by filtering. This was done to show that the relatedness observed was due to parent-offspring relationships.

    `plink --bfile HapMap_3_r3_10 --filter-founders --make-bed --out HapMap_3_r3_11`


- To create a file with founders having pihat of 0.2 and above:

    `plink --bfile HapMap_3_r3_11 --extract indepSNP.prune.in --genome --min 0.2 --out ~/lab7/results/pihat_min0.2_in_founders`


The two individuals left in the `pihat_min0.2_in_founders` file have __different FIDs so these two are not of the same family. Their pihat value is 0.4924__

- To remove individuals with the lowest call rate:

    `plink --bfile HapMap_3_r3_11 --missing`

- A new file named `0.2_low_call_rate_pihat.txt` containing the sample with the lower call was generated. The file contained the  FID (13291) and the IID (NA07045).

- The picture below shows the file components of pihat_min0.2_in_founders and 0.2_low_call_rate_pihat.txt:

![](https://uchicago.box.com/shared/static/sey6863g10vwpacaxw9bro8evvypwvgu.png)


- The individual/sample with the lower call was deleted using: 

    `plink --bfile HapMap_3_r3_11 --remove 0.2_low_call_rate_pihat.txt --make-bed --out HapMap_3_r3_12`
