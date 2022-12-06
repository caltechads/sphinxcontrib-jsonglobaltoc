# sphinx-json-globaltoc

This [Sphinx](http://sphinx-doc.org) extension extends `JSONHTMLBuilder` from
`sphinxcontrib-serializinghtml` to add a `globaltoc` key to each `.fjson` file
produced.  `globaltoc` will contain the HTML for the global table of contents
for the entire set of documentation.

## Getting It

You can get `sphinx-json-globaltoc` by using pip:

```bash
pip install sphinx-json-globaltoc
```

If you want to install it from source, grab the git repository from GitHub and run `setup.py`:

```bash
git clone git://github.com/caltechads/sphinx-json-globaltoc.git
cd sphinx-json-globaltoc
python setup.py install
```

## Installing It

To enable `sphinx-json-globaltoc` in your Sphinx project, you need to add it to `extensions` list
in your `conf.py`:

```python
extensions = [
    ...
    'sphinx_json_globaltoc',
    ...
]
```

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
```
