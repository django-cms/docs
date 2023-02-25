.. _publishing:

##########
Publishing
##########

Without an additional package installed that supports versioning all django CMS pages are
published by default. This means they are visible to the public the moment you save them.
Accordingly, all changes you make are visible immediately.

`djangocms-versioning <https://github.com/django-cms/djangocms-versioning>`_ is a general
purpose package that manages versions within different categories, like **published**,
**draft**, **unpublished**, or **archived**. django CMS, however, is not restricted to
work with djangocms-verisoning. You can use other versioning packages or come up with
your own either from scratch or by forking djangocms-versioning.

This section gives a short introduction on how to work with djangocms-versioning. For details
please consult the
`documentation of djangocms-versioning <https://django-cms-docs.readthedocs.io/>`_.

Also, while this section focuses on pages, djangocms-versioning can lend its versioning
capabilities to other objects, too, e.g., aliases as defined in djangocms-alias.

**************
Version states
**************

Each :class:`~cms.models.pagemodel.Page` object can have many
:class:`~cms.models.contentmodels.PageContent` objects assigned actually containing the
page's content (hence the name) in a specific language. Djangocms-versioning extends this
relationship by allowing more :class:`~cms.models.contentmodels.PageContent` objects to
carry a version number and version state. The states are:

  * **draft**: This is the version which currently can be edited. Only draft versions can
    be edited and only one draft version per language is allowed. Changes made to draft
    pages are not visible to the public.
  * **published**: This is the version currently visible on the website to the public. Only
    one version per language can be public. It cannot be changed. If it needs to be changed
    a new draft is created based on a published page and the published page stays unchanged.
  * **unpublished**: This is a version which was published at one time but now is not
    visible to the public any more. There can be many unpublished versions.
  * **archived**: This is a version which has not been published and also is visible to
    the public any more. It might represent a state of the page which can be used in
    later work (by reverting it to a draft state).

Each new draft version will generate a new version number.

.. image:: /images/version-states.png
     :align: center
     :alt: Version states

When a page is published, is publicly visible even if its parent pages are not published.

********************
Code and PageContent
********************

When handling :class`~cms.models.pagemcdel.Page` in code, you'll generally only "see"
published pages:

.. code-block::

    PageContent.objects.filter(language="en")   # get all published English page contents

will only give published pages. This is to ensure that no draft or unpublished version
leaks or becomes visible to the public.

Since often draft page contents are the ones you interact with in the admin, or in
draft mode in the CMS frontend, there djangocms-versioning introduces an additional
model manager for the PageContents **which may only be used on admin sites and admin
forms**::

    PageContent.admin_manager.filter(page=my_page, language="en")

will retrieve page content objects of all versions. To get the current draft version
it is often easier to filter the ``Version`` object::

    from djangocms_versioning.constants import DRAFT
    from djangocms_versioning.models import Version

    version = Version.objects.get(content__page=my_page, content__language="en", status=DRAFT)
    draft_content = Version.content

Finally, there are instance where you want to access the "current" version of a page. This is
either the current draft version or - there is no draft - the published version. You can easily achieve this by using::

    for content in PageContent.admin_manager.filter(page=my_page).current_content():
        # iterates over the current (draft or published) version of all languages of my page
        if content.versions.first().state == DRAFT:
            # do something

.. note::

  ``corrent_content_iterator`` does not return a queryset but allows to iterate of the
  elements of the queryset that correspond to the current version.

For more details see the
`documentation of djangocms-versioning <https://djangocms-versioning.readthedocs.io>`_!
