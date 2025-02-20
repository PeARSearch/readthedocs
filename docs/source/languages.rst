=====================================================
Tutorial 2: Training a PeARS model for your language
=====================================================

At the time of writing (Feb. 2025), PeARS supports four languages: English, German, French and Malayalam. If you want to train a model for your own language, all you need is to ensure there is a Wikipedia snapshot for that language, and run a few commands from the `pearsnlp repository <https://github.com/possible-worlds-research/pearsnlp>`_, maintained by our friends at `Possible Worlds Research <https://github.com/possible-worlds-research>`_.

-------------------------
Step 1 - Install PeARSNLP
-------------------------

The installation instructions can be found `on GitHub <https://github.com/possible-worlds-research/pearsnlp>`_. We replicate them here below for convenience.

We recommend installing this package in a virtual environment. If you do not have virtualenv installed, you can get it in the following way:

.. code-block:: bash
  
    sudo apt update
    sudo apt install python3-setuptools
    sudo apt install python3-pip   
    sudo apt install python3-virtualenv

Then, clone PeARSNLP, make your virtual environment and install the necessary dependency (the Wikipedia-processing package `WikiNLP <https://github.com/possible-worlds-research/wikinlp>`_):

.. code-block:: bash
  
    git clone https://github.com/possible-worlds-research/pearsnlp.git
    cd pearsnlp
    virtualenv env && source env/bin/activate
    pip install git+https://github.com/possible-worlds-research/wikinlp.git

You're done!


-------------------------
Step 2 - Train a model
-------------------------

Training a model for your language simply consists in running the *modelbuilder.py* script. For instance, if you want to train a model for Faroese (language code 'fo'), you would run the following command:


.. code-block:: bash

    python3 modelbuilder.py fo


At the end of the process, you should have three files available in your local folders:

- a .vocab file in the spm/\<language-code\> folder;
- a .model file in the spm/\<language-code\> folder;
- a .cos file in the ds/\<language-code\> folder.



------------------------------------------------
Step 3 - Adding your model to your PeARS install
------------------------------------------------

PeARS needs to know that a new language has been added to your instance. To do this, you should:

- copy the three files referenced above (*.vocab*, *.model* and *.cos*) to your PeARS install, inside a newly created *app/api/models/\<language-code\>* folder (see the *app/api/models/en* for an example);

- modify your .env file in the root directory of your PeARS install and add the language code to the PEARS_LANGS variable (see :ref:`the docs <language-settings>` for more information on the .env file). We recommend ordering your language codes in order of importance. So if your instance is supposed to index both Faroese and English, but you expect more Faroese content, the relevant line in your .env should read

.. code-block:: bash

    PEARS_LANGS=fo,en




