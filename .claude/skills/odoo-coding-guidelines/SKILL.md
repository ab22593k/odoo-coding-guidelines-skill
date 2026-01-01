---
name: odoo-coding-guidelines
description: "Comprehensive Odoo coding guidelines covering module structure, XML files, Python, JavaScript, and CSS/SCSS conventions for developing Odoo applications. Use when working on Odoo module development, following Odoo coding standards, or maintaining Odoo codebases."
license: Apache License 2.0
---

# Odoo Coding Guidelines

This page introduces the Odoo Coding Guidelines. Those aim to improve the
quality of Odoo Apps code. Indeed proper code improves readability, eases
maintenance, helps debugging, lowers complexity and promotes reliability.
These guidelines should be applied to every new module and to all new development.

.. warning::

    When modifying existing files in **stable version** the original file style
    strictly supersedes any other style guidelines. In other words please never
    modify existing files in order to apply these guidelines. It avoids disrupting
    the revision history of code lines. Diff should be kept minimal.

.. warning::

    When modifying existing files in **main (development) version** apply those
    guidelines to existing code only for modified code or if most of the file is
    under revision. In other words modify existing files structure only if it is
    going under major changes. In that case first do a **move** commit then apply
    the changes related to the feature.

# Module structure

.. warning::

    For modules developed by the community, it is strongly recommended to name
    your module with a prefix like your company name.

## Directories

A module is organized in important directories. Those contain the business logic;
having a look at them should make you understand the purpose of the module.

- _data/_ : demo and data xml
- _models/_ : models definition
- _controllers/_ : contains controllers (HTTP routes)
- _views/_ : contains the views and templates
- _static/_ : contains the web assets, separated into _css/, js/, img/, lib/, ..._

Other optional directories compose the module.

- _wizard/_ : regroups the transient models (`models.TransientModel`) and their views
- _report/_ : contains the printable reports and models based on SQL views. Python objects and XML views are included in this directory
- _tests/_ : contains the Python tests

## File naming

File naming is important to quickly find information through all odoo addons.
This section explains how to name files in a standard odoo module. As an
example we use a `plant nursery <https://github.com/tivisse/odoodays-2018/tree/master/plant_nursery>`\_ application.
It holds two main models _plant.nursery_ and _plant.order_.

Concerning _models_, split the business logic by sets of models belonging to
a same main model. Each set lies in a given file named based on its main model.
If there is only one model, its name is the same as the module name. Each
inherited model should be in its own file to help understanding of impacted
models.

.. code-block:: text

    addons/plant_nursery/
    |-- models/
    |   |-- plant_nursery.py (first main model)
    |   |-- plant_order.py (another main model)
    |   |-- res_partner.py (inherited Odoo model)

Concerning _security_, three main files should be used:

- First one is the definition of access rights done in a :file:`ir.model.access.csv` file.
- User groups are defined in :file:`<module>_groups.xml`.
- Record rules are defined in :file:`<model>_security.xml`.

.. code-block:: text

    addons/plant_nursery/
    |-- security/
    |   |-- ir.model.access.csv
    |   |-- plant_nursery_groups.xml
    |   |-- plant_nursery_security.xml
    |   |-- plant_order_security.xml

Concerning _views_, backend views should be split like models and suffixed
by `_views.xml`. Backend views are list, form, kanban, activity, graph, pivot, ..
views. To ease split by model in views main menus not linked to specific actions
may be extracted into an optional `<module>_menus.xml` file. Templates (QWeb
pages used notably for portal / website display) are put in separate files named
`<model>_templates.xml`.

.. code-block:: text

    addons/plant_nursery/
    |-- views/
    |   | -- plant_nursery_menus.xml (optional definition of main menus)
    |   | -- plant_nursery_views.xml (backend views)
    |   | -- plant_nursery_templates.xml (portal templates)
    |   | -- plant_order_views.xml
    |   | -- plant_order_templates.xml
    |   | -- res_partner_views.xml

Concerning _data_, split them by purpose (demo or data) and main model. Filenames
will be the main_model name suffixed by `_demo.xml` or `_data.xml`. For instance
for an application having demo and data for its main model as well as subtypes,
activities and mail templates all related to mail module:

.. code-block:: text

    addons/plant_nursery/
    |-- data/
    |   |-- plant_nursery_data.xml
    |   |-- plant_nursery_demo.xml
    |   |-- mail_data.xml

Concerning _controllers_, generally all controllers belong to a single controller
contained in a file named `<module_name>.py`. An old convention in Odoo is to
name this file `main.py` but it is considered as outdated. If you need to inherit
an existing controller from another module do it in `<inherited_module_name>.py`.
For example adding portal controller in an application is done in `portal.py`.

.. code-block:: text

    addons/plant_nursery/
    |-- controllers/
    |   |-- plant_nursery.py
    |   |-- portal.py (inheriting portal/controllers/portal.py)
    |   |-- main.py (deprecated, replaced by plant_nursery.py)

Concerning _static files_, Javascript files follow globally the same logic as
python models. Each component should be in its own file with a meaningful name.
For instance, the activity widgets are located in `activity.js` of mail module.
Subdirectories can also be created to structure the 'package' (see web module
for more details). The same logic should be applied for the templates of JS
widgets (static XML files) and for their styles (scss files). Don't link
data (image, libraries) outside Odoo: do not use an URL to an image but copy
it in the codebase instead.

Concerning _wizards_, naming convention is the same of for python models:
`<transient>.py` and `<transient>_views.xml`. Both are put in the wizard
directory. This naming comes from old odoo applications using the wizard
keyword for transient models.

.. code-block:: text

    addons/plant_nursery/
    |-- wizard/
    |   |-- make_plant_order.py
    |   |-- make_plant_order_views.xml

Concerning _statistics reports_ done with python / SQL views and classic views
naming is the following :

.. code-block:: text

    addons/plant_nursery/
    |-- report/
    |   |-- plant_order_report.py
    |   |-- plant_order_report_views.xml

Concerning _printable reports_ which contain mainly data preparation and Qweb
templates naming is the following :

.. code-block:: text

    addons/plant_nursery/
    |-- report/
    |   |-- plant_order_reports.xml (report actions, paperformat, ...)
    |   |-- plant_order_templates.xml (xml report templates)

The complete tree of our Odoo module therefore looks like

.. code-block:: text

    addons/plant_nursery/
    |-- __init__.py
    |-- __manifest__.py
    |-- controllers/
    |   |-- __init__.py
    |   |-- plant_nursery.py
    |   |-- portal.py
    |-- data/
    |   |-- plant_nursery_data.xml
    |   |-- plant_nursery_demo.xml
    |   |-- mail_data.xml
    |-- models/
    |   |-- __init__.py
    |   |-- plant_nursery.py
    |   |-- plant_order.py
    |   |-- res_partner.py
    |-- report/
    |   |-- __init__.py
    |   |-- plant_order_report.py
    |   |-- plant_order_report_views.xml
    |   |-- plant_order_reports.xml (report actions, paperformat, ...)
    |   |-- plant_order_templates.xml (xml report templates)
    |-- security/
    |   |-- ir.model.access.csv
    |   |-- plant_nursery_groups.xml
    |   |-- plant_nursery_security.xml
    |   |-- plant_order_security.xml
    |-- static/
    |   |-- img/
    |   |   |-- my_little_kitten.png
    |   |   |-- troll.jpg
    |   |-- lib/
    |   |   |-- external_lib/
    |   |-- src/
    |   |   |-- js/
    |   |   |   |-- widget_a.js
    |   |   |   |-- widget_b.js
    |   |   |-- scss/
    |   |   |   |-- widget_a.scss
    |   |   |   |-- widget_b.scss
    |   |   |-- xml/
    |   |   |   |-- widget_a.xml
    |   |   |   |-- widget_a.xml
    |-- views/
    |   |-- plant_nursery_menus.xml
    |   |-- plant_nursery_views.xml
    |   |-- plant_nursery_templates.xml
    |   |-- plant_order_views.xml
    |   |-- plant_order_templates.xml
    |   |-- res_partner_views.xml
    |-- wizard/
    |   |--make_plant_order.py
    |   |--make_plant_order_views.xml

