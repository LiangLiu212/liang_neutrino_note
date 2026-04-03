==================
Run NUISANCE
==================

Overview
--------

This section describes how to install and run NUISANCE to convert GENIE output to flat tree format for further analysis.

Prerequisites
-------------

Before installing NUISANCE, ensure you have:

- GENIE installed (see :doc:`/spack_install_genie`)
- INCL installed
- spack environment set up

Installation
------------

Follow these steps to install NUISANCE:

1. **Setup spack environment and navigate to your work area** (the same directory where you installed GENIE and INCL):

.. code-block:: bash

    cd <your_work_area>

2. **Clone the NUISANCE repository**:

.. code-block:: bash

    git clone https://github.com/NUISANCEMC/nuisance.git
    cd nuisance

3. **Create and enter the build directory**:

.. code-block:: bash

    mkdir build
    cd build

4. **Configure NUISANCE with cmake**:

.. code-block:: bash

    cmake -DGENIE_ENABLED=ON \
          -DNuWro_ENABLED=OFF \
          -DNEUT_ENABLED=OFF \
          -DGiBUU_ENABLED=OFF \
          -DCMAKE_BUILD_TYPE=Debug \
          -DProb3plusplus_ENABLED=ON \
          -Dnusystematics_ENABLED=OFF \
          -DNuHepMC_ENABLED=OFF \
          ../

5. **Compile and install**:

.. code-block:: bash

    make
    make install

Setup
-----

After installation, you need to source the NUISANCE setup script:

.. code-block:: bash

    source <your_path>/nuisance/build/Linux/setup.sh

Make sure to replace ``<your_path>`` with the actual path to your NUISANCE installation.

Converting GENIE to Flat Tree
------------------------------

Once NUISANCE is set up, you can convert GENIE GHEP output to flat tree format using the ``nuisflat`` command:

.. code-block:: bash

    nuisflat -i GENIE:${outdir}/${file_name}.gprep.root -o ${outdir}/${file_name}.flat.root

**Parameters:**

- ``-i GENIE:${outdir}/${file_name}.gprep.root`` : Input GENIE file path
- ``-o ${outdir}/${file_name}.flat.root`` : Output flat tree file path

Replace ``${outdir}`` with your output directory and ``${file_name}`` with your GENIE file name.
