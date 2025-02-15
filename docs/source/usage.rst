==============
Installation
==============

*PeARS Federated* is a version of PeARS for federated use. Admins create PeARS instances that users can join to contribute to the index.

*PeARS Federated* is provided as-is. Before you use it, please check the rules of your country on crawling Web content and displaying snippets. And be a good netizen: do not overload people's servers while indexing!

PeARS can be installed locally from :ref:`source <localinstall>` for development, testing, or even to manage one's own personal search service. For public use (i.e. to share your search service with the world), we recommend the :ref:`docker install <dockerinstall>`. For those who would like to share their search index but are not fully comfortable with running their own server, we also have bespoke installation instructions for the PythonAnywhere cloud service, which provides an easy-to-user, visual interface to set up one's own Web app.

.. _localinstall:

-------------------
Install from source
-------------------

The following is meant to help you test PeARS on localhost, on your machine. At the point where you are ready to deploy, please check our wiki for more instructions.


1. Clone this repo on your machine
==================================

.. code-block:: bash
    
    git clone https://github.com/PeARSearch/PeARS-federated.git


2. (Optional step) Setup a virtualenv in your directory
=======================================================

If you haven't yet set up virtualenv on your machine, please install it via pip:

.. code-block:: bash

    sudo apt-get update
    sudo apt-get install python3-setuptools
    sudo apt-get install python3-pip
    sudo apt install python3-virtualenv

Then change into the PeARS-orchard directory:

.. code-block:: bash

    cd PeARS-federated

Then run:

.. code-block:: bash

    virtualenv env && source env/bin/activate


3. Install the build dependencies
=================================

From the PeARS-federated directory, run:

.. code-block:: bash

    pip install -r requirements.txt


4. (Optional step) Install further languages
============================================

If you want to search and index in several languages at the same time, you can add multilingual support to your English install. To do this:

.. code-block:: bash

    flask pears install-language lc

where you should replace lc with a language code of your choice. For now, we are only supporting English (en), German (de), French (fr) and Malayalam (ml) but more languages are coming!


5. Set up your .env
===================

There is a .env template file at *.env-template* in the root directory of the repository. You should copy it to *.env* and fill in the information for your setup.


6. Run your pear!
=================

While on your local machine, in the root of the repo, run:

.. code-block:: bash

    python3 run.py


Now, go to your browser at *localhost:8080*. You should see the search page for PeARS. You don't have any pages indexed yet, so go to the F.A.Q. page (link at the top of the page) and follow the short instructions to get you going!



.. _dockerinstall:

-------------------
Install from docker
-------------------

These instructions assume that you are running your own server.


1. Deploy Docker and Docker Compose
===================================

The following instructions are for Ubuntu. For other distributions, refer to the `official Docker documentation <https://docs.docker.com/engine/install/>`_.

* SSH into your server.
* Install necessary packages and Docker:

.. code-block:: bash

    sudo apt-get update
    sudo apt-get install -y ca-certificates curl gettext vim
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
      $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
      sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt-get update
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin



2. Deploy PeARS-federated
=========================

* SSH into your server
* Set the domain name and instance specific directory name

.. code-block:: bash

    export DOMAIN=pears-pod-url.com # Provide the URL on which you want to reach your pears-federated pod
    export PEARS_DIR=~/pears-pod-name-1 # replace `pears-pod-name-1` with the name of your pod for ease of identification
    export STAGE=production # replace this with `staging` if you are just testing the setup, otherwise it will create a TLS certificate for you

Download the Docker-compose file and setup base directory for your instance
============================================================================

Download the `docker-compose.yml` from the Github repository to the base of your server:
        
.. code-block:: bash

    wget https://raw.githubusercontent.com/PeARSearch/PeARS-federated/nvn/add-deploy-files/deployment/docker-compose.yaml -O template.yaml
    
Use the above variables in the docker-compose file
     
.. code-block:: bash

    envsubst < template.yaml > docker-compose.yaml
    rm -rf template.yaml
    
Create a directory to store your instance details and to store persistent data for the instance:
        
.. code-block:: bash

    mkdir -p ${PEARS_DIR}/data

Configure the environmental details for your instance
=====================================================

Download the `env-template` files from the GitHub repository:

.. code-block:: bash

    wget https://raw.githubusercontent.com/PeARSearch/PeARS-federated/nvn/add-deploy-files/deployment/.env-template -O ${PEARS_DIR}/.env
    
Update the values in the `.env` file to match your configuration (follow the instructions in the .env file to fill in the data):

.. code-block:: bash

    vim ${PEARS_DIR}/.env


Bring Up the Docker Compose
===========================

.. note::

    This command assumes that you are running this command from the directory in which the `docker-compose.yaml` file exists

.. code-block:: bash

        docker compose up -d

Point your DNS to the IP address of the server
==============================================