.. note:: File names should only contain `[a-z0-9_]` (lowercase
alphanumerics and `_`)

.. warning:: Use correct file permissions : folder 755 and file 644.

.. \_contributing/development/xml_guidelines:

# XML files

## Format

To declare a record in XML, the **record** notation (using _<record>_) is recommended:

- Place `id` attribute before `model`
- For field declaration, `name` attribute is first. Then place the
  _value_ either in the `field` tag, either in the `eval`
  attribute, and finally other attributes (widget, options, ...)
  ordered by importance.

- Try to group the record by model. In case of dependencies between
  action/menu/views, this convention may not be applicable.
- Use naming convention defined at the next point
- The tag _<data>_ is only used to set not-updatable data with `noupdate=1`.
  If there is only not-updatable data in the file, the `noupdate=1` can be
  set on the `<odoo>` tag and do not set a `<data>` tag.

.. code-block:: xml

    <record id="view_id" model="ir.ui.view">
        <field name="name">view.name</field>
        <field name="model">object_name</field>
        <field name="priority" eval="16"/>
        <field name="arch" type="xml">
            <list>
                <field name="my_field_1"/>
                <field name="my_field_2" string="My Label" widget="statusbar" statusbar_visible="draft,sent,progress,done" />
            </list>
        </field>
    </record>

Odoo supports custom tags acting as syntactic sugar:

- menuitem: use it as a shortcut to declare a `ir.ui.menu`
- template: use it to declare a QWeb View requiring only the `arch` section of the view.

These tags are preferred over the _record_ notation.

## XML IDs and naming

Security, View and Action

