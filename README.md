This is the repository containing the documentation for the Avalanche Platform. 


# Installing

This software uses `pipenv` to manage its dependencies. To build these docs, you must install the `pipenv` tool. The `pipenv` tool is a package manger which creates a virtual environment for the software its managing, similar to `npm` in nodejs.

On Ubuntu 18.04, using pip3:

```sh
sudo pip3 install pipenv
```

From the directory root, type:

```sh
pipenv install
```

# Building

To build the docs, type:

```sh
pipenv run mkdocs build
```

# Viewing 

To view the documents, first build them as described above, then type:

```sh
pipenv run mkdocs serve
```

This will display:

```sh
[I 200124 17:53:24 server:296] Serving on http://127.0.0.1:8000
[I 200124 17:53:24 handlers:62] Start watching changes
[I 200124 17:53:24 handlers:64] Start detecting changes
```

Now your docs will be served from the url above.

In addition, the generated documents are placed into the "/site" folder in the document root. In that folder, open the `index.html` file is the root of the documents when posted online.

# Editing

The documentation is generated from MarkDown files located in the "/docs" folder in the document root. Edit the *.md files in that folder and then re-build the site. ***DO NOT EDIT CONTENTS OF THE SITE FOLDER DIRECTLY!!!***


