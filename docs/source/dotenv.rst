==============
The PeARS .env
==============

If you are a PeARS admin setting up an instance, you will have to go through the important step of configuring the .env file of PeARS to fit your purposes. This page explains the different environment variables available in PeARS, and what you should do with them.


.. _language-settings:

-----------------
Language settings
-----------------

**PEARS_LANGS** contains the languages you want to be available on your instance. This variable should be in the form of language codes, separated by commas. For instance, just *en* for English-only, or *en,de* for English and German. Make sure you have installed the models for the languages you want to use. At this time, PeARS supports English, German, French and Malayalam.

**TRANSLATION_DIR** should contain the full path to your translation directory. By default, it is simply *translations* in the top directory of your installation.

-------------
Mail Settings
-------------

If you are using your PeARS instance locally, you may leave these variables empty. But if you are running a public instance, you will want ways to email new users, help them perform basic actions like resetting passwords, and perhaps also send yourself important information about your instance (like new sign-ups). To do this, you need to associate a mail account with your instance. That account should support SMTP. Most of the information below will have been provided by your mail provider.

**MAIL_DEFAULT_SENDER** is your email address.

**EMAIL_USER** is probably the same email address as previously, unless you have a specific username that you use for your mail server.

**MAIL_SERVER** your mail server.

**MAIL_PORT** the port of your SMTP mail server.

**EMAIL_PASSWORD** the password associated with your mail account.

-------
Secrets
-------

The following variables are used for security purposes. You should set them all to some long random string of your choice.

**SECRET_KEY** is some long string.

**SECURITY_PASSWORD_SALT** is some other long string.

**CSRF_SESSION_KEY** is yet another long string.

---------------
Docker settings
---------------

You only need to set these variables if you are using a Docker installation.

**PODS_DIR** is the full path to your pod directory in the *data* folder.

**CAPTCHA_DIR** is the full path to your captcha directory in the *data* folder.

-----------------------
PythonAnywhere settings
-----------------------

You only need to set this variable if you are using PythonAnywhere to host your instance.

**PA_USERNAME** is your PythonAnywhere username.

------
Server
------

**APP_PORT** is the port you want to use to run your PeARS instance. It is set at 8080 by default.

**FLASK_ENV** should be set to "production" when running the app in production, otherwise to "development".

---------------------
User-related settings
---------------------

These settings control what your visitors can do with your site.

**NEW_USERS_ALLOWED** allows users to create accounts on your instance when set to 'true'. When 'false', the sign-up button is deactivated.

**FEEDBACK_FORM** allows you to display a link to a feedback form on your instance. It is set to 'false' by default.

-----------------------
Search results settings
-----------------------

The default snippet length in PeARS is fixed at 10 words to respect the EU legal framework. TL;DR there are legal restrictions on how much of a copyrighted text you can copy and redistribute. In some jurisdictions, this applies to snippets in search results. We have tried to be conservative in the default PeARS settings. If you are in a more lenient jurisdiction, or if your instance only contains open data (e.g. Creative Commons content), you may want to set the length of snippets **at your own risk**. 

**SNIPPET_LENGTH** is set to 10 by default.

---------------------
Optimisation settings
---------------------

The optimisation settings control the search efficiency of your instance by turning on/off some features.

**LIVE_MATRIX** controls whether your index is dynamically updated, i.e. when you index new pages, are they instantly searchable or do you require an instance reboot? For very young instances with only a few hundreds of pages, or on serious hardware, feel free to leave this as 'true'. As soon as you experience some sluggishness in search, turn it off to 'false' and start indexing in batches, briefly rebooting your instance when the new batch is in.

**EXTEND_QUERY** controls query expansion. Query expansion is what lets you find kittens when you searched for cats. The search system expands your query with related terms and returns pages that contain those terms, as well as the documents that contain your original request. Query expansion makes the search process a little longer, so turning it off by setting it to 'false' helps performance.

---------
About you
---------

The following variables contain information that will be used to personalise your instance.

**OWN_BRAND** should be set to 'true' if you want to use your own logo for your instance. 

**LOGO_PATH** is the path to your own logo, to be set if OWN_BRAND is true. By default, your logo (ideally of dimension 480x250px) is stored in the *static/assets* folder if you have installed from source, or in your *data* folder if you are in a docker installation. You should have two versions of the image, one for day mode and for night mode, and they should be named *logo.png* and *logo-dark.png* respectively.

**SITENAME** contains the domain name of your site.

**SITE_TOPIC** should be a brief description of the focus of your instance. It will appear in the FAQ page.

**SEARCH_PLACEHOLDER** is a placeholder with some examples of what people can search for, which appears in the main search bar of the instance. It helps your visitors understand what kind of content can be found on your instance.


-----
Legal
-----

The following variables are there in case you have to satisfy any legal requirements in terms of showing the owner of your instance. They should all be fairly explanatory and will be used to populate the T&C, Impressum and Privacy policy pages of the site. 

**ORG_NAME** is your organisation's name.

**ORG_ADDRESS** is your organisation's address, on one line.

**ORG_EMAIL** is your organisation's email.

**APPLICABLE_LAW** is your jurisdiction (usually, your location).

**TAX_OFFICE**, if applicable, if your tax office.

**REGISTRATION_NUMBER** is your registration ID for organisations or companies, if applicable.

**VAT_NUMBER** is your VAT number, if applicable.

**EU_SPECIFIC** should be set to true if the server is located in the EU, false otherwise. It displays extra information about the European Commission online dispute resolution platform on the impressum page.

**SERVERS** contains the organisation providing the servers on which the instance is hosted, if any.