```

Use the following pattern :

* For a menu: :samp:`{<model_name>}_menu`, or :samp:`{<model_name>}_menu_{do_stuff}` for submenus.
* For a view: :samp:`{<model_name>}_view_{<view_type>}`, where *view_type* is
  ``kanban``, ``form``, ``list``, ``search``, ...
* For an action: the main action respects :samp:`{<model_name>}_action`.
  Others are suffixed with :samp:`_{<detail>}`, where *detail* is a
  lowercase string briefly explaining the action. This is used only if
  multiple actions are declared for the model.
* For window actions: suffix the action name by the specific view information
  like :samp:`{<model_name>}_action_view_{<view_type>}`.
* For a group: :samp:`{<module_name>}_group_{<group_name>}` where *group_name*
  is the name of the group, generally 'user', 'manager', ...
* For a rule: :samp:`{<model_name>}_rule_{<concerned_group>}` where
  *concerned_group* is the short name of the concerned group ('user'
  for the 'model_name_group_user', 'public' for public user, 'company'
  for multi-company rules, ...).

Name should be identical to xml id with dots replacing underscores. Actions
should have a real naming as it is used as display name.

.. code-block:: xml

    <!-- views  -->
    <record id="model_name_view_form" model="ir.ui.view">
        <field name="name">model.name.view.form</field>
        ...
    </record>

    <record id="model_name_view_kanban" model="ir.ui.view">
        <field name="name">model.name.view.kanban</field>
        ...
    </record>

    <!-- actions -->
    <record id="model_name_action" model="ir.act.window">
        <field name="name">Model Main Action</field>
        ...
    </record>

    <record id="model_name_action_child_list" model="ir.actions.act_window">
        <field name="name">Model Access Children</field>
    </record>

    <!-- menus and sub-menus -->
    <menuitem
        id="model_name_menu_root"
        name="Main Menu"
        sequence="5"
    />
    <menuitem
        id="model_name_menu_action"
        name="Sub Menu 1"
        parent="module_name.module_name_menu_root"
        action="model_name_action"
        sequence="10"
    />

    <!-- security -->
    <record id="module_name_group_user" model="res.groups">
        ...
    </record>

    <record id="model_name_rule_public" model="ir.rule">
        ...
    </record>

    <record id="model_name_rule_company" model="ir.rule">
        ...
    </record>

Inheriting XML
~~~~~~~~~~~~~~

Xml Ids of inheriting views should use the same ID as the original record.
It helps finding all inheritance at a glance. As final Xml Ids are prefixed
by the module that creates them there is no overlap.

Naming should contain an ``.inherit.{details}`` suffix to ease understanding
the override purpose when looking at its name.

.. code-block:: xml

    <record id="model_view_form" model="ir.ui.view">
        <field name="name">model.view.form.inherit.module2</field>
        <field name="inherit_id" ref="module1.model_view_form"/>
        ...
    </record>

New primary views do not require the inherit suffix as those are new records
based upon the first one.

.. code-block:: xml

    <record id="module2.model_view_form" model="ir.ui.view">
        <field name="name">model.view.form.module2</field>
        <field name="inherit_id" ref="module1.model_view_form"/>
        <field name="mode">primary</field>
        ...
    </record>

.. _contributing/development/python_guidelines:

Python
======

.. warning::

    Do not forget to read the :ref:`Security Pitfalls <reference/security/pitfalls>`
    section as well to write secure code.

PEP8 options
------------

Using a linter can help show syntax and semantic warnings or errors. Odoo
source code tries to respect Python standard, but some of them can be ignored.

- E501: line too long
- E301: expected 1 blank line, found 0
- E302: expected 2 blank lines, found 1

Imports
-------

The imports are ordered as

#. External libraries (one per line sorted and split in python stdlib)
#. Imports of ``odoo`` submodules
#. Imports from Odoo addons (rarely, and only if necessary)

Inside these 3 groups, the imported lines are alphabetically sorted.

.. code-block:: python

    # 1 : imports of python lib
    import base64
    import re
    import time
    from datetime import datetime
    # 2 : imports of odoo
    from odoo import Command, _, api, fields, models # ASCIIbetically ordered
    from odoo.fields import Domain
    from odoo.tools.safe_eval import safe_eval as eval
    # 3 : imports from odoo addons
    from odoo.addons.web.controllers.main import login_redirect
    from odoo.addons.website.models.website import slug

Idiomatics of Programming (Python)
----------------------------------

- Always favor *readability* over *conciseness* or using the language features or idioms.
- Don't use ``.clone()``

.. code-block:: python

    # bad
    new_dict = my_dict.clone()
    new_list = old_list.clone()
    # good
    new_dict = dict(my_dict)
    new_list = list(old_list)

- Python dictionary : creation and update

.. code-block:: python

    # -- creation empty dict
    my_dict = {}
    my_dict2 = dict()

    # -- creation with values
    # bad
    my_dict = {}
    my_dict['foo'] = 3
    my_dict['bar'] = 4
    # good
    my_dict = {'foo': 3, 'bar': 4}

    # -- update dict
    # bad
    my_dict['foo'] = 3
    my_dict['bar'] = 4
    my_dict['baz'] = 5
    # good
    my_dict.update(foo=3, bar=4, baz=5)
    my_dict = dict(my_dict, **my_dict2)

- Use meaningful variable/class/method names
- Useless variable : Temporary variables can make the code clearer by giving
  names to objects, but that doesn't mean you should create temporary variables
  all the time:

.. code-block:: python

    # pointless
    schema = kw['schema']
    params = {'schema': schema}
    # simpler
    params = {'schema': kw['schema']}

- Multiple return points are OK, when they're simpler

.. code-block:: python

    # a bit complex and with a redundant temp variable
    def axes(self, axis):
        axes = []
        if type(axis) == type([]):
            axes.extend(axis)
        else:
            axes.append(axis)
        return axes

     # clearer
    def axes(self, axis):
        if type(axis) == type([]):
            return list(axis) # clone the axis
        else:
            return [axis] # single-element list

- Know your builtins : You should at least have a basic understanding of all
  the Python builtins (http://docs.python.org/library/functions.html)

.. code-block:: python

    value = my_dict.get('key', None) # very very redundant
    value = my_dict.get('key') # good

Also, ``if 'key' in my_dict`` and ``if my_dict.get('key')`` have very different
meaning, be sure that you're using the right one.

- Learn list comprehensions : Use list comprehension, dict comprehension, and
  basic manipulation using ``map``, ``filter``, ``sum``, ... They make the code
  easier to read.

.. code-block:: python

    # not very good
    cube = []
    for i in res:
        cube.append((i['id'],i['name']))
    # better
    cube = [(i['id'], i['name']) for i in res]

- Collections are booleans too : In python, many objects have "boolean-ish" value
  when evaluated in a boolean context (such as an if). Among these are collections
  (lists, dicts, sets, ...) which are "falsy" when empty and "truthy" when containing
  items:

.. code-block:: python

    bool([]) is False
    bool([1]) is True
    bool([False]) is True

So, you can write ``if some_collection:`` instead of ``if len(some_collection):``.


- Iterate on iterables

.. code-block:: python

    # creates a temporary list and looks bar
    for key in my_dict.keys():
        "do something..."
    # better
    for key in my_dict:
        "do something..."
    # accessing the key,value pair
    for key, value in my_dict.items():
        "do something..."

- Use dict.setdefault

.. code-block:: python

    # longer.. harder to read
    values = {}
    for element in iterable:
        if element not in values:
            values[element] = []
        values[element].append(other_value)

    # better.. use dict.setdefault method
    values = {}
    for element in iterable:
        values.setdefault(element, []).append(other_value)

- As a good developer, document your code (docstring on methods, simple
  comments for tricky part of code)
- In additions to these guidelines, you may also find the following link
  interesting: https://david.goodger.org/projects/pycon/2007/idiomatic/handout.html
  (a little bit outdated, but quite relevant)

Programming in Odoo
-------------------

- Avoid to create generators and decorators: only use the ones provided by
  the Odoo API.
- As in python, use ``filtered``, ``mapped``, ``sorted``, ... methods to
  ease code reading and performance.

Propagate the context
~~~~~~~~~~~~~~~~~~~~~

The context is a ``frozendict`` that cannot be modified. To call a method with
a different context, the ``with_context`` method should be used :

.. code-block:: python

    records.with_context(new_context).do_stuff() # all the context is replaced
    records.with_context(**additionnal_context).do_other_stuff() # additionnal_context values override native context ones

.. warning::
      Passing parameter in context can have dangerous side-effects.

      Since the values are propagated automatically, some unexpected behavior may appear.
      Calling ``create()`` method of a model with *default_my_field* key in context
      will set the default value of *my_field* for the concerned model.
      But if during this creation, other objects (such as sale.order.line, on sale.order creation)
      having a field name *my_field* are created, their default value will be set too.

If you need to create a key context influencing the behavior of some object,
choose a good name, and eventually prefix it by the name of the module to
isolate its impact. A good example are the keys of ``mail`` module :
*mail_create_nosubscribe*, *mail_notrack*, *mail_notify_user_signature*, ...

Think extendable
~~~~~~~~~~~~~~~~

Functions and methods should not contain too much logic: having a lot of small
and simple methods is more advisable than having few large and complex methods.
A good rule of thumb is to split a method as soon as it has more than one
responsibility (see http://en.wikipedia.org/wiki/Single_responsibility_principle).

Hardcoding a business logic in a method should be avoided as it prevents to be
easily extended by a submodule.

.. code-block:: python

    # do not do this
    # modifying the domain or criteria implies overriding whole method
    def action(self):
        ...  # long method
        partners = self.env['res.partner'].search(complex_domain)
        emails = partners.filtered(lambda r: arbitrary_criteria).mapped('email')

    # better but do not do this either
    # modifying the logic forces to duplicate some parts of the code
    def action(self):
        ...
        partners = self._get_partners()
        emails = partners._get_emails()

    # better
    # minimum override
    def action(self):
        ...
        partners = self.env['res.partner'].search(self._get_partner_domain())
        emails = partners.filtered(lambda r: r._filter_partners()).mapped('email')

The above code is over extendable for the sake of example but the readability
must be taken into account and a tradeoff must be made.

Also, name your functions accordingly: small and properly named functions are
the starting point of readable/maintainable code and tighter documentation.

This recommendation is also relevant for classes, files, modules and packages.
(See also http://en.wikipedia.org/wiki/Cyclomatic_complexity)

Never commit the transaction
```

The Odoo framework is in charge of providing the transactional context for
all RPC calls.
All `cr.commit()` calls outside of the server framework must
have an **explicit comment** explaining why they are absolutely necessary, why
they are indeed correct, and why they do not break the transactions. Otherwise
they can and will be removed!

The principle is that a new database cursor is opened at the beginning of each
RPC call, and committed when the call has returned, just before transmitting the
answer to the RPC client, approximately like this:

.. code-block:: python

    def execute(self, db_name, uid, obj, method, *args, **kw):
        db, pool = pooler.get_db_and_pool(db_name)
        # create transaction cursor
        cr = db.cursor()
        try:
            res = pool.execute_cr(cr, uid, obj, method, *args, **kw)
            cr.commit() # all good, we commit
        except Exception:  # try to be more specific
            cr.rollback() # error, rollback everything atomically
            raise
        finally:
            cr.close() # always close cursor opened manually
        return res

If any error occurs during the execution of the RPC call, the transaction is
rolled back atomically, preserving the state of the system.

Similarly, the system also provides a dedicated transaction during the execution
of tests suites and scheduled actions.

The consequence is that if you manually call `cr.commit()` anywhere there is
a very high chance that you will break the system in various ways, because you
will cause partial commits, and thus partial and unclean rollbacks, causing
among others:

#. inconsistent business data, usually data loss
#. workflow desynchronization, documents stuck permanently
#. tests that can't be rolled back cleanly, and will start polluting the
database, and triggering error (this is true even if no error occurs
during the transaction)

Here is the very simple rule:
You should **NEVER** call `cr.commit()` or `cr.rollback()` yourself,
**UNLESS** you have explicitly created your own database cursor!
And the situations in which you need to do this are exceptional!

    And by the way if you did create your own cursor, then you need to handle
    error cases and proper rollback, as well as properly close the cursor when
    you're done with it.

