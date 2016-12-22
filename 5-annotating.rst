================================
5. Annotating your transcriptome
================================

dammit!

`Dammit <http://www.camillescott.org/dammit/index.html>`__ is an annotation pipeline written with `pydoit <http://pydoit.org/>`__ by `Camille Scott <http://www.camillescott.org/>`__. Dammit translates an unannotated transcriptome with `Transdecoder <http://transdecoder.github.io/>`__ then uses the following protein databases as evidence for annotation: Pfam-A, Rfam, OrthoDB, uniref90 (optional with `--full`)

Install stuff
=============

::

    # ami-002f0f6a
    # Ubuntu 15.10, m4.xlarge with 100GB storage
    # In addition to install from 0-install-aws.rst, do these:

    sudo apt-get -y install python3-dev python-numpy ruby hmmer unzip \
        infernal ncbi-blast+ liburi-escape-xs-perl emboss liburi-perl \
        build-essential libsm6 libxrender1 libfontconfig1 \
        parallel libx11-dev
    sudo gem install crb-blast

Install miniconda

::

    curl -LO https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
    bash Miniconda3-latest-Linux-x86_64.sh
    # Press Enter x 26
    # yes
    # Enter
    # yes
    
    source ~/.bashrc

Create a python 3 environment for dammit

::

    conda create -n dammit anaconda python=3.5
    # y
    source activate dammit
    # source deactivate

Install shmlast

::

    conda install --file <(curl https://raw.githubusercontent.com/camillescott/shmlast/master/environment.txt)
    # y
    pip install --upgrade pip
    pip install shmlast

Install last

::

    cd
    curl -LO http://last.cbrc.jp/last-658.zip
    unzip last-658.zip
    pushd last-658 && make && make install prefix=~ && popd

Install the proper version of GNU parallel:

::

    cd 
    (wget -O - pi.dk/3 || curl pi.dk/3/ || fetch -o - http://pi.dk/3) | bash
    sudo cp /home/ubuntu/bin/parallel /usr/bin/parallel

Transdecoder

::

    cd
    curl -LO https://github.com/TransDecoder/TransDecoder/archive/2.0.1.tar.gz
    tar -xvzf 2.0.1.tar.gz
    cd TransDecoder-2.0.1; make

Put everything in the path:

::

    export PATH=$HOME/last-658/src:$PATH
    export PATH=$HOME/last-658/scripts:$PATH
    export PATH=$HOME/busco:$PATH
    export PATH=$HOME/TransDecoder-2.0.1:$PATH

Install the proper version of matplotlib

::

    pip install https://pypi.python.org/packages/source/m/matplotlib/matplotlib-1.5.1.tar.gz

Finally, install dammit from the refactor/1.0 branch

::

    pip install https://github.com/camillescott/dammit/archive/refactor/1.0.zip

Install databases (takes ~15-20 min)
# Is there a faster install?
# Don't need everything?

::

    dammit databases --install


Run the dammit pipeline

::

    # after trinity
    deactivate

    cd ${PROJECT}
    mkdir -p annotation
    cd annotation
    ln -s ${PROJECT}/assembly/trinity_out_dir/Trinity.fasta .

    source activate dammit

Run the command:

::

    dammit annotate Trinity.fasta --busco-group metazoa --n_threads 2
    
If dammit runs successfully, there will be a directory "Trinity.fasta.dammit" with ~dozen files, including "Trinity.fasta.dammit.gff3" and "Trinity.fasta.dammit.fasta".  If the command is run again, there will be a message: "**Pipeline is already completed!**"
