---
layout: post
title:  "How To Set Up Standalone Spark With Python and Jupyter Notebooks"
date:   2017-06-08 20:34:26 -0300
categories: spark jupyter python howto
---

Here's a guide on how you can quickly get set up a standalone Spark instance and [Jupyter Notebooks](http://jupyter.org/).

This guide is written for MacOS but roughly the same steps should work on linux. Just swap out brew for apt-get or yum or whatever.

Ensure that you have java installed before continuing.

First, [download](https://spark.apache.org/downloads.html) the latest spark distribution (2.1.1 at time of writing) and extract it to some directory - I used ~/opt.

```bash
~ $ mkdir ~/opt
~ $ cd ~/opt
~/opt $ tar zxvf spark-2.1.1-bin-hadoop2.7.tgz
```

Next, install `python` and `virtualenvwrapper`

```bash
$ brew install python3
...
$ brew install pyenv-virtualenvwrapper
...
```

Create a python virtual environment called `spark`
```bash
$ mkvirtualenv -p python3 spark
```

Install some libraries and deactivate the virtual environment (for the moment)
```bash
(spark) ~ $ pip install jupyter pandas
...
(spark) ~ $ deactivate
~ $
```

Next, we're going to add a few environment variables that get set up and torn down when you activate and deactivate your virtual environment.

First is `~/.virtualenvs/spark/bin/postactivate` - make it look like this:
```
#!/bin/bash
# This hook is sourced after this virtualenv is activated.
export PYSPARK_PYTHON=/usr/local/bin/python3
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'
export PATH=$PATH:$HOME/opt/spark-2.1.1-bin-hadoop2.7/bin
export SPARK_HOME=$HOME/opt/spark-2.1.1-bin-hadoop2.7
```

Then make `~/.virtualenvs/spark/bin/postdeactivate` look like this:
```
#!/bin/bash
# This hook is sourced after this virtualenv is deactivated.
unset PYSPARK_PYTHON
unset PYSPARK_DRIVER_PYTHON
unset PYSPARK_DRIVER_PYTHON_OPTS
```

Finally, activate your workspace and start up your notebook!
```bash
~ $ workon spark
(spark) ~ $ pyspark --master local[7]
```
*Note:* The command `pyspark --master local[7]` will start spark in standalone mode using 7 cores. My macbook pro has 8 cores so I picked `n-1`. It's probably best for you to do the same. ie. If you are running on a machine with 4 cores then set it to 3.

Here is the output that I get from this command:
```
[I 21:04:55.463 NotebookApp] Serving notebooks from local directory: /Users/cnicholls
[I 21:04:55.464 NotebookApp] 0 active kernels
[I 21:04:55.464 NotebookApp] The Jupyter Notebook is running at: http://localhost:8888/?token=e94f42d8b6617ce08d0de7b718b013943114e6a5dce24a13
[I 21:04:55.464 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 21:04:55.464 NotebookApp]

    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8888/?token=e94f42d8b6617ce08d0de7b718b013943114e6a5dce24a13
0:97: execution error: "http://localhost:8888/tree?token=8c03d3122d95a3bae5af9394417167a209aa1b246361e9af" doesn’t understand the “open location” message. (-1708)
```

I'm not sure why I get this error: `0:97: execution error: "http://localhost:8888/tree?token=8c03d3122d95a3bae5af9394417167a209aa1b246361e9af" doesn’t understand the “open location” message. (-1708)` but it doesn't seem to be a problem.

Now you can access localhost:8888 to use your notebooks.

[Here are a few notebooks](https://github.com/chrisnicholls/maritime-devcon-2017/tree/master/notebooks) that you can download and open in jupyter to get you started with spark.