And contrary to popular belief, you do not even need to call `cr.commit()`
in the following situations:

- in the `_auto_init()` method of an _models.Model_ object: this is taken
  care of by the addons initialization method, or by the ORM transaction when
  creating custom models
- in reports: the `commit()` is handled by the framework too, so you can
  update the database even from within a report
- within _models.Transient_ methods: these methods are called exactly like
  regular _models.Model_ ones, within a transaction and with the corresponding
  `cr.commit()/rollback()` at the end
- etc. (see general rule above if you are in doubt!)

Avoid catching exceptions

```

Catch only specific exceptions, and avoid overly broad exception handling.
Uncaught exceptions will be logged and handled properly by the framework.

You should be specific about the types you catch and handle them
accordingly, and you should limit the scope of your try-catch block as much
as possible.

.. code-block:: python

    # BAD CODE
    try:
        do_something()
    except Exception as e:
        # if we caught a ValidationError, we did not rollback and we left the
        # ORM in an undefined state
        _logger.warning(e)

For scheduled actions, you should rollback the changes if you catch errors and
wish to continue. Scheduled actions run in a separate transaction, so you can
rollback or commit directly when you signal progress.

.. seealso::
   :ref:`reference/actions/cron`

If you must handle framework exceptions, you must use **savepoints**
to isolate your function as much as possible.
This will flush the computations when entering the block and rollback changes
properly in case of exceptions.

.. code-block:: python

    try:
        with self.env.cr.savepoint():
            do_stuff()
    except ...:
        ...

.. warning::

    After you start more than 64 savepoints during a single transaction,
    PostgreSQL will slow down.
    In all cases, if the server runs replicas, savepoints have a huge overhead.
    If you process records and savepoint in a loop, for example when processing
    records one by one for a batch, limit the size of the batch.
    If you have more records, the function should maybe become a scheduled job
    or you have to accept the performance penalty.

Use translation method correctly
```

Odoo uses a GetText-like method named "underscore" `_()` to indicate that
a static string used in the code needs to be translated at runtime.
That method is available at `self.env._` using the language of the
environment.

A few very important rules must be followed when using it, in order for it to
work and to avoid filling the translations with useless junk.

Basically, this method should only be used for static strings written manually
in the code, it will not work to translate field values, such as Product names,
etc. This must be done instead using the translate flag on the corresponding
field.

The method accepts optional positional or named parameter
The rule is very simple: calls to the underscore method should always be in
the form `self.env._('literal string')` and nothing else:

.. code-block:: python

    _ = self.env._

    # good: plain strings
    error = _('This record is locked!')

    # good: strings with formatting patterns included
    error = _('Record %s cannot be modified!', record)

    # ok too: multi-line literal strings
    error = _("""This is a bad multiline example
                 about record %s!""", record)
    error = _('Record %s cannot be modified' \
              'after being validated!', record)

    # bad: tries to translate after string formatting
    #      (pay attention to brackets!)
    # This does NOT work and messes up the translations!
    error = _('Record %s cannot be modified!' % record)

    # bad: formatting outside of translation
    # This won't benefit from fallback mechanism in case of bad translation
    error = _('Record %s cannot be modified!') % record

    # bad: dynamic string, string concatenation, etc are forbidden!
    # This does NOT work and messes up the translations!
    error = _("'" + que_rec['question'] + "' \n")

    # bad: field values are automatically translated by the framework
    # This is useless and will not work the way you think:
    error = _("Product %s is out of stock!") % _(product.name)
    # and the following will of course not work as already explained:
    error = _("Product %s is out of stock!" % product.name)

    # Instead you can do the following and everything will be translated,
    # including the product name if its field definition has the
    # translate flag properly set:
    error = _("Product %s is not available!", product.name)

Also, keep in mind that translators will have to work with the literal values
that are passed to the underscore function, so please try to make them easy to
understand and keep spurious characters and formatting to a minimum. Translators
must be aware that formatting patterns such as `%s` or `%d`, newlines, etc.
need to be preserved, but it's important to use these in a sensible and obvious
manner:

.. code-block:: python

    # Bad: makes the translations hard to work with
    error = "'" + question + _("' \nPlease enter an integer value ")

    # Ok (pay attention to position of the brackets too!)
    error = _("Answer to question %s is not valid.\n" \
              "Please enter an integer value.", question)

    # Better
    error = _("Answer to question %(title)s is not valid.\n" \
              "Please enter an integer value.", title=question)

In general in Odoo, when manipulating strings, prefer `%` over `.format()`
(when only one variable to replace in a string), and prefer `%(varname)` instead
of position (when multiple variables have to be replaced). This makes the
translation easier for the community translators.

## Symbols and Conventions

- Model name (using the dot notation, prefix by the module name) :
  - When defining an Odoo Model : use singular form of the name (_res.partner_
    and _sale.order_ instead of _res.partnerS_ and _saleS.orderS_)
  - When defining an Odoo Transient (wizard) : use `<related_base_model>.<action>`
    where _related_base_model_ is the base model (defined in _models/_) related
    to the transient, and _action_ is the short name of what the transient do. Avoid the _wizard_ word.
    For instance : `account.invoice.make`, `project.task.delegate.batch`, ...
  - When defining _report_ model (SQL views e.i.) : use
    `<related_base_model>.report.<action>`, based on the Transient convention.

- Odoo Python Class : use Pascal case (Object-oriented style).

.. code-block:: python

    class AccountInvoice(models.Model):
        ...

- Variable name :
  - use Pascal case for model variable
  - use underscore lowercase notation for common variable.
  - suffix your variable name with _\_id_ or _\_ids_ if it contains a record id or list of id. Don't use `partner_id` to contain a record of res.partner

.. code-block:: python

    Partner = self.env['res.partner']
    partners = Partner.browse(ids)
    partner_id = partners[0].id

- `One2Many` and `Many2Many` fields should always have _\_ids_ as suffix (example: sale_order_line_ids)
- `Many2One` fields should have _\_id_ as suffix (example : partner_id, user_id, ...)
- Method conventions
  - Compute Field : the compute method pattern is _*compute*<field_name>_
  - Search method : the search method pattern is _*search*<field_name>_
  - Default method : the default method pattern is _*default*<field_name>_
  - Selection method: the selection method pattern is _*selection*<field_name>_
  - Onchange method : the onchange method pattern is _*onchange*<field_name>_
  - Constraint method : the constraint method pattern is _*check*<constraint_name>_
  - Action method : an object action method is prefix with \_action\_\_.
    Since it uses only one record, add `self.ensure_one()`
    at the beginning of the method.

- In a Model attribute order should be
  #. Private attributes (`_name`, `_description`, `_inherit`, ...)
  #. Default method and `default_get`
  #. Field declarations
  #. SQL constraints and indexes
  #. Compute, inverse and search methods in the same order as field declaration
  #. Selection method (methods used to return computed values for selection fields)
  #. Constrains methods (`@api.constrains`) and onchange methods (`@api.onchange`)
  #. CRUD methods (ORM overrides)
  #. Action methods
  #. And finally, other business methods.

