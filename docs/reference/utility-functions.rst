.. _utility_functions:

#################
Utility functions
#################

Utility functions provide functionality that is regularly used within the django CMS core and are also available to
third party packages.

************
Placeholders
************

.. module:: cms.utils.placeholder

.. autofunction:: get_placeholder_from_slot

.. autofunction:: get_declared_placeholders_for_obj


*******
Plugins
*******

.. module:: cms.utils.plugins

.. autofunction:: assign_plugins

.. autofunction:: has_reached_plugin_limit

.. autofunction:: get_plugin_class

.. autofunction:: get_plugin_model

.. autofunction:: get_plugins_as_layered_tree

.. autofunction:: copy_plugins_to_placeholder

