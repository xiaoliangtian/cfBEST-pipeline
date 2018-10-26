## Getting started

	https://github.com/Genetalks/cfBEST-pipeline.git 
	perl cfBEST_pipeline.pl -ss demo.config –pc panel.config -bc barcode.config -ref hg19.fasta -id pID -indir dir/ 
	-outdir outdir/ -threads 10 -min_depth 4


## Introduction

	cfBEST_pipeline is a software package for analyzing the next-generation sequencing data generated by the cfBEST 
	method, which is a cfDNA single molecule counting system. By calculating the mutation frequency and deducing the
	genotypes,cfBEST is capable of non-invasively detecting maternal and fetal genotypes without the prior information
	of parental genotypes. 

	cfBEST_pipeline consists of five steps: 1)pre-processing (include data merging, barcode trimming, adapter trimming
	and primer recognizing), 2)mapping and filtering, 3)consensus sequence calling, 4)allele counting, 5)genotyping.
	Its accuracy is guaranteed though precise adapter trimming by recognizing the overlap between pair ends, accurate
	re-alignment of indel closed to read end, counting depth by specific primers in specific sites, and a comprehensive
	genotyping statistic model.


## Usage
	-ss        	<file> 		Input sample sheet file, required
  	-id      	<str>     	config ID in panel config file, required
  	-indir    	<dir>      	raw fastq dir, "*.R1.fastq.gz", required
  	-pc       	<file>    	Input panel config file, [config/panel.config]
  	-bc       	<file>    	Input barcode config file, [config/barcode.config]
	-ref       	<file>    	Input ref genome file(fasta), [config/hg19.fastq]
  	-outdir 	<dir>    	outdir of output file, [./]
	
  	--SingleEnd         		SingleEnd, read2 as second barcode, optional
  	--Double_Barcode    		Double barcode, optional
  	--Abnormal_Recognize   		abnormal sample recognize, optional
 	-min_depth     	<int>    	depth threshold to filter low depth reads group, [4]
 	-min_CV       	<float>  	min depth CV=(max-sec)/max when call consensus sequence, [0.65]
  	-mdepth_snp    	<int>    	min depth of snp to infer cffDNA, [100]
  	-msize         	<int>    	intersize threshold of consensus sequence,[2000]
  	-secBar_len    	<int>    	length of r2 seq as barcode when specified --SingleEnd, [9]
  	-mismatch_bar1 	<int>    	mismatch of the first barcode, [1]
  	-mismatch_bar2 	<int>    	mismatch of the second barcode, [1]
  	-parallel      	<int>     	jobs num of parallel, [4]
  	-threads       	<int>    	threads num, [6]
  	-step          	<int>    	step, [1]
        				  	0: call data from Undetermined
        					1: data merge
        					2: barcode process
        					3: primer process
        					4: consensus maker
        					5: variants detect
						6: genotyping
        					7: merge statistic and abnormal sample recognize
  	-h         			Help

#### Format of data sheet file (-ss flag): 

Col|Name|description
:-:|:---|:---------
1|SampleID|Required, Sample ID
2|Index|Required, Sample Index
3|BarcodeGroup|Required, Specified barcode group IDs used in the sample, detailed barcode sequence is listed in file ./config/barcode.config
4|SampleType|Required, Sample type, include gravida-plasma-thalassemia and standards-thalassemia and other, represent: 1) infer fetal fraction and detect genotype, 2) detect genotype based on supplied fetal fraction, 3) neither infer fetal fraction nor detect genotype but only detect variants frequency, respectively.
5|Description|Required, Sample description
6-8|Genotypes|Optional, Fetal maternal and paternal genotype, it will judge True or False when supplied. N represent negative, N/N, xx/N, xx/xx represent three heterozygosis state.

	Example:
	S1    CTTCGTT B6,B7,B8        gravida-plasma-thalassemia      sample descriptions     -28/N   -28/N   CD41-42/N

#### Format of panel config file (-pc flag):

Col|Name|Description
:-:|:---|:---------
1|PanelID|Required Panel ID
2|TargetBedFile|Required File, 4 column: chr start end primerID
3|PrimerInfoFile|Required File, 5 column: chr pos strand primerID primerSeq
4|SpecificPrimerFile|Required File, 5 column: chr start end primerID distance
5|FusionFile|Optional File, 1) fusion region, 4 column: “Region” chr start end; 2)fusion breakpoints, 6 column: “Break” fusionID chr1 pos1 chr2 pos2
6|TargetMutationFile|Required File, 6 column: chr start end ref alt info(ID=xxx;GENE=xxx; STRAND=xxx; CDS=xxx;AA=xxx)



#### Format of barcode config file (-bc flag):

Col	|Description
:-:	|:----------
1	|Barcode group ID
2	|Barcode sequence