.. code-block:: python

    class Event(models.Model):
        # Private attributes
        _name = 'event.event'
        _description = 'Event'

        # Default methods
        def _default_name(self):
            ...

        # Fields declaration
        name = fields.Char(string='Name', default=_default_name)
        seats_reserved = fields.Integer(string='Reserved Seats', store=True
            readonly=True, compute='_compute_seats')
        seats_available = fields.Integer(string='Available Seats', store=True
            readonly=True, compute='_compute_seats')
        price = fields.Integer(string='Price')
        event_type = fields.Selection(string="Type", selection='_selection_type')

        # compute and search fields, in the same order of fields declaration
        @api.depends('seats_max', 'registration_ids.state', 'registration_ids.nb_register')
        def _compute_seats(self):
            ...

        @api.model
        def _selection_type(self):
            return []

        # Constraints and onchanges
        @api.constrains('seats_max', 'seats_available')
        def _check_seats_limit(self):
            ...

        @api.onchange('date_begin')
        def _onchange_date_begin(self):
            ...

        # CRUD methods (and name_search, _search, ...) overrides
        @api.model
        def create(self, vals_list):
            ...

        # Action methods
        def action_validate(self):
            self.ensure_one()
            ...

        # Business methods
        def mail_user_confirm(self):
            ...

.. \_contributing/development/js_guidelines:

# Javascript

## Static files organization

Odoo addons have some conventions on how to structure various files. We explain
here in more details how web assets are supposed to be organized.

The first thing to know is that the Odoo server will serve (statically) all files
located in a _static/_ folder, but prefixed with the addon name. So, for example,
if a file is located in _addons/web/static/src/js/some_file.js_, then it will be
statically available at the url _your-odoo-server.com/web/static/src/js/some_file.js_

The convention is to organize the code according to the following structure:

- _static_: all static files in general
  - _static/lib_: this is the place where js libs should be located, in a sub folder.
    So, for example, all files from the _jquery_ library are in _addons/web/static/lib/jquery_
  - _static/src_: the generic static source code folder
    - _static/src/css_: all css files
    - _static/fonts_
    - _static/img_
    - _static/src/js_
      - _static/src/js/tours_: end user tour files (tutorials, not tests)

    - _static/src/scss_: scss files
    - _static/src/xml_: all qweb templates that will be rendered in JS

  - _static/tests_: this is where we put all test related files.
    - _static/tests/tours_: this is where we put all tour test files (not tutorials).

## Javascript coding guidelines

- `use strict;` is recommended for all javascript files
- Use a linter (jshint, ...)
- Never add minified Javascript Libraries
- Use Pascal case for class declaration

More precise JS guidelines are detailed in the `github wiki  <https://github.com/odoo/odoo/wiki/Javascript-coding-guidelines>`\_.
You may also have a look at existing API in Javascript by looking Javascript
References.

.. \_contributing/coding_guidelines/scss:

# Git guidelines

## Configure your git

Based on ancestral experience and oral tradition, following things go a long way towards making your commits more helpful:

- Be sure to define both `user.email` and `user.name` in your local git config

```bash
git config --global <var> <value>
```

- Be sure to add your full name to your Github profile here. Please feel fancy and add your team, avatar, your favorite quote, and whatnot ;-)

## Commit message structure

Commit message has four parts: tag, module, short description and full description. Try to follow the preferred structure for your commit messages

```
[TAG] module: describe your change in a short sentence (ideally < 50 chars)
Long version of change description, including rationale for change,
or a summary of feature being introduced.

Please spend a lot more time describing WHY the change is being done rather
than WHAT is being changed. This is usually easy to grasp by actually reading
the diff. WHAT should be explained only if there are technical choices
or decision involved. In that case explain WHY this decision was taken.

End message with references, such as task or bug numbers, PR numbers, and
OPW tickets, following the suggested format:
task-123 (related to task)
Fixes #123  (close related issue on Github)
Closes #123 (close related PR on Github)
opw-123 (related to ticket)
```

## Tag and module name

Tags are used to prefix your commit. They should be one of the following

- **[FIX]** for bug fixes: mostly used in stable version but also valid if you are fixing a recent bug in development version;
- **[REF]** for refactoring: when a feature is heavily rewritten;
- **[ADD]** for adding new modules;
- **[REM]** for removing resources: removing dead code, removing views, removing modules, …;
- **[REV]** for reverting commits: if a commit causes issues or is not wanted reverting it is done using this tag;
- **[MOV]** for moving files: use git move and do not change content of moved file otherwise Git may lose track and history of file; also used when moving code from one file to another;
- **[REL]** for release commits: new major or minor stable versions;
- **[IMP]** for improvements: most of the changes done in development version are incremental improvements not related to another tag;
- **[MERGE]** for merge commits: used in forward port of bug fixes but also as main commit for feature involving several separated commits;
- **[CLA]** for signing the Odoo Individual Contributor License;
- **[I18N]** for changes in translation files;
- **[PERF]** for performance patches;
- **[CLN]** for code cleanup;
- **[LINT]** for linting passes;

After tag comes the modified module name. Use the technical name as functional name may change with time. If several modules are modified, list them or use various to tell it is cross-modules. Unless really required or easier avoid modifying code across several modules in the same commit. Understanding module history may become difficult.

## Commit message header

After tag and module name comes a meaningful commit message header. It should be self explanatory and include the reason behind the change. Do not use single words like "bugfix" or "improvements". Try to limit the header length to about 50 characters for readability.

Commit message header should make a valid sentence once concatenated with `if applied, this commit will <header>`. For example `[IMP] base: prevent to archive users linked to active partners` is correct as it makes a valid sentence `if applied, this commit will prevent users to archive...`.

## Commit message full description

In the message description specify the part of the code impacted by your changes (module name, lib, transversal object, …) and a description of the changes.

First explain WHY you are modifying code. What is important if someone goes back to your commit in about 4 decades (or 3 days) is why you did it. It is the purpose of the change.

What you did can be found in the commit itself. If there was some technical choices involved it is a good idea to explain it also in the commit message after the why.

For Odoo R&D developers "PO team asked me to do it" is not a valid why, by the way.

Please avoid commits which simultaneously impact multiple modules. Try to split into different commits where impacted modules are different. It will be helpful if we need to revert changes in a given module separately.

Don't hesitate to be a bit verbose. Most people will only see your commit message and judge everything you did in your life just based on those few sentences. No pressure at all.

**You spend several hours, days or weeks working on meaningful features. Take** some time to calm down and write clear and understandable commit messages.

If you are an Odoo R&D developer WHY should be the purpose of the task you are working on. Full specifications make the core of the commit message.

**If you are working on a task that lacks purpose and specifications please** consider making them clear before continuing.

Finally here are some examples of correct commit messages:

```
[REF] models: use `parent_path` to implement parent_store
 This replaces the former modified preorder tree traversal (MPTT) with
 fields `parent_left`/`parent_right`[...]
 Closes #22793
 Fixes #22769

[FIX] website: remove unused alert div, fixes look of input-group-btn
 Bootstrap's CSS depends on input-group-btn
 element being the first/last child of its parent.
 This was not the case because of the invisible
 and useless alert.
```

**Use the long description to explain _why_ not _what_, _what_ can be seen in the diff**

# Security in Odoo

Aside from manually managing access using custom code, Odoo provides two main data-driven mechanisms to manage or restrict access to data.

Both mechanisms are linked to specific users through _groups_: a user belongs to any number of groups, and security mechanisms are associated to groups, thus applying security mechanisms to users.

## res.groups

