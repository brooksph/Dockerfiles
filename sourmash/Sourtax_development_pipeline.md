sourmash-tax

Install instructions

Starting from a blank Ubuntu 16.04 with 100 of RAM install, run:

	sudo apt-get update && sudo apt-get -y install python3.5-dev \
	python3-virtualenv python3-matplotlib python3-numpy g++ make

then create a new virtualenv, :

	cd
	python3.5 -m virtualenv env -p python3.5 --system-site-packages
	. env/bin/activate

You'll need to install a few things, including a recent version of khmer:

	pip install screed pytest PyYAML doit
	pip install git+https://github.com/dib-lab/khmer.git

Next, grab the sbt_search branch of sourmash:

	cd
	git clone https://github.com/dib-lab/sourmash.git -b sbt_search
	
Build and install it:

	cd sourmash && make install

Next, grab this repo:

	cd
	git clone https://github.com/dib-lab/2016-sourmash-tax.git

The sourtax.py script will now be in ~/2016-sourmash-tax/pipeline/sourtax.py. See the demo section below.

	screen 

Download data 

	cd  2016-sourmash-tax/scripts
	./retrieve_lindreen_seqs.sh
	
Concatenate
	
	./concatenate_lindgreen_seqs.sh	
	rm -r *1.fq.gz
	rm -r *0.fq.gz

Interleave reads 
	
	./interleave_reads.sh
	rm -r *1.cat.fq.gz
	rm -r *2.cat.fq.gz
	
Trim the sequences 
	
	for i in $(cat interleaved_seqs.txt)
	do
	load-into-counting.py -M 16e9 -k 20 $i.kh $i
	abundance-dist.py -s $i.kh $i $i.dist
	./plot-abundance-dist.py $i.dist $i-dist.png --ymax=300
	done
	
	for i in $(cat interleaved_seqs.txt)
	do
	abundance-dist.py -s $i.kh $i $i.dist
	./plot-abundance-dist.py $i.dist $i-dist.png --ymax=300
	done
	
	abundance-dist.py -s setA1.interleaved.cat.fq.gz.kh setA1.interleaved.cat.fq.gz setA1.dist

Preparing a database

To prepare a database, you need to calculate sourmash signatures that contain the k-mer cardinality of the source sequence. This can be done using the --with-cardinality option:

	sourmash compute --with-cardinality genome.fa

If you have a FASTA file containing a bunch of genomes, sourmash will by default calculate a single signature for all of them; you can fix this by using the --singleton option:

	sourmash compute --singleton --with-cardinality all-genomes.fa

Once you have the signatures calculated, you can build the database (here named 'db') with sourmash sbt_index:

	sourmash sbt_index db *.sig

So, for example, to build a search database from the data/15genome.fa.gz file included in this repo, do:

sourmash compute --singleton --with-cardinality data/15genome.fa.gz
sourmash sbt_index db 15genome.fa.gz.sig