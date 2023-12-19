.. _complex_apphooks_how_to:

How to manage complex apphook configuration
===========================================

In :ref:`apphooks_how_to` we discuss some basic points of using apphooks. In this
document we will cover some more complex implementation possibilities.

.. _multi_apphook:

Attaching an application multiple times
---------------------------------------

Define a namespace at class-level
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to attach an application multiple times to different pages, then the class
defining the apphook *must* have an ``app_name`` attribute:

.. code-block::

    class MyApphook(CMSApp):
        name = _("My Apphook")
        app_name = "myapp"

        def get_urls(self, page=None, language=None, **kwargs):
            return ["myapp.urls"]

The ``app_name`` does three key things:

- It provides the *fallback namespace* for views and templates that reverse URLs.
- It exposes the *Application instance name* field in the page admin when applying an
  apphook.
- It sets the *default apphook instance name* (which you'll see in the *Application
  instance name* field).

We'll explain these with an example. Let's suppose that your application's views or
templates use ``reverse('myapp:index')`` or ``{% url 'myapp:index' %}``.

In this case the namespace of any apphooks you apply must match ``myapp``. If they
don't, your pages using them will throw up a ``NoReverseMatch`` error.

You can set the namespace for the instance of the apphook in the *Application instance
name* field. However, you'll need to set that to something *different* if an instance
with that value already exists. In this case, as long as ``app_name = "myapp"`` it
doesn't matter; even if the system doesn't find a match with the name of the instance it
will fall back to the one hard-wired into the class.

In other words setting ``app_name`` correctly guarantees that URL-reversing will work,
because it sets the fallback namespace appropriately.

Set a namespace at instance-level
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the other hand, the *Application instance name* will override the ``app_name`` *if* a
match is found.

This arrangement allows you to use multiple application instances and namespaces if that
flexibility is required, while guaranteeing a simple way to make it work when it's not.

Django's :ref:`django:topics-http-reversing-url-namespaces` documentation provides more
information on how this works, but the simplified version is:

1. First, it will try to find a match for the *Application instance name*.
2. If it fails, it will try to find a match for the ``app_name``.

.. _apphook_configurations:

Apphook configurations
----------------------

Namespacing your apphooks also makes it possible to manage additional database-stored
apphook configuration, on an instance-by-instance basis.

Basic concepts
~~~~~~~~~~~~~~

To capture the configuration that different instances of an apphook can take, a Django
model needs to be created - each apphook instance will be an instance of that model, and
administered through the Django admin in the usual way.

Once set up, an apphook configuration can be applied to to an apphook instance, in the
*Advanced settings* of the page the apphook instance belongs to:

.. image:: /how_to/images/select_apphook_configuration.png
    :alt: selecting an apphook configuration application
    :width: 400
    :align: center

The configuration is then loaded in the application's views for that namespace, and will
be used to determined how it behaves.

Creating an application configuration in fact creates an apphook instance namespace.
Once created, the namespace of a configuration cannot be changed - if a different
namespace is required, a new configuration will also need to be created.

An example apphook configuration
--------------------------------

In order to illustrate how this all works, we’ll create a new FAQ application, that
provides a simple list of questions and answers, together with an apphook class and an
apphook configuration model that allows it to exist in multiple places on the site in
multiple configurations.

We’ll assume that you have a working django CMS project running already.

Create the new FAQ application
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Let us quickly create the new app:

1. **Create a new app in your project**:

   .. code-block::

       python -m manage startapp faq

2. **Create a model for the app config in ``models.py``**: The app config will be
   identified by its namespace.

   .. code-block::

       class FaqConfig(models.Model):
           namespace = models.CharField(
               _("instance namespace),
               default=None,
               max_length=100,
               unique=True,
           )

           paginate_by = models.PositiveIntegerField(
               _("paginate size"),
               blank=False,
               default=5,
           )

3. **Create the FAQ model** also in ``models.py``: All entries will be assigned to an
   instance of the app hook.

   .. code-block::

       from django.db import models

       class Entry(models.Model):
           app_config = ForeignKey(FaqConfig, null=False)  # We need to assign an FAQ entry to its app instance
           question = models.TextField(blank=True, default='')
           answer = models.TextField()

           def __str__(self):
               return self.question or "<Empty question>"

           class Meta:
               verbose_name_plural = 'entries'