- name serves as user-readable identification for the group (spells out role / purpose of group)
- category_id: The module category, serves to associate groups with an Odoo App (~a set of related business models) and convert them into an exclusive selection in user form.
- implied_ids: Other groups to set on the user alongside this one. This is a convenience pseudo-inheritance relationship: it's possible to explicitly remove implied groups from a user without removing the implier.
- comment: Additional notes on the group e.g.

## Access Rights

_Grants_ access to an entire model for a given set of operations. If no access rights matches an operation on a model for a user (through their group), user doesn't have access.

Access rights are additive, a user's accesses are the union of the accesses they get through all their groups e.g. given a user who is part of group A granting read and create access and a group B granting update access, user will have all three of create, read, and update.

### ir.model.access

- name: The purpose or role of the group.
- model_id: The model whose access ACL controls.
- group_id: The `res.groups` to which accesses are granted, an empty `group_id` means ACL is granted to _every user_ (non-employees e.g. portal or public users).
- The `perm_{method}` attributes grant the corresponding CRUD access when set, they are all unset by default.
  - perm_create
  - perm_read
  - perm_write
  - perm_unlink

## Record Rules

Record rules are _conditions_ which must be satisfied in order for an operation to be allowed. Record rules are evaluated record-by-record, following access rights.

Record rules are default-allow: if access rights grant access and no rule applies to the operation and model for the user, access is granted.

### ir.rule

- name: The description of the rule.
- model_id: The model to which the rule applies.
- groups: The `res.groups` to which access is granted (or not). Multiple groups can be specified. If no group is specified, the rule is _global_ which is treated differently than "group" rules (see below).
- global: Computed on the basis of `groups`, provides easy access to the global status (or not) of the rule.
- domain_force: A predicate specified as a domain, rule allows the selected operations if the domain matches the record, and forbids it otherwise.

The domain is a _python expression_ which can use the following variables:

- `time`: Python's `time` module.
- `user`: The current user, as a singleton recordset.
- `company_id`: The current user's currently selected company as a single company id (not a recordset).
- `company_ids`: All the companies to which the current user has access as a list of company ids (not a recordset).

The `perm_{method}` have completely different semantics than for `ir.model.access`: for rules, they specify which operation the rules applies _for_. If an operation is not selected, then the rule is not checked for it, as if the rule did not exist. All operations are selected by default.

- perm_create
- perm_read
- perm_write
- perm_unlink

### Global rules versus group rules

There is a large difference between global and group rules in how they compose and combine:

- Global rules _intersect_, if two global rules apply then _both_ must be satisfied for access to be granted, this means adding global rules always restricts access further.
- Group rules _union_, if two group rules apply then _either_ can be satisfied for access to be granted. This means adding group rules can expand access, but not beyond the bounds defined by global rules.
- The global and group rulesets _intersect_, which means the first group rule being added to a given global ruleset will restrict access.

**Creating multiple global rules is risky as it's possible to create non-overlapping rulesets, which will remove all access.**

## Field Access

An ORM `Field` can have a `groups` attribute providing a list of groups (as a comma-separated string of external identifiers).

If the current user is not in one of the listed groups, he will not have access to the field:

- restricted fields are automatically removed from requested views
- restricted fields are removed from `fields_get()` responses
- attempts to (explicitly) read from or write to restricted fields results in an access error

## Security Pitfalls

As a developer, it is important to understand security mechanisms and avoid common mistakes leading to insecure code.

### Unsafe Public Methods

Any public method can be executed via a RPC call with chosen parameters. The methods starting with a `_` are not callable from an action button or external API.

On public methods, record on which a method is executed and parameters can not be trusted, ACL being only verified during CRUD operations.

```python
# this method is public and its arguments can not be trusted
def action_done(self):
    if self.state == "draft" and self.env.user.has_group('base.manager'):
        self._set_state("done")

# this method is private and can only be called from other python methods
def _set_state(self, new_state):
    self.sudo().write({"state": new_state})
```

Making a method private is obviously not enough and care must be taken to use it properly.

### Bypassing the ORM

You should never use the database cursor directly when the ORM can do the same thing! By doing so you are bypassing all the ORM features, possibly automated behaviours like translations, invalidation of fields, `active`, access rights and so on.

And chances are that you are also making the code harder to read and probably less secure.

```python
# very very wrong
self.env.cr.execute('SELECT id FROM auction_lots WHERE auction_id in (' + ','.join(map(str, ids))+') AND state=%s AND obj_price > 0', ('draft',))
auction_lots_ids = [x[0] for x in self.env.cr.fetchall()]

# no injection, but still wrong
self.env.cr.execute('SELECT id FROM auction_lots WHERE auction_id in %s AND state=%s AND obj_price > 0', (tuple(ids), 'draft',))
auction_lots_ids = [x[0] for x in self.env.cr.fetchall()]

# nearly there
auction_lots_ids = [x[x] for x in self.env.execute_query(SQL("""
    SELECT id FROM auction_lots
    WHERE auction_id IN %s AND state = %s AND obj_price > 0
    """, tuple(ids), 'draft'))#

# better
auction_lots_ids = self.search([('auction_id','in',ids), ('state','=','draft'), ('obj_price','>',0)])#
```

#### SQL injections

Care must be taken not to introduce SQL injections vulnerabilities when using manual SQL queries. The vulnerability is present when user input is either incorrectly filtered or badly quoted, allowing an attacker to introduce undesirable clauses to a SQL query (such as circumventing filters or executing `UPDATE` or `DELETE` commands).

The best way to be safe is to never, NEVER use Python string concatenation (+) nor string parameters interpolation (%) to pass variables to a SQL query string.

The second reason, which is almost as important, is that it is the job of the database abstraction layer (psycopg2) to decide how to format query parameters, not your job! For example psycopg2 knows that when you pass a list of values it needs to format them as a comma-separated list, enclosed in parentheses!

Even better, there exists a `SQL` wrapper to build queries using templates that handles the formatting of inputs.

```python
# the following is very bad:
#   - it's a SQL injection vulnerability
#   - it's unreadable
#   - it's not your job to format the list of ids
self.env.cr.execute('SELECT distinct child_id FROM account_account_consol_rel ' +
           'WHERE parent_id IN ('+','.join(map(str, ids))+')')#

# better
self.env.cr.execute('SELECT DISTINCT child_id ' +
           'FROM account_account_consol_rel ' +
           'WHERE parent_id IN %s',
           (tuple(ids),)#

# more readable
self.env.cr.execute(SQL("""
    SELECT DISTINCT child_id
    FROM account_account_consol_rel
    WHERE parent_id IN %s
    """, tuple(ids)))#
```

This is very important, so please be careful also when refactoring, and most importantly do not copy these patterns!

### Building the domains

Domains are represented as lists and are serializable. You would be tempted to manipulate these lists directly, however it can introduce subtle issues where the user could inject a domain if the input is not normalized.

Use `Domain` to handle safely the manipulation of domains.

```python
# bad
# the user can just pass ['|', ('id', '>', 0)] to access all
domain = ...  # passed by the user
security_domain = [('user_id', '=', self.env.uid)]
domain += security_domain  # can have a side-effect if this is a function argument
self.search(domain)#

# better
domain = Domain(...)
domain &= Domain(('user_id', '=', self.env.uid))
self.search(domain)#
```

### Unescaped field content

When rendering content using JavaScript and XML, one may be tempted to use a `t-raw` to display rich-text content. This should be avoided as a frequent XSS vector.

