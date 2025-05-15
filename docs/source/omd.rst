================================================================
Tutorial 3: Use PeARS with the On My Disk private cloud solution
================================================================

**What:** *PeARS-OMD* is a version of PeARS used in the context of the project *On My Disk: search integration*. A description of the project can be found `on this page <https://www.ngisearch.eu/view/Events/FirstTenSearchersAnnounced>`_. We are grateful to the Next Generation Internet programme of the European Commission for the financial support given to this project (see credits at the bottom of this README).

This PeARS version is tailored for use with the `On My Disk <https://onmydisk.com/>`_ private cloud solution. It includes features for indexing and search over a user's decentralised filesystem. It also provides search over the websites decentrally hosted on the On My Disk network.

You can only use PeARS-OMD if you have an On My Disk account. If you do not yet have an account, follow the instructions `here <https://onmydisk.net/shared/AwIaXOi00OjYvsr+itu52Ojb/Jq8vYXg1ufCqtiL74qz0KbF7bPCt969xOeDhvSArtqwlA#linux>`_.

----------------------
Installation and setup
----------------------

PeARS-OMD is meant to be run privately on a local machine. The next steps explain how to run the system on a local port and connect it to an On My Disk account.

We will assume that you have an On My Disk client installed on your machine and accessible at *localhost*. Again, if you do not have an On My Disk account / client, follow the instructions `here <https://onmydisk.net/shared/AwIaXOi00OjYvsr+itu52Ojb/Jq8vYXg1ufCqtiL74qz0KbF7bPCt969xOeDhvSArtqwlA#linux>`_.

You will have to set up the On My Disk client for use with PeARS, by navigating to your settings and to the device tab. Please tick 'Use local PeARS server' and enter a string of your choice as authentification token.


1. Clone the repo on your machine
==================================

.. code-block:: bash
    
    git clone https://github.com/PeARSearch/PeARS-OMD.git


2. (Optional step) Setup a virtualenv in your directory
=======================================================

If you haven't yet set up virtualenv on your machine, please install it via pip:

.. code-block:: bash

    sudo apt-get update
    sudo apt-get install python3-setuptools
    sudo apt-get install python3-pip
    sudo apt install python3-virtualenv

Then change into the PeARS-OMD directory:

.. code-block:: bash

    cd PeARS-OMD

Then run:

.. code-block:: bash

    virtualenv env && source env/bin/activate


3. Install the build dependencies
=================================

From the PeARS-OMD directory, run:

.. code-block:: bash

    pip install -r requirements.txt


4. Set up authentification
==========================

Copy the *conf/pears.ini.template* file to your own *conf/pears.ini*. This file will contain your secret tokens for authentification and security.

.. code-block:: bash

    cd conf
    cp pears.ini.template pears.ini

You should change the following lines:

.. code-block:: bash

    # Secrets
    AUTH_TOKEN=<your token, identical to the one in the OMD client>
    SESSION_COOKIE_NAME=<some session name>
    CSRF_SESSION_KEY=<some long string>
    SECRET_KEY=<some long string>

The last three lines can be set to any long string of your choice. The first line, AUTH_TOKEN, should be set to the string that you chose in your On My Disk client, under 'Settings --> Devices --> Use local PeARS server'.


5. (Optional) Choose your languages
============================================

The list of available languages is also set in *conf/pears.ini*. Currently, the languages that are available out-of-the-box are English, French, Russian, and Slovenian. If you don't need all those languages, you can change the string "en,fr,ru,sl" to include only the language codes you require. Please note that the order of the language matters for certain things: it plays a role in the ordering of search results, and the first language in the list is used as a fallback if a document's language is not recognised as one of the installed languages.



6. Run your pear!
=================

In the root of the repo, run:

.. code-block:: bash

    python3 run.py

You should now see the login page of PeARS at http://localhost:9090/; use your On My Disk credentials to sign in.


