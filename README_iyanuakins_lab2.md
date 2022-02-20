#### __EDITED COMMAND HISTORY DURING MSBI32400_LAB2: CLASS AND HOMEWORK__
#### __Date Run: Wed, 1/19/2022 - Sun, 1/23/2022__

***
  - __To check and load the python module and load sypder__

     [Line] `Command` 

      122  `module avail python`

      123  `module load python`

      124  `spyder`

***
  - __To make the lab2 directory with its component directories__
    
     [Line] `Command`

      136  `mkdir -p lab2/{bin,data,doc,results,src}`
      
***
  - __To make a readme file within the doc directory of lab2__

    [Line] `Command`
    
     137  `touch lab2/doc/README.md`
   
   
***
  - __Moving to the home directory to download and install command line tools from NCBI. And to create a path for edirect__
  
    [Line] `Command`
          
      138  `cd ~`

      139  `sh -c "$(wget -q ftp://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh -O -)"`

      140  `export PATH=${PATH}:${HOME}/edirect`

***
  - __Moving to the doc directory to download and install pubmed files using eutilities(eg. esearch, efetch)__

      141  `cd lab2/doc`

      160  `esearch -db pubmed -query "nahlawi AND prostate" | efetch -format pubmed > doc/example1.txt`

      165  `esearch -db pubmed -query "bioinformatics [MAJR] AND software [TIAB]" | efetch -format xml | xtract -pattern PubmedArticle -block Author -sep " " -tab "\n" -element LastName,Initials | sort-uniq-count-rank > doc/bioinformatics_authors.txt`

      166  `esearch -db protein -query 'NP_000509.1' | efetch -format fasta > doc/hbb.fasta`

      167  `esearch -db snp -query '(FLT3[Gene Name]) AND "pathogenic"[Clinical Significance]' | efetch -format uid > doc/flt3_pathogenic.txt`

      169  `ll`

      174  `vim bioinformatics_authors.txt`

 *** 
  - __Running the igv GUI from the home directory to check out the HBB gene and Exons__
  
     [Line] `Command`

      175  `cd ~`

      176  `ls`

      177  `cd IGV_Linux_2.11.9`

      178  `./igv.sh`


***
  - __To check the contents of the lab2 directory__
  
     [Line] `Command`

      199  `tree lab2`

***
  - __Moving to the doc directory to append my history to the README.md file, then rename and inspect the file__

     [Line] `Command`

      200  `cd lab2/doc`

      201  `ll`

      202  `history`

      203  `history >> README.md`

      226  `mv README.md README_iyanuakins.md`

      227  `ll`

      228  `vim README_iyanuakins.md`
  
***  
  - __Loading the pandoc module to create a html version of the readme file.__

     [Line] `Command`    

      249  `module load pandoc`

      250  `pandoc -r markdown -w html README_iyanuakins.md > PandocOutput.html`


***
  - __Loading the firefox module to view the html file__
   
     [Line] `Command`

      251  `module load firefox`

      253  `firefox PandocOutput.html`

  
  ***
  - __To inspect the files in my doc directory using vim__
   

     [Line] `Command`

      260  `vim hbb.fasta`

      261  `vim bioinformatics_authors.txt`

      262  `vim README_iyanuakins.md`
  
  ***
  - __To overwrite the existing README file with an updated version of my history and text edit with vim__
    
     [Line] `Command`

      263  `history`

      264  `history > README_iyanuakins.md`
      
      265   `vim README_iyanuakins.md` 


***
  - __Creating a html version of the updated readme file and loading the firefox module to view the html file__
   
     [Line] `Command`

      268  `pandoc -r markdown -w html README_iyanuakins.md > PandocOutput.html`
     
      269  `module load firefox`

      270  `firefox PandocOutput.html`
