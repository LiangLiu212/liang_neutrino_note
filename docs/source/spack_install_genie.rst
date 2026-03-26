=====================================
Building GENIE with Spack Environments
=====================================

Overview
========

This guide provides a framework for using Spack environments to manage the build and installation of GENIE (Generates Events for Neutrino Interaction Experiments). Using Spack environments ensures reproducible builds, easy dependency management, and support for multiple configurations.


Spack Environment Framework
============================

Creating a subspack instance for GENIE development
--------------------------------------------------

If you have access to a shared CVMFS Spack instance running Spack 1.0 or later, you can create a chained Spack instance using the ``subspack`` extension. This approach allows you to:

- Reuse pre-built packages from the shared instance
- Install additional packages into your own local, writable environment
- Avoid redundant build times for commonly used dependencies


.. code-block:: console

    # first, source the spack in cvmfs
    $ source /cvmfs/larsoft.opensciencegrid.org/setup-env.sh
    
    # create a subspack instance and use it
    $ spack subspack --with-padding /exp/sbnd/app/users/liangliu/spack_genie/subspackenv
    # You only need to source your own subspack instance next time login
    $ source /exp/sbnd/app/users/liangliu/spack_genie/subspackenv/setup-env.sh


In this subspack, you will already have access to the compilers that are available in the upstream instance, so you’ll only need to install a compiler if you want to use one that isn’t already in that upstream instance.

Managing Subspack Environment Configurations
--------------------------------------------

Now, you can create a environment for your development. 

.. code-block:: console

    # list the exsit enviroment in spack
    $ spack env list
    ==> 4 environments
        larsoft-v10_11_01-unified-cuda-python-3_9-trimmed
        larsoft-v10_11_01-unified-cuda-python-3_10-trimmed-m2
        larsoft-v10_11_01-unified-cuda-python-3_9-ipykernel
        larsoft-v10_11_01-unified-cuda-python-3_10-trimmed
    
    # create a new enviroment in your subspack instance
    $ spack env create my_genie_env
    ==> Created environment my_genie_env in: /exp/sbnd/app/users/liangliu/spack_genie/subspackenv/var/spack/environments/my_genie_env
    ==> Activate with: spack env activate my_genie_env

    # active your enviroment
    $ spack env activate my_genie_env


Add packages from upstreams
---------------------------

Now you can add package from upstreams to your own enviroment. You can find all the installed packages in cmvfs

.. code-block:: console

    # you need to deactivate your enviroment to find all the installed package
    $ spack env deactivate
    $ spack find --long

For example we can add root to your enviroment.

.. code-block:: console

    $ spack find --long root
    -- linux-almalinux9-x86_64_v2 / %c,cxx=gcc@12.5.0 ---------------
    jybpsif root@6.28.12  47uztdp root@6.28.12

    -- linux-almalinux9-x86_64_v3 / %c,cxx=gcc@12.5.0 ---------------
    kmcoa5w root@6.28.12
    ==> 3 installed packages

We can find there are three installations of root in our system. ``jybpsif``, ``47uztdp``, ``kmcoa5w`` are the hash for each ones.
Now we can go back to our enviroment again and add your favorite one.

