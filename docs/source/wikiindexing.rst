=================================
Tutorial 1: index Wikipedia pages
=================================

The best way to familiarise oneself with PeARS is by playing with some nice open data. So we have written a little tutorial on how to set up a PeARS instance with selected Wikipedia material. At the end of this tutorial, you will have a PeARS installation running locally on your computer, and will be able to search the Wikipedia categories that you indexed.

The instructions use `WikiNLP <https://github.com/possible-worlds-research/wikinlp>`_ to generate a bespoke Wikipedia corpus that you will be able to feed to your PeARS. We will use the Python programming language to run WikiNLP, so make sure you have it installed.

We will assume a  local PeARS install for this tutorial. Refer to the documentation if you want to install PeARS on a publicly available server.

----------------------------------
Step 1 - create a Wikipedia corpus
----------------------------------

We will first install WikiNLP. The installation instructions can be found `on GitHub <https://github.com/possible-worlds-research/wikinlp>`_. We replicate them here below for convenience.

We recommend installing this package in a virtual environment. If you do not have virtualenv installed, you can get it in the following way:

.. code-block:: bash
  
   sudo apt update
   sudo apt install python3-setuptools
   sudo apt install python3-pip   
   sudo apt install python3-virtualenv

Then, create a directory for WikiNLP, make your virtual environment and install the package:

.. code-block:: bash
  
   mkdir wikinlp
   cd wikinlp
   virtualenv env && source env/bin/activate
   pip install git+https://github.com/possible-worlds-research/wikinlp.git

You're done!


Next, we want to download some Wikipedia categories. Let's now assume that you are a book lover with an affinity for science fiction. Further, let's imagine that you would like to search for novels with particular plot elements, irrespective of author, publication history or book reception. In other words, you would like to extract the synopses only from the original pages, and search over those.

So let's make ourselves an appropriate corpus from the English Wikipedia. From our new *wikinlp* folder, we will first download Wikipedia categories. To reduce download time, we will stick to categories with at least 10 pages and at most 1000.

In your wikinlp folder, open a new file and call it *extract_scifi.py*. In that file, copy/paste the following:

.. code-block:: python

   from wikinlp.categories import CatProcessor
   lang = 'en'
   catprocessor = CatProcessor(lang)
   catprocessor.get_categories(mincount=10, maxcount=1000)

Running the above with *python extract_scifi.py* results in a file called *data/en/wiki_categories.min.10.max.1000.txt*. Feel free to go and scroll down that file. It may give you ideas for other topics you may want to index. But for now, we will just notice that a few categories simply contain the word 'Science fiction novels'. We will make a note of those and grab the relevant pages / sections.

.. code-block:: python

   categories = []
   catfile = './data/en/wiki_categories.min.10.max.1000.txt'
   with open(catfile, 'r', encoding='utf-8') as fin:
       for l in fin:
           if "science fiction novels" in l.lower():
               categories.append(l.rstrip('\n'))
   catprocessor.get_category_pages(categories)
   catprocessor.get_page_content(categories, sections=['Plot','Plot summary'])

Run your python script again. It may take a while until all documents are downloaded.

------------------------------------------------------------------
Step 2 - Update your PeARS .env to fit the topic of your instance
------------------------------------------------------------------

We will now make minimal edits to our .env file:

.. code-block:: bash

   export PEARS_LANGS=en
   export SEARCH_PLACEHOLDER="Search through sci-fi novel synopses"
   export NEW_USERS_ALLOWED=false
   export FEEDBACK_FORM=false
   export LIVE_MATRIX=false
   export SNIPPET_LENGTH=50

Next, let's create a user and make that user admin. (NB: you need to be in the root folder of your installation to run these commands, i.e. in your *PeARS-federated* directory.)

.. code-block:: bash

   flask pears create-user scifilover mysecretpassword you@youremailprovider
   flask pears setadmin scifilover

Finally, let's make sure we can run PeARS:

.. code-block:: bash

   python3 run.py

Head over to *http://localhost:8080* in your browser (replace 8080 with your chosen port if you amended the default value). You should see PeARS running

                                                                  
-----------------------------------------------------------
Step 3 - populate your PeARS instance with your Wiki corpus
-----------------------------------------------------------

You can do this in one command, giving PeARS the WikiNLP folder where you preprocessed your corpus, a filter for the categories you want to index, the language of your corpus, your username, and finally the domain of your PeARS installation (which, for a local install, will be something like *http://localhost:8080*.

There is only one little detail to bear in mind. The filter should be a string which uniquely identifies all categories that we want to process. The categories that were returned by WikiNLP include the string "Science fiction novels" (with a capital 'S') as well as "science fiction novels" (lowercase). To make sure all categories are processed, we will just pass the filter 'fiction_novels':
                                                                                                                                                                                                                                                  
.. code-block:: bash

   flask pears indexwiki ~/wikinlp/data/en/categories/ fiction_novels en scifilover http://localhost:8080

Go and have a cup of tea, and don't interrupt processing, it may take a little while.


--------------
Step 4 - Play!
--------------

Run your PeARS again:

.. code-block:: bash

   python3 run.py                                                                                                                                                                                                                                                  

You should now be able to search your indexed pages. You will notice from the snippets that only the synopses of the novels have been included in the index. You have built your very own search engine for book summaries. Have fun playing!
