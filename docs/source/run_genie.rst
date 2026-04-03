====================
Run GENIE simulation
====================

Overview
--------

This section describes how to run GENIE simulations for neutrino interaction studies.

Prerequisites
-------------

Before running GENIE simulations, ensure you have:

- GENIE installed

Installation and Setup
----------------------

The installation of GENIE+INCL con be found in 

:doc:`/spack_install_genie`

Configuration
-------------

Prepare your simulation configuration:

- Define physics models
- Set beam energy and flux
- Specify target materials
- Configure output options

MicroBooNE Example
~~~~~~~~~~~~~~~~~~

A practical example using MicroBooNE experimental parameters:

**MicroBooNE Configuration:**

- **Flux**: MicroBooNE flux (Booster Neutrino Beam)
- **Energy**: MicroBooNE energy range (0.0 - 10.0 GeV)
- **Tune**: MicroBooNE tune (G18_10a_02_11a)
- **Target**: Liquid Argon (LAr)

**Sample Configuration File (microboone_config.xml):**


This configuration will simulate neutrino interactions in a liquid argon detector using the MicroBooNE beam specifications and physics tune.


Running the Simulation
----------------------

Basic command structure
~~~~~~~~~~~~~~~~~~~~~~

To run a GENIE simulation

.. code-block:: bash

    gevgen -n <number of events>\
           -p <probe pdg, e.g. 14 for muon neutrino> \
           -t <target pdg, e.g. 1000180400 for argon> \
           -e <minE>,<maxE> \
           --event-generator-list <list of channels, e.g. CCQE, CC, NC> \
           --tune <GENIE tune, e.g. G18_10a_02_11a> \
           --cross-sections <splines of total cross section> \
           -f <flux file>,<flux histogram> \
           -o <name of output *.ghep.root file> 

* ``--event-generator-list`` options should be the name of a collection of channels, it is configured in ``$GENIE/config/EventGeneratorListAssembler.xml``. The common used should be ::

    Default
    CCQE
    CC
    NC
    NCEL
    CCMEC
    NCMEC
    DIS
    RES
    ......

* ``--tune`` tune name can be find in ``$GENIE/config/``, the meaning of tune name is ::
    
     GENIE tune: Standard GENIE tune: G18_10a_02_11a
     - Prefix ............... : G
     - Year ................. : 18
     - Major model ID ....... : 10
     - Minor model ID ....... : a
     - Tuned param set ID ... : 02
     - Fit dataset ID ....... : 11a
  
* ``--cross-sections``, you can generate your own splines by using ``gmkspl``. We provides official splines by scisoft in `GENIE splines <http://scisoft.fnal.gov/scisoft/packages/genie_xsec/>`_ and you can download the spline for `G18_10a_02_11a <https://scisoft.fnal.gov/scisoft/packages/genie_xsec/v3_06_00/genie_xsec-3.06.00-noarch-G1810a0211a-k250-e1000.tar.bz2>`_

* ``-f`` point to a flux file. MicroBooNE flux is a histogram saved in a root file, you can use it like ``-f /<path_to_flux>/MCC9_FluxHist_volTPCActive.root,hEnumu_cv``

Example
~~~~~~~

A example to run GENIE simulation with MicroBooNE tune.

.. code-block:: console

    $ cd <your workarea for GENIE simulation>
    $ wget https://scisoft.fnal.gov/scisoft/packages/genie_xsec/v3_06_00/genie_xsec-3.06.00-noarch-G1810a0211a-k250-e1000.tar.bz2
    $ tar -xvf tar -xvf genie_xsec-3.06.00-noarch-G1810a0211a-k250-e1000.tar.bz2
    $ cp -r /exp/sbnd/data/users/liangliu/GENIE/test_zexp/flux .

After that, you can save the following bash script into ``run_genie.sh`` and run it.

.. code-block:: console

    $ bash run_genie.sh

The ``run_genie.sh`` script.

.. code-block:: bash

    #!/bin/bash

    outdir="G18_10a_02_11a"

    mkdir -p ${outdir}

    interaction="CCMEC"

    events="50"
    version="v1"

    tune="G18_10a_02_11a"

    probe="14"
    target="1000180400"
    minE="0."
    maxE="10."

    fluxfile="flux/MCC9_FluxHist_volTPCActive.root"
    fluxhisto="hEnumu_cv"

    spline="genie_xsec/v3_06_00/NULL/G1810a0211a-k250-e1000/data/gxspl-NUsmall.xml"

    # Generate GENIE events
    echo "gevgen -n $events -p ${probe} -t ${target} -e ${minE},${maxE}  --event-generator-list ${interaction} --tune ${tune} --cross-sections ${spline} -f ${fluxfile},${fluxhisto} -o ${outdir}/${probe}_${target}_${interaction}_${version}_${tune}.ghep.root"
    gevgen -n $events -p ${probe} -t ${target} -e ${minE},${maxE} --event-generator-list ${interaction} --tune ${tune} --cross-sections ${spline} -f ${fluxfile},${fluxhisto} -o ${outdir}/${probe}_${target}_${interaction}_${version}_${tune}.ghep.root --seed 1232


    # Convert file from ghep to gst
    echo "gntpc -f gst -i ${outdir}/${probe}_${target}_${interaction}_${version}_${tune}.ghep.root -o ${outdir}/${probe}_${target}_${interaction}_${version}_${tune}.gst.root --tune ${tune}"
    gntpc -f gst -i ${outdir}/${probe}_${target}_${interaction}_${version}_${tune}.ghep.root -o ${outdir}/${probe}_${target}_${interaction}_${version}_${tune}.gst.root --tune ${tune}

    # Remove all unnecessary files
    ##rm *.status
    rm input-flux.root


GENIE make spline
-----------------

adsasd

Viewing Results
---------------
