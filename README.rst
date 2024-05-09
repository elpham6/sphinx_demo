Deploying Sphinx Documentation
##############################

Making a documentation website for your python project can be daunting and usually requires some web-dev
skills. But Sphinx can be used to generate documentations for your project - it can automatically convert your docstrings
and in-code documentation into various output formats like HTML, PDF, LaTeX, man-pages, etc.

This guide will show you the framework to easily create and streamline documentation for your Python project on a Git repository, using Sphinx.

Tutorial
********

1. Install Sphinx
==================

- For Debian/Ubuntu/Windows Subsystem for Linux (WSL), run ``sudo apt install python3-sphinx``
- For Anaconda, open the Anaconda terminal and run ``conda install sphinx``

To ``pip`` install:
- For Linux, macOS or Windows, run ``pip install -U sphinx``

(It is recommended that you use a virtual environment when you pip install. To create a virtual environment in the ``.venv`` directory, run ``python -m venv .venv``.)

For installation options not mentioned, refer to the `documentation <https://www.sphinx-doc.org/en/master/usage/installation.html>`_ for more information.

2. Create a Git repository
==========================

1. Create a Git repository using this `template <https://github.com/new?template_name=sphinx_template&template_owner=elpham6>`_. Put whatever name you would like for the Repository name, and click Create repository.

2. The repo's folder structure should have a ``docs`` and ``src`` folder.

``docs`` contain your files for Sphinx documentation. ``src`` should contain your code.

In this guide, the ``src`` folder should have a **calculator.py** and **helloworld.py** file for demo purposes.

**Note on folder structure**: You can employ different directory structures depending on the need for your project. Search for existing packages and Git repo conventions guides, such as `this <https://github.com/kriasoft/Folder-Structure-Conventions>`_
for more references.

3. Setting Up Documentation Sources
====================================

- Change your directory location to the ``docs`` folder.

- Run ``sphinx-quickstart``. You will be prompted with the following:

1. You have two options for placing the build directory for Sphinx output.
Either, you use a directory "_build" within the root path, or you separate
"source" and "build" directories within the root path.
**Separate source and build directories (y/n) [n]:**

Select `n`; it is recommended to have the ``docs`` directory separate.

2. **Project name**: enter your project name here

3. **Author name(s)**: enter your name(s) here

4. **Project release**: enter the version number of your project here

5. **Project language [en]**: enter the language for your project here. Default is English (`en`).

This will create a default configuration ``conf.py`` file, the make files and ``_build``, ``_static``, ``_templates`` folders.

4. Creating Documentation From Modules
======================================

- Go back to the root directory (run ``cd ..``).

- Run ``sphinx-apidoc -o output_dir package_dir``

Here, `-o` flag is for the ``output_dir``, which is ``docs`` in this case, and ``package_dir`` is the directory with all the modules you want to document.
In this case, it is ``src``.

This will create .rst files for each Python module in the specified ``package_dir``.

5. Config Specifications
========================

Open **conf.py** in the **docs** folder. You can see all the default configurations here.

5.1. OS Syspath Change
-----------------------

Add the following to the beginning of **conf.py**:

.. code-block:: python

    import os
    import sys
    sys.path.insert(0, os.path.abspath("../src"))

**Note**: This assumes the folder structure of the template repo. If you have a different structure, make sure to replace ``"../src"`` with the path to the code you would like to make documentation for.

5.2. Adding Extensions
-----------------------

Sphinx has many useful extensions, which you can check out `here <https://www.sphinx-doc.org/en/master/usage/extensions/index.html>`_.

For this tutorial, add the following extensions to the ``extensions`` list:

- ``sphinx.ext.autodoc``: automatically takes doc strings from your Python files

- ``sphinx.ext.napoleon``:  understand NumPy or Google doc string standards and format them nicely. If you write your doc strings using Numpy or Google standard, you need this extension.

  Since this example uses Google-style doc string, add:

    `` napoleon_google_docstring = True ``

  to the **conf.py** file.

- ``sphinx.ext.linkcode``: provides a link to the source code on GitHub. Note that this requires more config specifications, which you can refer to `here <https://www.sphinx-doc.org/en/master/usage/extensions/linkcode.html>`_.

  This guide assumes we want to get HTML output. Add the following dictionary ``html_context`` to pass to ``linkcode`` config:

.. code-block:: python

    html_context = {
    "display_github": True,  # Integrate GitHub
    "github_user": "elpham6",  # Username of repo's owner
    "github_repo": "sphinx_demo",  # Repo name
    "github_version": "main",  # Version
    "conf_py_path": ".",  # Path in the checkout to the docs root
    }

  
  Then, add the ``linkcode`` settings:

.. code-block:: python

    def linkcode_resolve(domain, info):
    if domain != 'py':
        return None
    if not info['module']:
        return None
    filename = info['module'].replace('.', '/')
    # return "https://somesite/sourcerepo/%s.py" % filename
    return f"https://github.com/{html_context['github_user']}/{html_context['github_repo']}/blob/{html_context['github_version']}/{html_context['conf_py_path']}/{filename}.py"

Adjust the config according to your folder structure and names. If you would like a different type of output, refer to Sphinx's `configuration documentation <https://www.sphinx-doc.org/en/master/usage/configuration.html#options-for-html-output>`_.