.. code-block:: console

    $ spack env activate my_genie_env
    $ spack add root@6.28.12/kmcoa5w
    $ spack concretize
    $ spack find 
    ==> In environment my_genie_env
    ==> 1 root specs
    -- no arch / no compilers ---------------------------------------
    [^] root@6.28.12

    -- linux-almalinux9-x86_64 / no compilers -----------------------
    autoconf@2.69    fontconfig@2.14.0  glibc@2.34     libxext@1.3.4   nasm@2.15.03      texinfo@6.7
    automake@1.16.2  freetype@2.10.4    gmake@4.3      libxft@2.3.3    ncurses@6.2       util-macros@1.19.3
    curl@7.76.1      gawk@5.1.0         libice@1.0.10  libxpm@3.5.13   openssl@3.2.2     xextproto@2024.1
    diffutils@3.7    gcc@11.5.0         libsm@1.2.3    m4@1.4.19       perl@5.32.1       xproto@2024.1
    expat@2.5.0      gdbm@1.23          libtool@2.4.6  mesa@24.2.8     pkg-config@1.7.3  xtrans@1.4.0
    findutils@4.8.0  gettext@0.21       libxcb@1.13.1  mesa-glu@9.0.1  readline@8.1      zlib@1.2.11

    -- linux-almalinux9-x86_64_v2 / %c,cxx=gcc@11.5.0 ---------------
    binutils@2.45  gcc@12.5.0  gmp@6.3.0  isl@0.26  zstd@1.5.7

    -- linux-almalinux9-x86_64_v2 / %c=gcc@11.5.0 -------------------
    mpc@1.3.1  mpfr@4.2.1

    -- linux-almalinux9-x86_64_v2 / no compilers --------------------
    autoconf-archive@2023.02.20  compiler-wrapper@1.0  gcc-runtime@11.5.0

    -- linux-almalinux9-x86_64_v3 / %c,cxx=gcc@12.5.0 ---------------
    bison@3.8.2   flex@2.6.3     libffi@3.5.2         ninja@1.13.0          py-cython@0.29.36  root@6.28.12   xxhash@0.8.3
    cmake@3.31.9  ftgl@2.4.0     libjpeg-turbo@3.0.4  nlohmann-json@3.12.0  py-numpy@1.26.4    unuran@1.11.0
    davix@0.8.10  gsoap@2.8.135  libpng@1.6.47        pcre@8.45             python@3.10.19     vdt@0.4.6
    fcgi@2.4.4    icu4c@76.1     lz4@1.10.0           postgresql@18.0       re2c@3.1           xrootd@5.8.4

    -- linux-almalinux9-x86_64_v3 / %c,fortran=gcc@12.5.0 -----------
    fftw@3.3.10  pythia6@6.4.28

    -- linux-almalinux9-x86_64_v3 / %c=gcc@12.5.0 -------------------
    bzip2@1.0.8   gsl@2.8                    isa-l@2.31.1   libx11@1.8.12           rngstreams@1.0.1      yasm@1.3.0
    giflib@5.2.2  inputproto@2.3.2           json-c@0.18    libxcrypt@4.4.38        sqlite@3.50.4
    gl2ps@1.4.2   intel-oneapi-mkl@2024.2.2  kbproto@1.0.7  libxml2@2.13.5          util-linux-uuid@2.41
    glew@2.2.0    intel-oneapi-tbb@2022.3.0  libiconv@1.18  py-meson-python@0.18.0  xz@5.6.3

    -- linux-almalinux9-x86_64_v3 / %cxx=gcc@12.5.0 -----------------
    rapidjson@1.2.0-2024-08-16

    -- linux-almalinux9-x86_64_v3 / no compilers --------------------
    compiler-wrapper@1.0  glx@1.4      py-flit-core@3.12.0  py-pip@25.1.1                py-setuptools@80.9.0  py-wheel@0.45.1
    gcc-runtime@12.5.0    meson@1.8.5  py-packaging@25.0    py-pyproject-metadata@0.9.1  py-tomli@2.0.1        python-venv@1.0
    ==> 107 installed packages
    ==> 0 concretized packages to be installed (show with `spack find -c`)


Now, you have added root to your local enviroment as well as all the dependencies. You can load it now and check 

.. code-block:: console

    $ spack load root
    $ which root
    /exp/sbnd/app/users/liangliu/spack_genie/subspackenv/var/spack/environments/my_genie_env/.spack-env/view/bin/root

Using your subspack instance
----------------------------

* Install Marley

.. code-block:: console

    $ spack load root@6.28.12
    $ spack load gcc@12.5.0
    $ git clone git@github.com:MARLEY-MC/marley.git
    $ cd marley
    $ source setup_marley.sh
    $ cd build
    $ make -j N

Build your setup env script ``setup_env.sh``

.. code-block:: bash

    source /exp/sbnd/app/users/liangliu/spack_genie/subspackenv/setup-env.sh
    spack env activate my_genie_env
    spack load root@6.28.12
    spack load gcc@12.5.0 

You only need to source your ``setup_env.sh`` next time you login.


Install INCL++ and GENIE
------------------------

INCL++ and GENIE have dependencies, ``root``, ``boost``, ``geant4``, ``log4cpp``, ``lhapdf``, and ``eigen``.
You can edit your ``spack.yaml`` for your local enviroment.

.. code-block:: console

    # make sure your are in your local enviroment and go to your enviroment directory
    $ spack cd --env

You can add following specs into your ``spack.yaml``. 

.. code-block:: yaml

    # This is a Spack Environment file.
    #
    # It describes a set of packages to be installed, along with
    # configuration settings.
    spack:
    # add package specs to the `specs` list
    specs:
    - root@6.28.12
    - boost+date_time+program_options+timer ^gcc@12.5.0
    - geant4@10.6.1
    - log4cpp@1.1.3/25koh6z
    - lhapdf@6.5.5/szlqpnt
    - eigen@3.4.1/p7jsx6o
    view: true
    concretizer:
        unify: true


After that, you should be able to concretize it and install all the missing package

.. code-block:: console

    $ spack concertize
    $ spack install

Go to your workarea for your GENIE, 

.. code-block:: console

    $ cd /path/to/your/GENIE

You need to create a script to setup GENIE enviroment.

