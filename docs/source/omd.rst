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

.. image:: /static/set-pears-token.png
   :width: 800px
   :alt: Setting the PeARS token in the On My Disk client.



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


----------------------
Indexing and searching
----------------------

Your PeARS is set up to index your private files, as well as the public websites hosted on the On My Disk network. These two functions can be accessed under the tabs 'private' and 'websites' respectively.

1. Private indexing
===================

The first thing you will want to do on your private indexer page is retrieve your account information from the On My Disk gateway. To do this, simply click on 'Update your database'. It will populate the page with your personal information.

.. image:: /static/omd-private-indexing.png
   :width: 800px
   :alt: Private indexing page on the PeARS client, showing the 'update your database' button.


Under 'Subscriptions: locations', you will find the physical devices you have registered on the OnMyDisk network, as well as any folder shared with you by other users. By default, each location is unticked, meaning that PeARS will not index it. If you want to index a specific location, you can do so by ticking the relevant checkbox and pressing the button 'Update subscriptions'.

Under 'Subscriptions: groups', you will find a list of all the groups you belong to. By default, your PeARS install will index all groups for the locations you are subscribed to. If you wish to exclude a group from indexing, simply untick it here and click 'Update subscriptions'.

Whenever you make a change to your subscriptions, PeARS will update your current index: it will retrieve your documents from all relevant physical locations and build a searchable index for them, or possibly delete parts of the index that you decided to unsubscribe from. If you ever need to force reindexing of your files, you can use the 'Perform manual indexing' button towards the top of the page.

2. Website indexing
===================

On My Disk is not just a private cloud service, but also a private website hosting service. People hosting their website on the On My Disk network can have their public content directly searchable from local PeARS nodes. In order to know which websites are available on the network, head over to the Websites tab and click on 'Browse OMD websites':

.. image:: /static/browse-omd-websites.png
   :width: 800px
   :alt: Website tab of the PeARS client, showing the 'Browse OMD websites' button.


You will be presented with the current list of all sites on the network:

.. image:: /static/omd-website-list.png
   :width: 800px
   :alt: On My Disk website list


To make a site searchable on your local PeARS client, simply click on the little cloud icon. This will add the site to your subscribed content.

The list of sites you are subscribed to is visible from the 'Websites' tab. Whenever you want to unsubscribe from a site, you can untick the relevant checkbox and click on 'Update subscriptions.

.. image:: /static/omd-subscribed-websites-list.png
   :width: 800px
   :alt: Website tab of the PeARS client, showing the list of currently subscribed websites.


As for private indexing, clicking on 'Perform manual indexing' will trigger indexing for all your locations, including subscribed websites. Otherwise, website indexing will happen automatically once a day, as long as your PeARS client is online.


3. Searching
============

Once indexing is complete, you can search. Results will look like this, and include both websites and private files:

.. image:: /static/omd-search-results.png
   :width: 800px
   :alt: Screenshot of the search results page


The system will search both your files' metadata as well as their full text, if applicable: the contents from plain text files will be indexed directly; the contents of certain supported file types (`pdf`, `odt`, `docx`, `xlsx`, and `pptx`) will be automatically converted and made searchable.

4. Cleaning your environment
============================

Whenever you want to come back to a clean install, manually delete your database and pods:

.. code-block:: bash

    rm -f app/db/app.db
    rm -fr app/pods/*


-----------------------------------------------------
Creating your own website and making it searchable
-----------------------------------------------------

We will first go to our On My Disk client and click on 'New website':

.. image:: /static/new-website-link.png
   :width: 400px
   :alt: Screenshot of On My Disk client, showing the 'New website' link


Follow the instructions to create your website, entering a name, title and description. (As 'type' can use a Jekyll site for your first attempt.)

.. image:: /static/new-omd-website.png
   :width: 400px
   :alt: Screenshot of the dialogue box to create a new On My Disk website

Click on 'Next', select a theme, and click on 'Create'. Template files will be automatically created in your On My Disk account for your new site. You can visit your template site by clicking on the world icon in the top right hand corner of the screen.

If you now go back to your PeARS interface, you should find your new site when refreshing your website list. Now, anybody with a PeARS client can index it.

-------
Credits
-------

.. image:: /static/NGI.png
   :width: 400px
   :alt: NGI Logo

Funded by the European Union. Views and opinions expressed are however those of the author(s) only and do not necessarily reflect those of the European Union or European Commission. Neither the European Union nor the granting authority can be held responsible for them. Funded within the framework of the NGI Search project under grant agreement No101069364.

