####
	sourmash compute -k 11,21,31,41,51,61,71,81,91,101,201,301 --dna -n 0 --scaled 10000 --singleton mircea.fa

### make SBT
	for i in 11 21 31 41 51 61 71 81 91 101 201 301
	do
	sourmash sbt_index -k $i --dna mircea_$i mircea.fa.sig
	done
	
#### build a signature from the metagenome

	sourmash compute -k 11,21,31,41,51,61,71,81,91,101,201,301 --dna --scaled 10000 -n 0 mircea.fa

#### search

	for i in 11 21 31 41 51 61 71 81 91 101 201 301
	do
		sourmash sbt_gather -k $i --dna mircea_${i} mircea.fa.sig --threshold=0.001 -o mircea.fa.sig.k.${i}.txt
	done 
	
----------------------------------------------------------------------------
build signature for Thermotoga_petrophila_RKU-1

	sourmash compute -k 11,21,31,41,51,61,71,81,91,101,201,301 --dna -n 0 --scaled 10000 --singleton GCF_000016785.1_ASM1678v1_genomic.fna.gz

SBT search for Thermotoga_petrophila_RKU-1 against mircea 

	for i in 11 21 31 41 51 61 71 81 91 101 201 301
	do
		sourmash sbt_search -k $i --dna mircea_${i} GCF_000016785.1_ASM1678v1_genomic.fna.gz.sig --threshold=0.001 --save-matches GCF_000016785.1_ASM1678v1_genomic.fna.gz.sig.${i}.txt --best-only 
	done

make pull request for -o for sbt_search 

currently --save-matches but should be[-o OUTPUT]

parser.add_argument('-o', '--output', type=argparse.FileType('wt'))

----------------------------------------------------------------------------

Perform same analysis with microbes 15, 31, 51


	
#### build a signature from the metagenome

	sourmash compute -k 15,31,51 --dna --scaled 10000 -n 0 --singleton mircea.2.fa

submit job to que
	
	qsub -I -l nodes=1:ppn=1,mem=100gb,walltime=76:00:00 -N mircea_sbt_search

#### search

	for i in 15 31 51
	do
		sourmash sbt_gather -k $i --dna ~ctb/sigs/microbes.k${i}.sbt.json mircea.2.fa.sig --threshold=0.001 -o microbes.2.k${i}.txt
	done 
	
	
Stopped here

Ask Titus if he has a microbes.sig laying around somewhere or if he has microbes.sbt.json with larger k values
--------------------------------------------------------------------------	
Next, raw reads

submit to que 

	qsub -I -l nodes=1:ppn=1,mem=100gb,walltime=48:00:00 -N mircea_raw_reads 

####
	sourmash compute -k 11,21,31,41,51,61,71,81,91,101,201,301 --dna -n 0 --scaled 10000 --singleton mircea.fa

### make SBT
	for i in 11 21 31 41 51 61 71 81 91 101 201 301
	do
		sourmash sbt_index -k $i --dna mircea_$i mircea.fa.sig
	done
	
#### build a signature from the raw reads 

	sourmash compute -k 11,21,31,41,51,61,71,81,91,101,201,301 --dna --scaled 10000 -n 0 SRR606249.pe.qc.fq.gz.abundtrim.gz

#### search

	for i in 11 21 31 41 51 61 71 81 91 101 201 301
	do
		sourmash sbt_gather -k $i --dna mircea_${i} SRR606249.pe.qc.fq.gz.abundtrim.gz.sig --threshold=0.001 -o SRR606249.pe.qc.fq.gz.abundtrim.gz.sig.${i}.txt
	done 
	
and then against microbes if possible

----

	sourmash compute -k 15,31,51 --dna --scaled 10000 -n 0 SRR606249.pe.qc.fq.gz.abundtrim.gz

	submit job to que

	qsub -I -l nodes=1:ppn=1,mem=100gb,walltime=76:00:00 -N mircea_sbt_search
search

	for i in 15 31 51
	do
    	sourmash sbt_gather -k $i --dna ~ctb/sigs/microbes.k${i}.sbt.json mircea.2.fa.sig --threshold=0.001 -o SRR606249.pe.qc.fq.gz.abundtrim.gz.sig${i}.txt
	done 


Sourmash compare w/o singleton

Sbt search 
Import txt files 


	../signatures/GCF_000007225.1_ASM722v1_genomic.fna.gz.sig 
	../signatures/GCF_000008085.1_ASM808v1_genomic.fna.gz.sig 
	../signatures/GCF_000015805.1_ASM1580v1_genomic.fna.gz.sig 
	../signatures/GCF_000016385.1_ASM1638v1_genomic.fna.gz.sig 
	../signatures/GCF_000016785.1_ASM1678v1_genomic.fna.gz.sig 
	../signatures/GCF_000018565.1_ASM1856v1_genomic.fna.gz.sig 
	../signatures/GCF_000091545.1_ASM9154v1_genomic.fna.gz.sig 
	../signatures/GCF_000091665.1_ASM9166v1_cds_from_genomic.fna.gz.sig


