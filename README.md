ARGs_OAP_v2.0 manual
===============

**New release of ARGs_OAP verion 2.0**  

**Novel features**

1. The SARG database was expanded about three times in the version 2.0
2. SARGfam was supplied
3. Cell number estimation with universial single-copy marker gene was added
4. Add -q for quality control of raw fastq files
5. <span style="color:red">Local version of stage two script</span>
6. <span style="color:red">Adding option -s for very big input data (useful for users without 64bit usearch to split big input automatically)</span>,
 

**We have simplified the running process of stage_one. We have made a step by step video about how to use ARGs-OAP platform, hopefully, this video will guid new users to go through the process within ten minutes.  The address is: https://www.youtube.com/watch?v=PCr1ctXvZPk**    

**As many users have very big data to analyze, we supplied a local version for the stage two analysis in the near future along with SARG2.0 database. Some users report that the Galaxy has problem for uploading data in China, please refreash the page and upload again, the server is not stable as previous after upgrading.**

**The DATABASE FILE of [SARG2.0](https://github.com/biofuture/Ublastx_stageone/blob/master/DB/SARG_20170328_12307.fasta) and the [structure](https://github.com/biofuture/Ublastx_stageone/blob/master/DB/structure_20170328_12037.LIST) of the database are under the DB directory.**


**A mirror site was added in Shenzhen China for mainland China users to solve the slow data uploading problem [SUSTC-MIRROR-ARGS-OAP](http://smile.sustc.edu.cn:8080/)**


Repo of ARGs_OAP2.0 
Repo of ARGs_OAP1.0 [Version 1.0](https://github.com/biofuture/Ublastx_stageone/releases/tag/Ublastx_stageone) 


**1. adding a method to obtain microbial community structure from the shotgun metagenomics data set.**  
**2. adding copy number correction using Copyrighter database and normalize ARGs abundance by cell number.**  

Detail introduction of copy number correction can be referred to [Transform ARGs abundance against cell number](https://github.com/biofuture/Ublastx_stageone/wiki/Transform-ARGs-abundance-against-cell-number)

There are some questions raised by users, please refer to the [FAQ](https://github.com/biofuture/Ublastx_stageone/wiki/FAQ) for details.  

To run Ublastx, users should download the stage one source code into local computer system (Unix/Linux) and upload the generated files for stage two onto our Galaxy analysis platform (http://smile.hku.hk/SARGs). 

What does Ublastx do:  
=====================

1. Fast environmental searching of antibiotic resistant gene in multiple metagenomics data sets; the ARGs abundance can be normalized to cell number
2. Generate mother table of type and sub-type level ARGs of users' samples and a merged sub-type level mother table    
3. Generate a PcoA of users samples with other typical environment samples such as human gut, ocean and sediment to show the relationship of user concerned samples with already sequenced environment.  

clone source code into local computer
=====================================
    git clone  https://github.com/biofuture/Ublastx_stageone.git  

Prepare the meta-data file of your samples  
==========================================
To run the stage one pipeline, users need to prepare relative meta-data.txt file and put all the pair-end fastq file into one directory  
Example of meta-data file **meta-data.txt**  Tips:   
* You need keep the first and second column's name as SampleID and Name
* The SampleID are required to be numbers counting from 1 to 2 to 3 etc.
* Category is the classification of your samples into groups and we will colored your samples in PcoA by this informaton
* The meta-data table should be separated by tabular for each of the items 
* The Name of each sample should be the fastq file names for your pair-end Illumina sequencing data, your fastq files will automatically be recognized by Name_1.fq and Name_2.fq, so you need to keep the name consistent with your fq file name. (if you files are end with .fastq or .fasta, you need to change them to end with .fq or .fa)
 
**Please make sure the meta-data file is pure txt format, if you edit the file under windows, using nodepad++ and check the end of each line by cliking View-> Show Symbol -> Show All Characters. If the line is end up with CRLF, please remove the CR by replace \r to nothing in the replace dialogue frame**

SampleID | Name | Category 
---------|------|-----------
 1       | STAS | ST    
 2       | SWHAS104 | SWH  

Prepare database and usearch
============================

SARG Database and 32 bit usearch is avaliable in DB/ and bin/ directory, respectively. **Users donot need to download CARD and ARDB anymore!!**

Stage one pipeline
==================
When meta-data.txt and database files are prepared, then put all your fastq files into one directory in your local system (notice the name of your fastq files should be Name_1.fq and Name_2.fq). your can give -h to show the help information. Examples could be found in source directory example, in example directory run test:   

`nohup ../ublastx_stage_one  -i inputfqs -o testoutdir -m meta-data.txt -n 8`

    Usage: ./ublastx_stage_one -i <Fq input dir> -m <Metadata_map.txt> -o <output dir> -n [number of threads] -f [fa|fq] -z -h  -c    
            Author: JIANG Xiaotao
        Date: 12-11-2014
        Modidied : 31-01-2018
        Email: biofuture.jiang@gmail.com
        ./ublastx_stage_one_version2 -i <Fq input dir> -m <Metadata_map.txt> -o <output dir> -n [number of threads] -f [fa|fq] -z -h -c [S|U]
        -i Input files directory, required
        -m meta data file, required
        -o Output files directory, default current directory
        -n number of threads used for usearch, default 1
        -f the format of processed files, default fq
        -z whether the fq files were .gz format, if -z, then firstly gzip -d, default(none) 
        -x evalue for searching 16S in usearch default 1e-10
        -y evalue for searching universal single copy marker gene default 3
        -v the identity value for diamond to search the USCMGs default  0.45
        -c This option is to chose methods for estimating the prokyarto cell number  using copy number correction by Copywriter database to transfrom 16S information into cell number [ direct searching hyper variable region database by usearch] opetin "S", or using 30 universal single copy marker genes of prokyarto averagely coverage to estimate the cell number (Here, we ignore other eukaryota sequences in one metagenomics sample) represented by "U", (default U)
        -h print this help information

This step will search reads against SARG databbase and 16S greengene non-redundant 85 OTUs database to identify potential ARG reads and 16S reads. This step will generate searching results files for each fastq.  This step also obtain the microbial community structure information of samples by searching against hyper-variable region database, and then perform copy number correction using Copyrighter copy number database (release date) to finally estimate the cell number of samples. 
 
The results are in testoutdir/, it looks like this:

    extracted.fa                  STAS_2.16s                        SWHAS104.16s_hyperout.txt
    meta_data_online.txt          STAS_2.us                         SWHAS104_1.us
    STAS_1.16s                    STAS.extract_1.fa                 SWHAS104_2.16s
    STAS.16s_1v6.us               STAS.extract_2.fa                 SWHAS104_2.us
    STAS.16s_2v6.us               SWHAS104_1.16s                    SWHAS104.extract_1.fa
    STAS.16s_hvr_community.txt    SWHAS104.16s_1v6.us               SWHAS104.extract_2.fa
    STAS.16s_hvr_normal.copy.txt  SWHAS104.16s_2v6.us               ublastx_bash_Mon-Feb-1-16:20:59-2016.sh
    STAS.16s_hyperout.txt         SWHAS104.16s_hvr_community.txt
    STAS_1.us                     SWHAS104.16s_hvr_normal.copy.txt

The **extracted.fa** and **meta_data_online.txt** are two files needed for ublastx_stage_two analysis. The STAS.16s_hvr_community.txt is the microbial community of sample STAS and STAS.16s_hvr_normal.copy.txt is the averagely copy number of the microbial community after CopyRighter database correction.

The meta-data-online.txt looks like this 

SampleID | Name | Category | #ofreads | #of16S| **#ofCell**
---------|------|-----------|----------|-------|-------- 
 1       | STAS | ST  |200000 | 10.1  |   4.9
 2       | SWHAS104 | SWH  |200000 | 9.7 |    4.1

Stage two pipeline on Galaxy system and download results
========================================================
Go to http://smile.hku.hk/SARGs  and using the module ARG_OAP.  

1. Using **ARG_OAP** -> **Upload Files** module to upload the extracted fasta file and meta_data_online.txt file generated in stage one into Galaxy  
2. Click **ARG_OAP** and **Ublast_stagetwo**, select your uploaded files  
3. For \"Column in Metadata:\" chose the column you want to classify your samples (default: 3)

Click **Execute** and you can find four output files for your information

After a while or so, you will notice that their are four files generated for your information.  
 
**File 1 and 2**: PcoA figures of your samples and other environment samples generated by ARGs abundance matrix normalization to 16s reads number and cell number  
**File 3 and 4**: Other tabular mother tables which including the profile of ARGs type and sub type information, as long as with other environment samples mother table. File3 results of ARGs abundance normalization aganist 16S reads number; File 4 results of ARGs abundance normalization aganist cell number

------------------------------------------------------------------------------------------------------------------------  
**Notice:**

This tools only provide the required scripts for ARGs-OAP1.0/2.0 pipeline

This pipeline is distributed in the hope to achieve the aim of management of antibiotic resistant genes in envrionment, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.This pipeline is only allowed to be used for non-commercial and academic purpose.

**The SARG database is distributed only freely used for academic prupose, any commercial use should require the agrrement from the developer team.** 



The copyrights of the following tools/databases which could be used in this pipeline belong to their original developers. The user of this pipeline should follow the guideline and regulations of these tools/database which could be found at the websites of their developers.  


1)     Usearch: (http://www.drive5.com/usearch/)

2)     Copyrighter: (http://microbiomejournal.biomedcentral.com/articles/10.1186/2049-2618-2-11)

3)     Greengenes: (http://greengenes.lbl.gov/cgi-bin/nph-index.cgi)

Please check the above websites for details of these tools/databases.