Make sure you create an A name record pointing from your PeARS URL to the public IP address of the server



3. (Optional) Adding more pods to the same server
=================================================

If you want to host several PeARS instances on the same server, we will have to re-use the same docker-compose file by adding new pod configurations and re-using the `https-portal` container that you will find in the `docker-compose` file to point to different instances for different domain names. Here are the step by step details for doing that:

.. note::

    We assume you have already followed the above steps and have a single pod running already at this point

Create a new directory for the new pod and download the environment variable file

.. code-block:: bash

    export PEARS_DIR_2=~/pears-pod-name-2 # replace pears-pod-name-2 with your new pod name
    mkdir -p ${PEARS_DIR_2}/data
    # You can also copy this file from your existing pod directory for ease of editing
    wget https://raw.githubusercontent.com/PeARSearch/PeARS-federated/nvn/add-deploy-files/deployment/.env-template -O ${PEARS_DIR_2}/.env
    
Change the environment details in the `.env` file:

.. code-block:: bash

    vim ${PEARS_DIR_2}/.env

Update the docker-compose to also bring up the second instance. If you open your `docker-compose.yaml` file in the server at this point, you will find something like this:

.. code-block:: bash
   
    version: '3.8'

    services:
        pears-federated:
            env_file:
            - pears-pod-name-1/.env
            image: pearsproject/pears-federated:latest
            volumes:
            - pears-pod-name-1/data/:/var/lib/pears/data

        https-portal:
            image: steveltn/https-portal:1
            environment:
            DOMAINS: 'pears-pod-url.com -> http://pears-federated:8000'
            STAGE: production
            ports:
            - "80:80"
            - "443:443"
            depends_on:
            - pears-federated
            volumes:
            - https-portal-data:/var/lib/https-portal

To add another pod, you will have to first copy the `pears-federated` container definition to a new definition in the file with appropriate names as follows:

.. code-block:: bash

    version: '3.8'

    services:
        pears-federated: # if you want you can also rename this to have a more identifiable name
            env_file:
            - pears-pod-name-1/.env
            image: pearsproject/pears-federated:latest
            volumes:
            - pears-pod-name-1/data/:/var/lib/pears/data

        pears-federated-pod-2: # !! CHANGE rename this to have a more identifiable suffix
            env_file:
            - pears-pod-name-2/.env # !! CHANGE point to your new directory pears-pod-name-2
            image: pearsproject/pears-federated:latest
            volumes:
            - pears-pod-name-2/data/:/var/lib/pears/data # !! CHANGE point to your new directory pears-pod-name-2
        ...


Update `https-portal` pod to point to the new pod as well

  .. code-block:: bash

    version: '3.8'

    services:
        pears-federated:
            env_file:
            - pears-pod-name-1/.env
            image: pearsproject/pears-federated:latest
            volumes:
            - pears-pod-name-1/data/:/var/lib/pears/data

        pears-federated-pod-2:
            env_file:
            - pears-pod-name-2/.env
            image: pearsproject/pears-federated:latest
            volumes:
            - pears-pod-name-2/data/:/var/lib/pears/data

        https-portal:
            image: steveltn/https-portal:1
            environment:
                # !! CHANGE: point the URL you want to point to your new pod to the http://<name-of-the-new-pod-in-this-file>:8000
                # You use a comma to separate the entries; this can support any number of mappings
                DOMAINS: 'pears-pod-url.com -> http://pears-federated:8000, pears-pod-2-url.com -> http://pears-federated-pod-2:8000'
                STAGE: production
            ports:
            - "80:80"
            - "443:443"
            depends_on:
            - pears-federated
            - pears-federated-pod-2 # !! CHANGE: notice that it is not depending on the new pod as well
            volumes:
            - https-portal-data:/var/lib/https-portal
    ```

Bring Up the Docker Compose

.. note:: 

    This command assumes that you are running this command from the directory in which the `docker-compose.yaml` file exists

Start the Docker Compose services:

.. code-block:: bash

    docker compose up -d

Check the new pod is running by running the command:
  
.. code-block:: bash

    docker ps

Point your DNS to the IP address of the server

Make sure you create an A name record pointing from your new PeARS URL to the public IP address of the server

If you want to add a third instance, you can follow the same steps as above but for a third entry.


4. Management
=============

Backing Up data
---------------

To avoid loss of data, regularly back up the `data` folder:

Create a backup directory:
    
.. code-block:: bash

    mkdir -p ~/pears-federated-backups

Copy the data directory to the backup directory:
    
.. code-block:: bash

    cp -r ~/pears-pod-name-1/data ~/pears-federated-backups/data_backup_$(date +%Y%m%d%H%M%S)

Regularly schedule this backup process using a cron job or other automation tools to ensure your data is safe. You can setup configurations to upload these directory to a remote cloud storage for maximum security.