The resulting ``extensions`` list should look like this:

.. code-block:: python

    extensions = [
        "sphinx.ext.autodoc",
        "sphinx.ext.napoleon",
        "sphinx.ext.linkcode",
      ]


5.3 Theme (Optional)
--------------------

The default theme for the output is Alabaster.

This tutorial uses a Sphinx theme called `Read the Docs <https://sphinx-themes.org/sample-sites/sphinx-rtd-theme/>`_, which has a much better format than the default.

1. To install, run ``pip install sphinx-rtd-theme``.

2. In the **conf.py** file, change the `html_theme` tag to `sphinx_rtd_theme`.

  You can find more themes at various sources like `www.sphinx-themes.org/`,
  `https://sphinxthemes.com`, etc.

6. Building Documentation
=========================

1. Change directory to the `docs` folder.

2. Run ``make html``. The result will be in **docs/_build/html**.

3. To view your build, go to **docs/_build/html**. Open **index.html**, which shows you the homepage for your documentation.

4. If you make any changes to your code or documentation, simply run ``make html`` again from the **docs** folder to update your documentation.

7. Adding More Pages
=====================

The default options in Sphinx produce a great template, but you want to add and adjust content in order to produce a better website.

To add other pages to your Sphinx website, simply create `.rst` files in the appropriate location, then add them to the ``toctree`` of ``index.rst``, or to the ``toctree`` of a file listed/included in ``index.rst``.

For example, when you view your homepage, you will only see the index menu and not the content of your code.
To see the contents of your modules, open ``index.rst`` and manually add `.rst` file names to Contents:

.. code-block:: rst

    .. toctree::
      :maxdepth: 4
      :caption: Contents:

      calculator

      helloworld

Now, when you run ``make html`` again, you will see the homepage showing the ``calculator`` and ``helloworld`` modules' content.
You can also move back and forth between the sections of the documentation using the "Next" or "Previous" buttons.

* For more instructions on defining document structure, refer to
  `Defining Docuement Structure <https://www.sphinx-doc.org/en/master/usage/quickstart.html#defining-document-structure>`_.

* For instructions on how to format reStructuredText, refer to
  `reStructuredText Basics <https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html>`_.

8. Deploying to GitHub Pages
============================

To automatically update the documentation on the website whenever you update your work, one way to do it is set up GitHub Action to trigger every time you push changes to the **main** branch of your repo.
This streamlines the process of keeping your documentation up-to-date.

8.1. Enable GitHub Pages
-------------------------
1. In your GitHub repository, click on **Settings**.

2. On the menu, under "Code and automation", click on **Pages**.

3. In the "Source" drop down menu, choose "GitHub Actions".

8.2. Set Up GitHub Actions
--------------------------

1. Move to the root directory of the repo.

2. Create a folder called `.github`. Then within the folder, create another folder called `workflows`.

3. Move to ``.github/workflows/``.

4. Create a .yml file, name it "sphinx-gitpg.yml".

5. To set up the configuration for the GitHub Action, copy and paste the following into the .yml file:

.. code-block:: yaml

    name: Docs build and upload

    on:
      push:
        branches:
          - main

      workflow_dispatch:

    permissions:
      contents: read
      pages: write
      id-token: write

    concurrency:
      group: "pages"
      cancel-in-progress: false

    jobs:
      docs:
        environment:
          name: github-pages
          url: ${{ steps.deployment.outputs.page_url }}
        runs-on: ubuntu-latest
        steps:
          - name: Checkout
            uses: actions/checkout@v4

          - name: Setup Python
            uses: actions/setup-python@v5
            with:
              python-version: '3.11'
          - name: Setup Sphinx
            run: |
              pip install sphinx sphinx_rtd_theme
          - name: Sphinx Build
            run: |
              cd 'docs'
              make html

          - name: Setup Pages
            uses: actions/configure-pages@v5

          - name: Upload GitHub Pages Artifact
            uses: actions/upload-pages-artifact@v3
            with:
              path: "docs/_build/html"

          - name: Deploy GitHub Pages
            id: deployment
            uses: actions/deploy-pages@v4

This makes sure that the documentation will be built and updated onto the GitHub page url only when you push changes on to your **main** branch.
If you add any more Sphinx extensions that needs to be installed, simply add the dependency to the "Setup Sphinx" step in the .yml file.

For example, ``pip install sphinx sphinx_rtd_theme`` means that the action will install sphinx, and sphinx_rtd_theme.

8.3. Check the Documentation Results
-------------------------------------
To check the result, go to https://user_name.github.io/sphinx_demo/, replace user_name with your GitHub username.

Also, if something fails, you can click on the "Actions" tab from the repository, and check for the error.

Now, if you make any changes and then push to the **main** branch of the repository, the website will automatically update the documentation.

Notes
****************
To ensure a better result:

* Have proper documentation for your code. This includes doc strings.
* Make sure that your doc strings follow a standard, eg. PEP, Google, Numpy, etc. This guide followed `Google doc string conventions <https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_google.html>`_.
* Highly recommended to use a linter for both your code and docs, like `Ruff <https://docs.astral.sh/ruff/#testimonials>`_.