It is very hard to control the integrity of data from the computation until the final integration in the browser DOM. A `t-raw` that is correctly escaped at time of introduction may no longer be safe at the next bugfix or refactoring.

```xml
<div t-name="insecure_template">
    <div id="information-bar"><t t-raw="info_message" /></div>
</div>
```

The above code may feel safe as the message content is controlled but is a bad practice that may lead to unexpected security vulnerabilities once this code evolves in the future.

### Creating safe content using Markup

See official documentation for explanations, but the big advantage of `Markup` is that it's a very rich type overriding `str` operations to _automatically escape parameters_.

This means that it's easy to create _safe_ html snippets by using `Markup` on a string literal and "formatting in" user-provided (and thus potentially unsafe) content:

```python
>>> Markup('<em>Hello</em> ') + '<foo>'
Markup('<em>Hello</em> &lt;foo&gt;')
>>> Markup('<em>Hello</em> %s') % '<foo>'
Markup('<em>Hello</em> &lt;foo&gt;')
```

though it is a very good thing, note that the effects can be odd at times:

```python
>>> Markup('<a>').replace('>', 'x')
Markup('<a>')
>>> Markup('<a>').replace(Markup('>'), 'x')
Markup('<ax>')
>>> Markup('<a&gt;').replace('>', 'x')
Markup('<ax>')
>>> Markup('<a&gt;').replace('>', '&')
Markup('<a&amp;')
```

The `escape` method (and its alias `html_escape`) turns a `str` into a `Markup` and escapes its content. It will not escape the content of a `Markup` object.

```python
def get_name(self, to_html=False):
    if to_html:
        return Markup("<strong>%s</strong>") % self.name  # escape the name
    else:
        return self.name

>>> record.name = "<R&D>"
>>> escape(record.get_name())
Markup("&lt;R&amp;D&gt;")
>>> escape(record.get_name(True))
Markup("<strong>&lt;R&amp;D&gt;</strong>")  # HTML is kept
```

### Escaping vs Sanitizing

**Escaping** is always 100% mandatory when you mix data and code, no matter how safe the data. **Escaping** converts _TEXT_ to _CODE_. It is absolutely mandatory to do it every time you mix _DATA/TEXT_ with _CODE_ (e.g. generating HTML or python code to be evaluated inside a `safe_eval`), because _CODE_ always requires _TEXT_ to be encoded. It is critical for security, but it's also a question of correctness. Even when there is no security risk (because the text is 100% guaranteed to be safe or trusted), it is still required (e.g. to avoid breaking the layout in generated HTML). Escaping will never break any feature, as long as the developer identifies which variable contains _TEXT_ and which contains _CODE_.

**Sanitizing** converts _CODE_ to _SAFER CODE_ (but not necessarily _safe_ code). It does not work on _TEXT_. Sanitizing is only necessary when _CODE_ is untrusted, because it comes in full or in part from some user-provided data. If the user-provided data is in the form of _TEXT_ (e.g. content from a form filled by a user), and if that data was correctly escaped before putting it in _CODE_, then sanitizing is useless (but can still be done). If however, user-provided data was **not escaped**, then sanitizing will **not** work as expected.

```python
>>> from odoo.tools import html_escape, html_sanitize
>>> data = "<R&D>"  # `data` is some TEXT coming from somewhere

# Escaping turns it into CODE, good!
>>> code = html_escape(data)
>>> code
Markup('&lt;R&amp;D&gt;')

# Now you can mix it with other code...
>>> self.website_description = Markup("<strong>%s</strong>") % code

# Sanitizing without escaping is BROKEN: data is corrupted!
>>> html_sanitize(data)
Markup('')

# Sanitizing *after* escaping is OK!
>>> html_sanitize(code)
Markup('<p>&lt;R&amp;D&gt;</p>')
```

### Evaluating content

Some may want to `eval` to parse user provided content. Using `eval` should be avoided at all cost. A safer, sandboxed, method `safe_eval` can be used instead but still gives tremendous capabilities to the user running it and must be reserved for trusted privileged users only as it breaks the barrier between code and data.

```python
# very bad
domain = eval(self.filter_domain)
return self.search(domain)

# better but still not recommended
from odoo.tools import safe_eval
domain = safe_eval(self.filter_domain)
return self.search(domain)

# good
from ast import literal_eval
domain = literal_eval(self.filter_domain)
return self.search(domain)
```

### Accessing object attributes

If the values of a record needs to be retrieved or modified dynamically, one may want to use the `getattr` and `setattr` methods.

```python
# unsafe retrieval of a field value
def _get_state_value(self, res_id, state_field):
    record = self.sudo().browse(res_id)
    return getattr(record, state_field, False)
```

This code is however not safe as it allows to access any property of the record, including private attributes or methods.

The `__getitem__` of a recordset has been defined and accessing a dynamic field value can be easily achieved safely:

```python
# better retrieval of a field value
def _get_state_value(self, res_id, state_field):
    record = self.sudo().browse(res_id)
    return record[state_field]
```

# CSS and SCSS

.. _contributing/coding_guidelines/scss/formatting:

## Syntax and Formatting

.. tabs::

.. code-tab:: html SCSS

      .o_foo, .o_foo_bar, .o_baz {
         height: $o-statusbar-height;

         .o_qux {
            height: $o-statusbar-height * 0.5;
         }
      }

      .o_corge {
         background: $o-list-footer-bg-color;
      }

.. code-tab:: css CSS

      .o_foo, .o_foo_bar, .o_baz {
         height: 32px;
      }

      .o_foo .o_quux, .o_foo_bar .o_quux, .o_baz .o_qux {
         height: 16px;
      }

      .o_corge {
         background: #EAEAEA;
      }

- four (4) space indents, no tabs;
- columns of max. 80 characters wide;
- opening brace (`{`): empty space after the last selector;
- closing brace (`}`): on its own new line;
- one line for each declaration;
- meaningful use of whitespace.

.. spoiler:: Suggested Stylelint settings

.. code-block:: html

      "stylelint.config": {
          "rules": {
              // https://stylelint.io/user-guide/rules

              // Avoid errors
              "block-no-empty": true,
              "shorthand-property-no-redundant-values": true,
              "declaration-block-no-shorthand-property-overrides": true,

              // Stylistic conventions
              "indentation": 4,

              "function-comma-space-after": "always",
              "function-parentheses-space-inside": "never",
              "function-whitespace-after": "always",

              "unit-case": "lower",

              "value-list-comma-space-after": "always-single-line",

              "declaration-bang-space-after": "never",
              "declaration-bang-space-before": "always",
              "declaration-colon-space-after": "always",
              "declaration-colon-space-before": "never",

              "block-closing-brace-empty-line-before": "never",
              "block-opening-brace-space-before": "always",

              "selector-attribute-brackets-space-inside": "never",
              "selector-list-comma-space-after": "always-single-line",
              "selector-list-comma-space-before": "never-single-line",
          }
      },

.. \_contributing/coding_guidelines/scss/properties_order:

## Properties order

Order properties from the "outside" in, starting from `position` and ending with decorative rules
(`font`, `filter`, etc.).

:ref:`Scoped SCSS variables <contributing/coding_guidelines/scss/scoped_scss_variables>` and
:ref:`CSS variables <contributing/coding_guidelines/scss/css_variables>` must be placed at the very
top, followed by an empty line separating them from other declarations.

.. code-block:: html