.. code-block:: bash

    #source /cvmfs/larsoft.opensciencegrid.org/spack-fnal-v1.0.0/setup-env.sh
    #source /cvmfs/larsoft.opensciencegrid.org/setup-env.sh
    source myspack/setup-env.sh
    spack env activate mymarley
    #spack load gcc/kwnheq5
    spack load gcc@12.5.0
    spack load root arch=linux-almalinux9-x86_64_v3
    spack load pythia6 arch=linux-almalinux9-x86_64_v3
    spack load log4cpp arch=linux-almalinux9-x86_64_v3
    spack load lhapdf arch=linux-almalinux9-x86_64_v3
    spack load libxml2 arch=linux-almalinux9-x86_64_v3
    spack load boost
    spack load geant4 arch=linux-almalinux9-x86_64_v3
    spack load gsl arch=linux-almalinux9-x86_64_v3
    #spack load ifdhc arch=linux-almalinux9-x86_64_v3 /rijacye
    spack load xrootd arch=linux-almalinux9-x86_64_v3
    #spack load cetlib arch=linux-almalinux9-x86_64_v3 /bdzdpju
    spack load eigen
    #spack load clhep@2.4.7.1 arch=linux-almalinux9-x86_64_v3 /3cnnbft
    export_path(){
    package_env=${1}
    package=${2}
    package_hash=`spack find --loaded --format "{hash}" ${package}`
    export ${package_env}=`spack location -i /${package_hash}`
    }


    export_path PYTHIA6 pythia6
    export_path LOG4CPP log4cpp
    export_path LHAPDF6 lhapdf
    export_path LIBXML2 libxml2
    export_path BOOST boost
    export_path GEANT4 geant4
    export_path GSL_PATH gsl
    export_path GCC_PATH gcc
    #export_path CETLIB cetlib
    #export_path CLHEP clhep
    export PYTHIA6_LIB_DIR=${PYTHIA6}/lib
    #export LHAPDF6=`spack location -i lhapdf`
    #export LOG4CPP=`spack location -i log4cpp`
    #export LIBXML2=`spack location -i libxml2`
    #export BOOST=`spack location -i boost`
    #export GEANT4=`spack location -i geant4`
    current_dir="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
    source ${current_dir}/inclxx_genie/install/bin/thisinclxx.sh
    echo "Setting GENIE environment variables..."


    # Finds the directory where this script is located. This method isn't
    # foolproof. See https://stackoverflow.com/a/246128/4081973 if you need
    # something more robust for edge cases (e.g., you're calling the script using
    # symlinks).
    THIS_DIRECTORY="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
    export GENIEBASE=$THIS_DIRECTORY
    export GENIE=$GENIEBASE/Generator
    export XSECSPLINEDIR=$GENIEBASE/data
    export GENIE_REWEIGHT=$GENIEBASE/Reweight
    export PATH=$GENIE/bin:$GENIE_REWEIGHT/bin:$PATH
    #export LD_LIBRARY_PATH=$GENIE/lib:$GENIE_REWEIGHT/lib:${LOG4CPP}/lib:${PYTHIA6}/lib:${GSL_PATH}/lib:${LHAPDF6}/lib:${LIBXML2}/lib:${BOOST}/lib:${GEANT4}/lib64:${GCC_PATH}/lib64:$LD_LIBRARY_PATH:`root-config --libdir`:${CLHEP}/lib
    export LD_LIBRARY_PATH=$GENIE/lib:$GENIE_REWEIGHT/lib:${LOG4CPP}/lib:${PYTHIA6}/lib:${GSL_PATH}/lib:${LHAPDF6}/lib:${LIBXML2}/lib:${BOOST}/lib:${GEANT4}/lib64:${GCC_PATH}/lib64:$LD_LIBRARY_PATH:`root-config --libdir`
    export LIBRARY_PATH=${LIBRARY_PATH}:${PYTHIA6_LIB_DIR}
    source ${THIS_DIRECTORY}/marley/setup_marley.sh
    unset GENIEBASE
    unset current_dir

In principle, you should be able to git clone branches from github. For this instruction, you can just copy the INCL++, GENIE, and GENIEReweight code from my area,

.. code-block:: console

    $ cp /exp/sbnd/app/users/liangliu/Maria/Generator .
    $ cp /exp/sbnd/app/users/liangliu/Maria/inclxx_genie .
    $ cp /exp/sbnd/app/users/liangliu/Maria/Reweight .

* build INCL++

.. code-block:: console

    $ cd inclxx_genie/build
    $ cmake ../inclxx/ -DINCL_DEEXCITATION_ABLA07=on -DINCL_DEEXCITATION_SMM=ON -DINCL_DEEXCITATION_GEMINIXX=ON -DBUILD_SHARED_LIBRARY=ON -DUSE_INSTALL_PATH=ON -DCMAKE_INSTALL_PREFIX=/<your_inclxx_path>/install
    $ make -j N
    $ make install

* build GENIE

.. code-block:: console

    $ cd $GENIE
    $ bash do_configure.sh
    $ make -j N

* build GENIE Reweight

.. code-block:: console

    $ cd $GENIE_REWEIGHT
    $ make -j N




See Also
========

- `Spack Documentation <https://spack.readthedocs.io/>`_
- `GENIE Homepage <http://www.genie-mc.org/>`_
- `Marley Homepage <https://www.marleygen.org/>`_
- `Spack at Fermilab <https://fnalssi.github.io/spack-at-fnal/>`_
