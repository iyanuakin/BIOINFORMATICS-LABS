#### __README : EDITED COMMAND HISTORY DURING MSBI32400_LAB4__
#### __Date Run: Wed, 2/2/2022__
#### __Topic: BLAST__

***
- A new directory `lab4` was created with its subdirectories `data`, `bin`, `src`,`doc` and `results` within my home directory using:

`mkdir -p lab4/{data,bin,src,doc,results}`

***  
- I changed my working directroty to `lab4/data` and installed the NCBI BLAST tool using:
`wget https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ncbi-blast-2.12.0+-x64-linux.tar.gz`

- The download was untarred into its own directory within the current directory using:

`tar -zxvf ncbi-blast-2.12.0+-x64-linux.tar.gz`

- Within the ncbi directory a new directory "db" was created. And from the "db" directory the following commands were used to retrieve the human reference proteome from UniProt and extract the fasta file:

`wget https://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/reference_proteomes/Eukaryota/UP000005640/UP000005640_9606.fasta.gz`

`gunzip UP000005640_9606.fasta.gz`

- The lines that have the FASTA annotation were counted using: 
`grep -v '^>' UP000005640_9606.fasta| wc -l` 
The output was __200010 lines__ and can be seen in:

![](https://uchicago.box.com/shared/static/vt4ikx0cz2d97n230jbe46h0juyvgnn3.png)

- I shared the BLAST  binaries with the server and confirmed it using the following:

`PATH=$PATH:/home/iyanuakins/lab4/data/ncbi-blast-2.12.0+/bin`

`export PATH=$PATH:/home/iyanuakins/lab4/data/ncbi-blast-2.12.0+/bin`

`echo $PATH`

***

- From the home directory the following commands were used to create a hidden directory: 
`vim ~/.ncbirc`

- The syntax in the directory was inserted using vim as:
```
; Start the section for BLAST configuration [BLAST]
; Specifies the path where BLAST databases are installed
BLASTDB=/home/iyanuakins/lab4/data/ncbi-blast-2.12.0+/db
```


- Still in the home directory, the new hidden directory was confirmed using: `ls -al`

***

- The directory was changed to the "db" directory using: `cd lab4/data/ncbi-blast-2.12.0+/db`

- A new BLASTdb was created using: 
`~/lab4/data/ncbi-blast-2.12.0+/bin/makeblastdb -in UP000005640_9606.fasta -parse_seqids -dbtype prot`

- The eutilities were used to get fasta files using the following commands:

`esearch -db protein -query "NP_000509" | efetch -format fasta > hbb.fasta`

`esearch -db protein -query "NP_976312" | efetch -format fasta > myog.fasta`

- Other fasta files were copied from the class directory to my `~/lab4/data` directory using:

`cp /project2/msbi32400/unknown.fasta ~/lab4/data`

`cp /project2/msbi32400/unknown_fragment.fasta ~/lab4/data`

`cp /project2/msbi32400/unknown2.fasta ~/lab4/data`

***

- The directory was changed to the "lab4/results/" directory. The proteins were BLASTed and viewed on firefox by running the following commands: 

- To BLAST the hbb.fasta:

`~/lab4/data/ncbi-blast-2.12.0+/bin/blastp -query ~/lab4/hbb.fasta -db ~/lab4/data/ncbi-blast-2.12.0+/db/UP000005640_9606.fasta -html -out ~/lab4/results/mysearch.html && firefox mysearch.html`

The output for the BLASTed __hbb.fasta had a top hit with the E-value : 4e - 107__  

- To BLAST the unknown.fasta:

`~/lab4/data/ncbi-blast-2.12.0+/bin/blastp -query ~/lab4/data/unknown.fasta -db ~/lab4/data/ncbi-blast-2.12.0+/db/UP000005640_9606.fasta -html -out ~/lab4/results/mysearch4.html && firefox mysearch4.html`

The output for the BLASTed __unknown.fasta had a top hit called; P699905 - Hemoglobin subunit alpha__. The output for the BLASTed hbb.fasta (left) and unknown.fasta (right) files can be seen here: 

![](https://uchicago.box.com/shared/static/2eg2jtl43eerj4cpt1wbyptc2l6jff0t.png)

- To BLAST the myog.fasta:

`~/lab4/data/ncbi-blast-2.12.0+/bin/blastp -query ~/lab4/myog.fasta -db ~/lab4/data/ncbi-blast-2.12.0+/db/UP000005640_9606.fasta -html -out ~/lab4/results/mysearch2.html && firefox mysearch2.html`


- To BLAST the unknown2.fasta: 

`~/lab4/data/ncbi-blast-2.12.0+/bin/blastp -query ~/lab4/data/unknown2.fasta -db ~/lab4/data/ncbi-blast-2.12.0+/db/UP000005640_9606.fasta -html -out ~/lab4/results/mysearch3.html && firefox mysearch3.html`

-The output for the BLASTed unknown2.fasta file can be seen here: 
![](https://uchicago.box.com/shared/static/tjz5u2t7qdob14iba378o4xu22mk5ih9.png)

- From the output, we see that one of the AAs wasn’t matched which explains why identities = 146/147 (99%). The base in the query is __V (Valine) and the reference sequence is E (Glutamaic acid)__. Ignoring the first M in the sequence, the position with a mismatch is __the 6th position__. Putting all these together I believe this is comparing a sickle celled/(HbS) sequence with the reference sequence.

***

- The unknown.fasta file was uploaded from my directory to the NCBI portal (https://blast.ncbi.nlm.nih.gov/Blast.cgi) using the firefox module on the server. After BLASTing with BLASTP, the output showed that it is an Amino Acid sequence for hemoglobin alpha2. It’s E-value is __1e -97__ and the output can be seen here: 

![](https://uchicago.box.com/shared/static/1xpf8yjqpvwc2euemp476voe71nxbnme.png) 

- Still on the NCBI Portal, a 16S ribosomal search was ran on Accession number _NR_119358.1_. The output shows that it is __Acinetobacter baumannii strain ATCC 19606. It’s E-value is 0.0__ and the output can be seen here: 

![](https://uchicago.box.com/shared/static/z0oaa0zrl63ph7qn0lapugb8n3j638yl.png)

- The Finally the sequence type of the unknown_fragment.fasta file was determined with the comand: `head unknown_fragment.fasta`.

On the NCBI Portal, the unknown_fragment.fasta file was uploaded with the assumption that it was a human sequence. After BLASTing it, the output showed it to be an __Amino Acid sequence from the BRCA1 in humans. It’s E-value is 0.0__ and the output can be seen here: 

![](https://uchicago.box.com/shared/static/eyv2s9vok3dqkv15c5513ao8esxvyc6e.png)