.o_element {
$-inner-gap: $border-width + $legend-margin-bottom;

      --element-margin: 1rem;
      --element-size: 3rem;

      @include o-position-absolute(1rem);
      display: block;
      margin: var(--element-margin);
      width: calc(var(--element-size) + #{$-inner-gap});
      border: 0;
      padding: 1rem;
      background: blue;
      font-size: 1rem;
      filter: blur(2px);

}

.. \_contributing/coding_guidelines/scss/naming_conventions:

## Naming Conventions

Naming conventions in CSS are incredibly useful in making your code more strict, transparent and
informative.

| Avoid `id` selectors, and prefix your classes with `o_<module_name>`, where `<module_name>` is the
technical name of the module (`sale`, `im_chat`, ...) or the main route reserved by the module
(for website modules mainly, i.e. : `o_forum` for the `website_forum` module).
| The only exception for this rule is the webclient: it simply uses the `o_` prefix.

Avoid creating hyper-specific classes and variable names. When naming nested elements, opt for the
"Grandchild" approach.

.. rst-class:: bg-light
.. example::

.. container:: alert alert-danger

      Don't

      .. code-block:: html

         <div class=“o_element_wrapper”>
            <div class=“o_element_wrapper_entries”>
               <span class=“o_element_wrapper_entries_entry”>
                  <a class=“o_element_wrapper_entries_entry_link”>Entry</a>
               </span>
            </div>
         </div>

.. container:: alert alert-success

      Do

      .. code-block:: html

         <div class=“o_element_wrapper”>
            <div class=“o_element_entries”>
               <span class=“o_element_entry”>
                  <a class=“o_element_link”>Entry</a>
               </span>
            </div>
         </div>

Besides being more compact, this approach eases maintenance because it limits the need of renaming
when changes occur at the DOM.

.. \_contributing/coding_guidelines/scss/scss_variables:

SCSS Variables

```

Our standard convention is `$o-[root]-[element]-[property]-[modifier]`, with:

* `$o-`
    The prefix.
* `[root]`
    Either the component **or** the module name (components take priority).
* `[element]`
    An optional identifier for inner elements.
* `[property]`
    The property/behavior defined by the variable.
* `[modifier]`
    An optional modifier.

.. example::

   .. code-block:: scss

      $o-block-color: value;
      $o-block-title-color: value;
      $o-block-title-color-hover: value;

.. _contributing/coding_guidelines/scss/scoped_scss_variables:

SCSS Variables (scoped)
```

These variables are declared within blocks and are not accessible from the outside.
Our standard convention is `$-[variable name]`.

.. example::

.. code-block:: html

      .o_element {
         $-inner-gap: compute-something;

         margin-right: $-inner-gap;

         .o_element_child {
            margin-right: $-inner-gap * 0.5;
         }
      }

.. seealso::
`Variables scope on the SASS Documentation
   <https://sass-lang.com/documentation/variables#scope>`\_

.. \_contributing/coding_guidelines/scss/mixins:

SCSS Mixins and Functions

```

Our standard convention is `o-[name]`. Use descriptive names. When naming functions, use verbs in
the imperative form (e.g.: `get`, `make`, `apply`...).

Name optional arguments in the :ref:`scoped variables form
<contributing/coding_guidelines/scss/scoped_scss_variables>`, so `$-[argument]`.

.. example::

   .. code-block:: html

      @mixin o-avatar($-size: 1.5em, $-radius: 100%) {
         width: $-size;
         height: $-size;
         border-radius: $-radius;
      }

      @function o-invert-color($-color, $-amount: 100%) {
         $-inverse: change-color($-color, $-hue: hue($-color) + 180);

         @return mix($-inverse, $-color, $-amount);
      }

.. seealso::
   - `Mixins on the SASS Documentation <https://sass-lang.com/documentation/at-rules/mixin>`_
   - `Functions on the SASS Documentation <https://sass-lang.com/documentation/at-rules/function>`_

.. _contributing/coding_guidelines/scss/css_variables:

CSS Variables
~~~~~~~~~~~~~

In Odoo, the use of CSS variables is strictly DOM-related. Use them to **contextually** adapt the
design and layout.

Our standard convention is BEM, so `--[root]__[element]-[property]--[modifier]`, with:

* `[root]`
    Either the component **or** the module name (components take priority).
* `[element]`
    An optional identifier for inner elements.
* `[property]`
    The property/behavior defined by the variable.
* `[modifier]`
    An optional modifier.

.. example::

  .. code-block:: scss

     .o_kanban_record {
        --KanbanRecord-width: value;
        --KanbanRecord__picture-border: value;
        --KanbanRecord__picture-border--active: value;
     }

     // Adapt the component when rendered in another context.
     .o_form_view {
        --KanbanRecord-width: another-value;
        --KanbanRecord__picture-border: another-value;
        --KanbanRecord__picture-border--active: another-value;
     }

.. _contributing/coding_guidelines/scss/variables_use:

Use of CSS Variables
--------------------

In Odoo, the use of CSS variables is strictly DOM-related, meaning that are used to **contextually**
adapt the design and layout rather than to manage the global design-system. These are typically used
when a component's properties can vary in specific contexts or in other circumstances.

We define these properties inside the component's main block, providing default fallbacks.

.. example::

   .. code-block:: scss
      :caption: :file:`my_component.scss`

      .o_MyComponent {
         color: var(--MyComponent-color, #313131);
      }

   .. code-block:: scss
      :caption: :file:`my_dashboard.scss`

      .o_MyDashboard {
         // Adapt the component in this context only
         --MyComponent-color: #017e84;
      }

.. seealso::
   `CSS variables on MDN web docs
   <https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties>`_

.. _contributing/coding_guidelines/scss/css_scss_variables_use:

CSS and SCSS Variables
~~~~~~~~~~~~~~~~~~~~~~

Despite being apparently similar, `CSS` and `SCSS` variables behave very differently. The main
difference is that, while `SCSS` variables are **imperative** and compiled away, `CSS` variables are
**declarative** and included in the final output.

.. seealso::
   `CSS/SCSS variables difference on the SASS Documentation
   <https://sass-lang.com/documentation/variables#:~:text=CSS%20variables%20are%20included%20in,use%20will%20stay%20the%20same>`_

In Odoo, we take the best of both worlds: using the `SCSS` variables to define the design-system
while opting for the `CSS` ones when it comes to contextual adaptations.

The implementation of the previous example should be improved by adding SCSS variables in order to
gain control at the top-level and ensure consistency with other components.

.. example::

   .. code-block:: scss
      :caption: :file:`secondary_variables.scss`

      $o-component-color: $o-main-text-color;
      $o-dashboard-color: $o-info;
      // [...]

   .. code-block:: text
      :caption: :file:`component.scss`

      .o_component {
         color: var(--MyComponent-color, #{$o-component-color});
      }

   .. code-block:: text
      :caption: :file:`dashboard.scss`

      .o_dashboard {
         --MyComponent-color: #{$o-dashboard-color};
      }

.. _contributing/coding_guidelines/scss/root:

The `:root` pseudo-class
~~~~~~~~~~~~~~~~~~~~~~~~

Defining CSS variables on the `:root` pseudo-class is a technique we normally **don't use** in
Odoo's UI. The practice is commonly used to access and modify CSS variables globally. We perform
this using SCSS instead.

Exceptions to this rule should be fairly apparent, such as templates shared across bundles that
require a certain level of contextual awareness in order to be rendered properly.
```
