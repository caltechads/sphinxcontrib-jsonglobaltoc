# sphinxcontrib-jsonglobaltoc

This [Sphinx](http://sphinx-doc.org) extension extends `JSONHTMLBuilder` from
`sphinxcontrib-serializinghtml` to add a `globaltoc` key to each `.fjson` file
produced.  `globaltoc` will contain the HTML for the global table of contents
for the entire set of documentation.

## Getting It

You can get `sphinxcontrib-jsonglobaltoc` by using pip:

```bash
pip install sphinxcontrib-jsonglobaltoc
```

If you want to install it from source, grab the git repository from GitHub and run `setup.py`:

```bash
git clone git://github.com/caltechads/sphinxcontrib-jsonglobaltoc.git
cd sphinxcontrib-jsonglobaltoc
python setup.py install
```

## Installing It

To enable `sphinxcontrib-jsonglobaltoc` in your Sphinx project, you need to add
it to `extensions` list in your `conf.py`:

```python
extensions = [
    ...
    'sphinx_json_globaltoc',
    ...
]
```

## Configuring It

`sphinxcontrib-jsonglobaltoc` adds one configuration option to `conf.py`:
`globaltoc_collapse`, a boolean with a default of `True`.

If `True`, do not add entries to the global table of contents for headings under
the included page.   If `False`, do add them.

If you have nested `toctree` blocks, you must set `globaltoc_collapse` to
`False` if you want to see the entries from the nested `toctree` in your
globaltoc.

## Using It

In your Sphinx docs folder, produce your `jsonhtml` output like so:

```bash
make json
```

Now if you open one of your `.ftjson` files and examine it, you
should see a `globaltoc` key.  For example:

```python
>>> import json
>>> with open('build/json/index.fjson', encoding='utf-8') as index:
...     data = json.load(index)
>>> index['globaltoc']
'<ul>\n<li class="toctree-l1"><a class="reference internal" href="page1/">Page 1</a></li>\n<li class="toctree-l1"><a class="reference internal" href="page2/">Page 2</a></li>\n<li class="toctree-l1"><a class="reference internal" href="page3/">Page 3</a></li>\n<li class="toctree-l1"><a class="reference internal" href="page4/">Page 4</a></li>\n<li class="toctree-l1"><a class="reference internal" href="page5/">Page 5</a></li>\n<li class="toctree-l1"><a class="reference internal" href="api/">Developer Interface</a></li>\n</ul>\n'
```

Each `.ftjson` file's ``globaltoc`` key will contain the **full global toc** for the
entire documentation set.  We do this so that you can just look at the
``master_doc`` and extract its ``globaltoc`` key to get the sitemap for the
entire set.  Otherwise you'd have to walk through every page in the set and
merge their individual HTML blobs into a whole.  Not fun.

## How to get nested toctrees to build properly into globaltoc

If you have a single `.. toctree::` declaration in the root page of your
documentation, then it's pretty difficult to make that not render properly into
``globaltoc``.

But if you want nested toctrees, if you don't construct your pages properly,
you'll get a mess in ``globaltoc``.

Let's say that you are writing a book with pages for chapters, and pages for sections
(in chapters) .  You want the sections to appear as children to the chapters,
and to not appear in the global table of contents as siblings of the chapters.

To do that in Sphinx, you have to make the root doc `.. toctree::` that lists
only the chapter pages, then have the chapter pages each have toctrees that list their
own section documents.

### An example

Here's `index.rst`, our root document:

```rst
=======
My Book
=======

.. toctree::
   :hidden:

   chapter1
   chapter2
   chapter3

Some introduction. Lorem ipsum dolor sit amet, consectetur adipiscing elit,
sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad
minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit
esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat
non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
```

Now for a chapter document, `chapter1.rst`, we list the section pages in its
`.. toctree::`:

```rst
*********
Chapter 1
*********

.. toctree::
   :hidden:

   chapter1-section1
   chapter2-section2
   chapter2-section3

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor
incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis
nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu
fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
culpa qui officia deserunt mollit anim id est laborum.
```

Finally, a section document, `chapter1-section1.rst`:

```rst
Chapter 1, Section 1
====================

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor
incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis
nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu
fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
culpa qui officia deserunt mollit anim id est laborum.
```

There are three important things to note here:

* You MUST get the heading levels right.  The top level document heading must be a
  level 1 heading.  In ReStructuredText that is `#` underline and overline.  The
  chapter page must have a level 2 heading.  In ReStructuredText that is `*`
  underline and overline.  If you don't get the heading levels right, you end up with
  very odd nesting behavior in the resultant global table of contents.

* Put your `.. toctree::` declaration directly under the page heading.  On
  sub-pages, the toctree gets its level from the **nearest preceding heading**,
  not from the page heading.  Thus to ensure that the sub-page toctree gets
  inserted into the global toc tree as the right level, you should put your `..
  toctree::` declaration right under the page heading.

* If all you're interested in your global table of contents are the page titles, be
  sure to do add `:maxdepth: 1` to your `.. toctree::` declaration.  You will still
  have access to the local table of contents for the headings on the page in the ``toc``
  key in the `.fjson` file.
