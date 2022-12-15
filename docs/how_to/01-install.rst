.. _installation:

#################################
How to install django CMS by hand
#################################

..  note::

    This guide assumes that you are starting with a blank project, so you will need to adapt the steps below appropriately as required. This guide also assumes you have some basic familiarity with Python and Django.

******************************
Install the django CMS package
******************************

Check the :ref:`Python/Django requirements <requirements>` for this version of django CMS.

django CMS also has other requirements, which it lists as dependencies in its ``setup.py``.

..  important::

    We strongly recommend doing all of the following steps in a virtual environment. You ought to know how to create,
    activate and dispose of virtual environments using `virtualenv <https://virtualenv.pypa.io>`_. If you don't, you
    can use the steps below to get started, but you are advised to take a few minutes to learn the basics of using
    virtualenv before proceeding further.

    ..  code-block:: bash

        virtualenv django-cms-site  # create a virtualenv
        source django-cms-site/bin/activate  # activate it

Once you are on an activated virtualenv, make sure ``pip`` is up-to-date by running::

	pip install --upgrade pip

Now, using ``pip`` install the latest stable version of django CMS ::

    pip install git+https://github.com/django-cms/django-cms@develop-4



****************************************
Create a new project
****************************************

To create a new project run::

    django-admin startproject myproject

Your new project will look like this::

    myproject
        myproject
            asgi.py
            __init__.py
            settings.py
            urls.py
            wsgi.py
        manage.py


********************************************
Minimally-required applications and settings
********************************************

Add the following applications and settings to the ``settings.py`` file in your project.

Intention to use django CMS version 4
=====================================

Add the following setting at the end of ``settings.py`` to confirm your intention to use django CMS version 4::

   CMS_CONFIRM_VERSION4 = True


Installed apps
==============

django CMS needs to use Django's :mod:`django:django.contrib.sites` framework. It also uses the core django CMS modules
``cms`` and ``menus``. Finally, `django-treebeard <http://django-treebeard.readthedocs.io>`_
is used to manage django CMS's page tree structures. Plugin trees are managed independently since version 4 of django CMS.

Add :mod:`django:django.contrib.sites`, ``cms``, ``menus``, and ``treebeard`` at the end of the the list of ``INSTALLED_APPS``::

    'django.contrib.sites',
    'cms',
    'menus',
    'treebeard',

Set a ``SITE_ID`` after the list of installed apps::

   SITE_ID = 1

django CMS installs `django CMS admin style <https://github.com/django-cms/djangocms-admin-style>`_.
This is an optional setting that provides some styling that helps make django CMS administration components easier to work with.
Its installation is strongly recommended.

To install django CMS admin style add the following in the ``INSTALLED_APPS`` and **before** ``django.contrib.admin``::

    'djangocms_admin_style',


Language settings
=================

List the :setting:`django:LANGUAGES` your project will serve. Also, add the :setting:`django:LANGUAGE_CODE` for each language.

For example::

    LANGUAGES = [
        ('en', 'English'),
        ('de', 'German'),
    ]

(For simplicity's sake, at this stage it is worth changing the default ``en-us`` in that you'll find in the
``LANGUAGE_CODE`` setting to ``en``.)


********
Database
********

django CMS requires a relational database backend. Each django CMS installation should have its own database.

You can use SQLite, which is included in Python and doesn't need to be installed separately or configured further. You
are unlikely to be using that for a project in production, but it's ideal for development and exploration, especially
as it is configured by default in a new Django project's :setting:`django:DATABASES`.

..  note::

    For deployment, you'll need to use a :doc:`production-ready database with Django <django:ref/databases>`. We
    recommend using `PostgreSQL`_ or `MySQL`_.

    Installing and maintaining database systems is far beyond the scope of this documentation, but is very well
    documented on the systems' respective websites.

    .. _PostgreSQL: http://www.postgresql.org/
    .. _MySQL: http://www.mysql.com

    Whichever database you use, it will also require the appropriate Python adaptor to be installed::

        pip install psycopg2     # for Postgres
        pip install mysqlclient  # for MySQL

    Refer to :setting:`Django's DATABASES setting documentation <django:DATABASES>` for the appropriate configuration
    for your chosen database backend.


