# Install packages

## Install anaconda

```bash
# download anaconda with link from anaconda download page
wget https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh

# install anaconda
bash Anaconda3-2024.10-1-Linux-x86_64.sh

# By default anaconda update your shell profile to automatically initialize conda.
# This will activate conda on startup and change the command prompt when activated.

# [Optional]
# If you'd prefer that conda's base environment not be activated on startup,
# run the following command when conda is activated:
conda config --set auto_activate_base false

# [Optional]
# You can undo this by running
conda init --reverse $SHELL

# For changes to take effect, close and re-open your current shell.
# or run
source .bashrc
```

## Install docker

```bash
sudo apt-get update

sudo apt-get install docker.io
```

### Run docker without sudo

```bash
# see: https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md

# Add the docker group if it doesn't already exist
sudo groupadd docker

# Add the connected user $USER to the docker group
sudo gpasswd -a $USER docker

# IMPORTANT: Log out and log back in so that your group membership is re-evaluated.

# Restart the docker daemon
sudo service docker restart
```

## Install docker-compose

> [!TIP]
> Before installing docker-compose create a `~/bin` directory where you put in your executable files.

```bash
# Get download path from github
# https://github.com/docker/compose/releases/download/v2.33.1/docker-compose-linux-x86_64

# Download docker-compose with specifiying the output (-O)
wget https://github.com/docker/compose/releases/download/v2.33.1/docker-compose-linux-x86_64 -O ~/bin/docker-compose

# you now have ~/bin/docker-compose
# but your system does not know that it is an executable file
chmod +x bin/docker-compose

# Add the bin directory with docker-compose to PATH
# open ~/.bashrc file and add
export PATH="${HOME}/bin:${PATH}"

# restart your VM or run
source ~/.bashrc
```

## Install pgcli with conda

```bash
# install pgcli
conda install -c conda-forge pgcli

# connect to postgres db
pgcli -h localhost -U root -d ny_taxi
```

## Install terraform

```bash
# copy download link for AMD64
https://releases.hashicorp.com/terraform/1.11.1/terraform_1.11.1_linux_amd64.zip

# download
wget https://releases.hashicorp.com/terraform/1.11.1/terraform_1.11.1_linux_amd64.zip

# install unzip
sudo apt-get install unzip

# unzip and remove zip file
unzip terraform_1.11.1_linux_amd64.zip
rm terraform_1.11.1_linux_amd64.zip

# terraform is already a executable
# and ~/bin is already inside PATH
terraform version
```

## Install java, spark and pyspark

A very nice explanation on how to install spark is shown by [data-engineering-zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/05-batch/setup/linux.md).

Here we'll show you how to install Spark 3.3.2 for Linux.
We tested it on Ubuntu 20.04 (also WSL), but it should work
for other Linux distros as well

### Installing Java

Download OpenJDK 11 or Oracle JDK 11 (It's important that the version is 11 - spark requires 8 or 11)

We'll use [OpenJDK](https://jdk.java.net/archive/)

Download it (e.g. to `~/spark`):

```
wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz
```

Unpack it:

```bash
tar xzfv openjdk-11.0.2_linux-x64_bin.tar.gz
```

define `JAVA_HOME` and add it to `PATH`:

```bash
export JAVA_HOME="${HOME}/spark/jdk-11.0.2"
export PATH="${JAVA_HOME}/bin:${PATH}"
```

check that it works:

```bash
java --version
```

Output:

```
openjdk 11.0.2 2019-01-15
OpenJDK Runtime Environment 18.9 (build 11.0.2+9)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.2+9, mixed mode)
```

Remove the archive:

```bash
rm openjdk-11.0.2_linux-x64_bin.tar.gz
```

### Installing Spark

Download Spark. Use 3.3.2 version:

```bash
wget https://archive.apache.org/dist/spark/spark-3.3.2/spark-3.3.2-bin-hadoop3.tgz
```

Unpack:

```bash
tar xzfv spark-3.3.2-bin-hadoop3.tgz
```

Remove the archive:

```bash
rm spark-3.3.2-bin-hadoop3.tgz
```

Add it to `PATH`:

```bash
export SPARK_HOME="${HOME}/spark/spark-3.3.2-bin-hadoop3"
export PATH="${SPARK_HOME}/bin:${PATH}"
```

#### Testing Spark

Execute `spark-shell` and run the following:

```scala
val data = 1 to 10000
val distData = sc.parallelize(data)
distData.filter(_ < 10).collect()
```

### PySpark

It's the same for all platforms. Go to [pyspark.md](pyspark.md).

> [!NOTE]
> This document assumes you already have python.

To run PySpark, we first need to add it to `PYTHONPATH`:

```bash
export PYTHONPATH="${SPARK_HOME}/python/:$PYTHONPATH"
export PYTHONPATH="${SPARK_HOME}/python/lib/py4j-0.10.9.5-src.zip:$PYTHONPATH"
```

Make sure that the version under `${SPARK_HOME}/python/lib/` matches the filename of py4j or you will
encounter `ModuleNotFoundError: No module named 'py4j'` while executing `import pyspark`.

For example, if the file under `${SPARK_HOME}/python/lib/` is `py4j-0.10.9.3-src.zip`, then the
`export PYTHONPATH` statement above should be changed to

```bash
export PYTHONPATH="${SPARK_HOME}/python/lib/py4j-0.10.9.3-src.zip:$PYTHONPATH"
```

On Windows, you may have to do path conversion from unix-style to windows-style:

```bash
SPARK_WIN=`cygpath -w ${SPARK_HOME}`

export PYTHONPATH="${SPARK_WIN}\\python\\"
export PYTHONPATH="${SPARK_WIN}\\python\\lib\\py4j-0.10.9-src.zip;$PYTHONPATH"
```

Now you can run Jupyter or IPython to test if things work. Go to some other directory, e.g. `~/tmp`.

Download a CSV file that we'll use for testing:

```bash
wget https://d37ci6vzurychx.cloudfront.net/misc/taxi_zone_lookup.csv
```

Now let's run `ipython` (or `jupyter notebook`) and execute:

```python
import pyspark
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .master("local[*]") \
    .appName('test') \
    .getOrCreate()

df = spark.read \
    .option("header", "true") \
    .csv('taxi_zone_lookup.csv')

df.show()
```

Test that writing works as well:

```python
df.write.parquet('zones')
```

### Add to ~/.bashrc file

In order to not have to type following commands every time you enter your environment, we place those environment variables inside the `~/.bashrc` file. Afterwards run `source ~/.bashrc` to activate.

```bash
export JAVA_HOME=${HOME}/spark/jdk-11.0.2
export PATH=${JAVA_HOME}/bin:${PATH}

export SPARK_HOME=${HOME}/spark/spark-3.3.2-bin-hadoop3
export PATH=${SPARK_HOME}/bin:${PATH}

# In order to tell python from where to install packages
export PYTHONPATH="${SPARK_HOME}/python/:${PYTHONPATH}"
export PYTHONPATH="${SPARK_HOME}/python/lib/py4j-0.10.9.5-src.zip:${PYTHONPATH}"
```