Database tables
===============

Now run migrations to create database tables for the new applications::

    python manage.py migrate


Admin user
==========

Create an admin superuser::

    python manage.py createsuperuser


*************************************
Using ``cms check`` for configuration
*************************************

Once you have completed the minimum required set-up described above, you can use django CMS's built-in ``cms check``
command to help you identify and install other components. Run::

    python manage.py cms check

This will check your configuration, your applications and your database, and report on any problems.

..  note::

    If key components are be missing, django CMS will be unable to run the ``cms check command`` and will simply raise
    an error instead.

After each of the steps below run ``cms check`` to verify that you have resolved that item in its checklist.


Sekizai
=======

`Django Sekizai <https://github.com/ojii/django-sekizai>`_ is required by the CMS for static files management. Add the following at the end of the list of ``INSTALLED_APPS``::

     'sekizai'

Also add ``'sekizai.context_processors.sekizai'`` to the ``TEMPLATES['OPTIONS']['context_processors']``:

..  code-block:: python
    :emphasize-lines: 7

    TEMPLATES = [
        {
            ...
            'OPTIONS': {
                'context_processors': [
                    ...
                    'sekizai.context_processors.sekizai',
                ],
            },
        },
    ]


Middleware
==========

In the :setting:`django:MIDDLEWARE` list add the following::

    'django.middleware.locale.LocaleMiddleware',
    'cms.middleware.user.CurrentUserMiddleware',
    'cms.middleware.page.CurrentPageMiddleware',
    'cms.middleware.toolbar.ToolbarMiddleware',
    'cms.middleware.language.LanguageCookieMiddleware',


You can also add ``'cms.middleware.utils.ApphookReloadMiddleware'``. It's not absolutely necessary, but it's
:ref:`useful <reloading_apphooks>`. If included, should be at the start of the list.

Add the following configuration at the end of ``settings.py``::

    X_FRAME_OPTIONS = 'SAMEORIGIN'

Context processors
==================

Add ``'cms.context_processors.cms_settings'`` to ``TEMPLATES['OPTIONS']['context_processors']``.

Also add ``'django.template.context_processors.i18n'`` if it's not already present in ``TEMPLATES['OPTIONS']['context_processors']``.

``cms check`` should now be unable to identify any further issues with your project. Some additional configuration is
required however.


******************************
Further required configuration
******************************

URLs
====

In the project's ``urls.py``, add ``url(r'^', include('cms.urls'))`` to the ``urlpatterns`` list. It should come after
other patterns, so that specific URLs for other applications can be detected first. Note: when using Django 2.0 or
later the syntax is ``re_path(r'^', include('cms.urls'))``

You'll also need to have an import for ``django.urls.include``. For example:

..  code-block:: python
    :emphasize-lines: 1,5

    from django.urls import re_path, include

    urlpatterns = [
        re_path(r'^admin/', admin.site.urls),
        re_path(r'^', include('cms.urls')),
    ]

The django CMS project will now run, as you'll see if you launch it with ``python manage.py runserver``. You'll be able
to reach it at http://localhost:8000/, and the admin at http://localhost:8000/admin/. You won't yet actually be able to
do anything very useful with it though.


.. _basic_template:

Templates
=========

django CMS requires at least one template for its pages, so you'll need to add :setting:`CMS_TEMPLATES` to your
settings. The first template in the :setting:`CMS_TEMPLATES` list will be the project's default template.

::

    CMS_TEMPLATES = [
        ('home.html', 'Home page template'),
    ]

In the root of the project, create a ``templates`` directory, and in that, ``home.html``, a minimal django CMS
template:


..  code-block:: html+django

    {% load cms_tags sekizai_tags %}
    <html>
        <head>
            <title>{% page_attribute "page_title" %}</title>
            {% render_block "css" %}
        </head>
        <body>
            {% cms_toolbar %}
            {% placeholder "content" %}
            {% render_block "js" %}
        </body>
    </html>

This is worth explaining in a little detail:

* ``{% load cms_tags sekizai_tags %}`` loads the template tag libraries we use in the template.
* ``{% page_attribute "page_title" %}`` extracts the page's ``page_title`` :ttag:`attribute <page_attribute>`.
* ``{% render_block "css" %}`` and ``{% render_block "js" %}`` are Sekizai template tags that load blocks of HTML
  defined by Django applications. django CMS defines blocks for CSS and JavaScript, and requires these two tags. We
  recommended placing ``{% render_block "css" %}`` just before the ``</head>`` tag, and and ``{% render_block "js" %}``
  tag just before the ``</body>``.
* ``{% cms_toolbar %}`` renders the :ttag:`django CMS toolbar <cms_toolbar>`.
* ``{% placeholder "content" %}`` defines a :ttag:`placeholder`, where plugins can be inserted. A template needs at
  least one ``{% placeholder %}`` template tag to be useful for django CMS. The name of the placeholder is simply a
  descriptive one, for your reference.

Django needs to be know where to look for its templates, so add ``templates`` to the ``TEMPLATES['DIRS']`` list:

..  code-block:: python
    :emphasize-lines: 4

    TEMPLATES = [
        {
            ...
            'DIRS': ['templates'],
            ...
        },
    ]

..  note::

    The way we have set up the template here is just for illustration. In a real project, we'd recommend creating a
    ``base.html`` template, shared by all the applications in the project, that your django CMS templates can extend.

    See Django's :ref:`template language documentation <django:template-inheritance>` for more on how template
    inheritance works.


Media and static file handling
==============================

A django CMS site will need to handle:

* *static files*, that are a core part of an application or project, such as its necessary images, CSS or
  JavaScript
* *media files*, that are uploaded by the site's users or applications.

:setting:`django:STATIC_URL` is defined (as ``"/static/"``) in a new project's settings by default.
:setting:`django:STATIC_ROOT`, the location that static files will be copied to and served from, is not required for
development - :doc:`only for production <django:howto/deployment/checklist>`.

For now, using the runserver and with ``DEBUG = True`` in your settings, you don't need to worry about either of these.

However, :setting:`django:MEDIA_URL` (where media files will be served) and :setting:`django:MEDIA_ROOT` (where they
will be stored) need to be added to your settings::

    MEDIA_URL = "/media/"
    MEDIA_ROOT = os.path.join(BASE_DIR, "media")

For deployment, you need to configure suitable media file serving. **For development purposes only**, the following will
work in your ``urls.py``:

..  code-block:: python
    :emphasize-lines: 1,2,6

    from django.conf import settings
    from django.conf.urls.static import static

    urlpatterns = [
        ...
    ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

(See the Django documentation for guidance on :doc:`serving media files in production
<django:howto/static-files/index>`.)

***************************************
Adding version management functionality
***************************************

By default all page contents if automatically public since django CMS version 4. If you wish to add support for
draft versions and publishing you will need to add ``djangocms-versioning`` to your project.

    pip install git+https://github.com/django-cms/djangocms-versioning@master

Add::

    'djangocms_versioning',

to ``INSTALLED_APPS``.


*************************************
Adding content-handling functionality
*************************************

You now have the basics set up for a django CMS site, which is able to manage and serve up pages. However the project
so far has no plugins installed, which means it has no way of handling content in those pages. All content in django
CMS is managed via plugins. So, we now need to install some additional addon applications to provide plugins and other
functionality.

You don't actually **need** to install any of these. django CMS doesn't commit you to any particular applications for
content handling. The ones listed here however provide key functionality and are strongly recommended.

Django Filer
============

`Django Filer`_ provides file and image management. Many other applications also rely on Django Filer - it's very
unusual to have a django CMS site that does *not* run Django Filer. The configuration in this section will get you
started, but you should refer to the `Django Filer documentation <https://django-filer.readthedocs.io>`_ for more
comprehensive configuration information.

.. _Django Filer: https://github.com/divio/django-filer

To install::

    pip install django-filer

A number of applications will be installed as dependencies. `Easy Thumbnails
<https://github.com/SmileyChris/easy-thumbnails>`_ is required to create new versions of images in different sizes;
`Django MPTT <https://github.com/django-mptt/django-mptt/>`_ manages the tree structure of the folders in Django Filer.

Pillow, the Python imaging library, will be installed. `Pillow <https://github.com/python-pillow/Pillow>`_ needs some
system-level libraries - the `Pillow documentation <https://pillow.readthedocs.io>`_ describes in detail what is
required to get this running on various operating systems.

Add::

    'filer',
    'easy_thumbnails',
    'mptt',

to ``INSTALLED_APPS``.

You also need to add::

    THUMBNAIL_HIGH_RESOLUTION = True

    THUMBNAIL_PROCESSORS = (
        'easy_thumbnails.processors.colorspace',
        'easy_thumbnails.processors.autocrop',
        'filer.thumbnail_processors.scale_and_crop_with_subject_location',
        'easy_thumbnails.processors.filters'
    )

New database tables will need to be created for Django Filer and Easy Thumbnails, so run migrations::

    python manage.py migrate filer
    python manage.py migrate easy_thumbnails

(or simply, ``python manage.py migrate``).


Django CMS CKEditor
===================

`Django CMS CKEditor`_ is the default text editor for django CMS.

.. _Django CMS CKEditor: https://github.com/django-cms/djangocms-text-ckeditor

Install: ``pip install djangocms-text-ckeditor``.

Add ``djangocms_text_ckeditor`` to your ``INSTALLED_APPS``.

Run migrations::

    python manage.py migrate djangocms_text_ckeditor


Miscellaneous plugins
=====================

There are plugins for django CMS that cover a vast range of functionality. To get started, it's useful to be able to
rely on a set of well-maintained plugins that cover some general content management needs.

* `djangocms-link <https://github.com/django-cms/djangocms-link>`_
* `djangocms-file <https://github.com/django-cms/djangocms-file>`_
* `djangocms-picture <https://github.com/django-cms/djangocms-picture>`_
* `djangocms-video <https://github.com/django-cms/djangocms-video>`_
* `djangocms-googlemap <https://github.com/django-cms/djangocms-googlemap>`_
* `djangocms-snippet <https://github.com/django-cms/djangocms-snippet>`_
* `djangocms-style <https://github.com/django-cms/djangocms-style>`_

To install::

    pip install djangocms-link djangocms-file djangocms-picture djangocms-video djangocms-googlemap djangocms-snippet
        djangocms-style

and add::

    'djangocms_link',
    'djangocms_file',
    'djangocms_picture',
    'djangocms_video',
    'djangocms_googlemap',
    'djangocms_snippet',
    'djangocms_style',

to ``INSTALLED_APPS``.

Then run migrations::

    python manage.py migrate.

These and other plugins are described in more detail in :ref:`commonly-used-plugins`. More are listed
plugins available on the `django CMS Marketplace <https://marketplace.django-cms.org/en/addons/>`_.


******************
Launch the project
******************

Start up the runserver::

    python manage.py runserver

and access the new site, which you should now be able to reach at ``http://localhost:8000``. Login if you haven't
done so already.

|it-works-cms|

.. |it-works-cms| image:: ../images/it-works-cms.png

**********
Next steps
**********

If this is your first django CMS project, read through the :ref:`user-tutorial` for a walk-through of some basics.

The :ref:`tutorials for developers <tutorials>` will help you understand how to approach django CMS as a developer.
Note that the tutorials assume you have installed the CMS using the django CMS Installer, but with a little
adaptation you'll be able to use it as a basis.

To deploy your django CMS project on a production web server, please refer to the :doc:`Django deployment documentation
<django:howto/deployment/index>`.
