# jupyter-notebook-on-cluster
### Index

  - [Generate a Public-key crypto key pair](#generate-a-public-key-crypto-key-pair)
- [Connecting to the cluster via ssh](#connecting-to-the-cluster-via-ssh)
- [Cloning your Github Repo](#cloning-your-github-repo)
- [Your Credentials](#your-credentials)
- [Creating and Accessing a Jupyter Notebook](#creating-and-accessing-a-jupyter-notebook)
- [Installing Python libraries](#installing-python-libraries)


### Generate a Public-key crypto key pair

We will be using the `ssh-keygen` command to generate your key pair. The command is the same on all platforms and will generate two keys for us, a **public key** and a **private key**. From PowerShell or the Mac or Linux Terminal run the following command:

```bash
ssh-keygen -t ed25519
```

A few things to watch out for, remember that Windows is **NOT** case sensitive, but Mac and Linux are case sensitive. Meaning of Mac, Git and git are not the same values, but on Windows they are.

```bash
# Note on Windows the divider: "\" is different than Mac and Linux 
# Mac and Linux use the "/"
# This example is on a Windows system
# The username on Windows in this case is: palad
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\palad/.ssh/id_ed25519):
# Mac example would look like this
# The username on the Mac in this case is: palad
Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/palad/.ssh/id_ed25519):
```

The value in the parenthesis is the default value. If you were to hit the "ENTER" key it would place your Public and Private Key files in the directory and name the file accordingly. Lets modify this entry so you can identify each key pair and its purpose later.

At the prompt we will type the following and it will look like this:

```bash
# Remember, No spaces in the file name!
# Windows
Enter file in which to save the key ...: C:\Users\palad/.ssh/id_ed25519_340_github_key
# Mac\Linux
Enter file in which to save the key ...: /Users/palad/.ssh/id_ed25519_340_github_key
```

The next prompt will ask you to enter a **passphrase**, this is an additional password that can be attached to a private key and would be required to use the private key for authentication. This can be a good idea as it is an extra layer of security against physical theft of your private key file. But in the use case we are working we are going to opt not to use it and handle security in a different method. In this case you can hit the "ENTER" key twice and it will not add a passphrase to your private key.

```bash
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

You will now see some output similar to this:

```bash
Your identification has been saved in c:\Users\palad/.ssh/id_ed25519_340_github_key.
Your public key has been saved in c:\Users\palad/.ssh/id_ed25519_340_github_key.pub.
The key fingerprint is:
SHA256:3LR4sEpKQgbA6LT7yOP54QcAUJ5/BaDEY/zgo3YWrOA palad@lenovo-laptop
The key's randomart image is:
+--[ED25519 256]--+
|O+o ...          |
|o**o   .         |
|+oO+    o .      |
|.*o+.  o * .     |
|o.=.+ o S +      |
|.E * + . .       |
|o =.o .          |
| +o...           |
|.ooo.            |
+----[SHA256]-----+
```

We are interested in the first two lines of the output. They show the file location of the public and private key. The key fingerprint is not the content of the key. By default all operating systems, Windows, Mac, and Linux store key pairs in a hidden directory in your home directory called: `.ssh`.

```bash
# Location of private and public keys -- note the public key has a .pub extension
# The private key has no default extension
Your identification has been saved in c:\Users\palad/.ssh/id_ed25519_340_github_key.
Your public key has been saved in c:\Users\palad/.ssh/id_ed25519_340_github_key.pub
```

In order to make secure ssh connections to the cluster we are going to need to add the content of the .pub Public key into your spark edge server account. Send the contents of your public key file:

```bash
# display the content of the .pub file -- note the location of the file
# On Windows:
type c:\Users\palad/.ssh/id_ed25519_340_github_key.pub
# On Mac/Linux
cat /Users/palad/.ssh/id_ed25519_340_github_key.pub
# You will receive output similar to this
# Don't worry the public key is meant to be openly distributed
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILOgJFa4p2bLzbiqSfin87zzrFC29vULvMXd+MrwHbL0
palad@lenovo-laptop
```

### Connecting to the cluster via ssh

After your key is succesfully added to your account you need to do the following things to connect to the cluster:
* Connect the school VPN visit https://vpn.iit.edu and download the university VPN (cisco) software (watch out your will have to authenticate via your second factor).
* You need to be on the VPN to access any resources related to the cluster.
* Connect via SSH
  * This is the example syntax
  * `ssh -i "~\.ssh\id_ed25519_spark_edge_key" hajek@system26.rice.iit.edu`

#### Cloning your Github Repo
* Follow the same steps as [here](#generate-a-public-key-crypto-key-pair) to generate a public key pair inside your cluster.

* Add the contents of the public key to your github account. 
* Create a `config` file. Type the following commands to create a config file.

```bash
# This command changes your directory to the correct location
cd ~/.ssh
# This command uses Vim to create a file named: config
vi config
```
* Add the following lines to the config file. Change the contents of the file to yours and save it.
```bash
# Comments are allowed by using the # sign
# This line tells SSH to use these values whenever a connection is made
# to github.com
Host github.com
  # This is the username or ID you created for GitHub
  User jhajek
  Hostname github.com
  # This command tells SSH which Private key to use when making an SSH 
  # connection to GitHub
  # change the path from my username to yours
  IdentityFile C:/Users/palad/.ssh/id_ed25519_github_key
```
* Clone your repository via ssh using `git clone`.

For more information on cloning your repo [click here](https://github.com/illinoistech-itm/jhajek/tree/master/itmd-521/git-tutorial).

### Your Credentials
The credentials for you S3 bucket will be in a file named `creds.txt `located in your spark-edge server home directory. Issue the below command in your home directory to view your credentials.
```
cat creds.txt
```
go to `http://system54.rice.iit.edu` and use the same credentials to view your bucket.

### Creating and Accessing a Jupyter Notebook

#### Method 1:

#### Creating a Properties.conf file

Create a file named `properties.conf`, Copy the below lines of code into the file, add your `ACCESSKEY` and `SECRETKEY` and push it to your github repo

```
spark.jars.packages org.apache.hadoop:hadoop-aws:3.3.0
spark.hadoop.fs.s3a.aws.credentials.provider org.apache.hadoop.fs.s3a.SimpleAWSCredentialsProvider
spark.hadoop.fs.s3a.access.key ('your ACCESSKEY from creds.txt)
spark.hadoop.fs.s3a.secret.key ('your SECRETKEY from creds.txt)
spark.hadoop.fs.s3a.path.style.access true
spark.hadoop.fs.s3a.impl org.apache.hadoop.fs.s3a.S3AFileSystem 
spark.hadoop.fs.s3a.committer.magic.enabled true
spark.hadoop.fs.s3a.committer.name magic
spark.hadoop.fs.s3a.endpoint http://system54.rice.iit.edu

```
Note: Exclude `()` when modifying the file for the ACCESSKEY and SECRETKEY in the above file. It is not recommended to remove any of the above configurations as they help to access your S3 bucket.

You can modify this file as required to add more configurations to your spark session.

The below command will create a spark session with the configurations in your properties.conf and attaches that session to a Jupyter Notebook.

```
pyspark --master spark://sm.service.consul:7077 --packages org.apache.hadoop:hadoop-aws:3.2.3 --properties-file path-to-your-properties.conf
```
Modify the command to include the path of your `properties.conf` present in you repo.

Note: It is recommended that you run the above command from your home directory as the file access in the jupyter notebook is restricted to the parent folder where you ran the command.

Copy and paste the URL generated, the one with `192.168.172.26`  after you ran the above command in a web browser to access the notebook.

Go to `http://system76.rice.iit.edu/` to access the spark web UI

use `ctrl+c` in the terminal to stop the jupyter notebook.

#### Method 2:

Run the below command to create a spark session
```
pyspark --master spark://sm.service.consul:7077 --packages org.apache.hadoop:hadoop-aws:3.2.3
```
The above command will create a spark session with packages required to connect to your S3 bucket.

Now, Copy and paste the URL generated, the one with `192.168.172.26`  after you ran the above command in a web browser to access the notebook.

After opening Jupyter notebook. In a new cell run the `spark.stop()` command. This ensures that the session you create above will be terminated.

In a new cell, copy and paste the following code and run it:

```
from pyspark import SparkConf
from pyspark.sql import SparkSession
from pyspark.sql.types import IntegerType
from pyspark.sql.functions import to_date

# Removing hard coded password - using os module to import them
import os
import sys

conf = SparkConf()
conf.set('spark.jars.packages', 'org.apache.hadoop:hadoop-aws:3.3.0')
conf.set('spark.hadoop.fs.s3a.aws.credentials.provider', 'org.apache.hadoop.fs.s3a.SimpleAWSCredentialsProvider')

conf.set('spark.hadoop.fs.s3a.access.key', os.getenv('ACCESSKEY'))
conf.set('spark.hadoop.fs.s3a.secret.key', os.getenv('SECRETKEY'))
# Configure these settings
# https://medium.com/@dineshvarma.guduru/reading-and-writing-data-from-to-minio-using-spark-8371aefa96d2
conf.set("spark.hadoop.fs.s3a.path.style.access", "true")
conf.set("spark.hadoop.fs.s3a.impl", "org.apache.hadoop.fs.s3a.S3AFileSystem")
# https://github.com/minio/training/blob/main/spark/taxi-data-writes.py
# https://spot.io/blog/improve-apache-spark-performance-with-the-s3-magic-committer/
conf.set('spark.hadoop.fs.s3a.committer.magic.enabled','true')
conf.set('spark.hadoop.fs.s3a.committer.name','magic')
# Internal IP for S3 cluster proxy
conf.set("spark.hadoop.fs.s3a.endpoint", "http://infra-minio-proxy-vm0.service.consul")

spark = SparkSession.builder.appName("place your app name here ").config('spark.driver.host','spark-edge.service.consul').config(conf=conf).getOrCreate()

```

The above code will create a new spark session with the configurations required to connect to S3 bucket. You can add your own configurations as need with `conf.set()`.

Note: If you are using any configurations from external jars, you can do so by passing those jars with `--packages` flag in the pyspark submit command. You can use those configurations from these packages even after `spark.stop()`.

Go to `http://system76.rice.iit.edu/` to access the spark web UI

use `ctrl+c` in the terminal to stop the jupyter notebook.

### Installing Python libraries

The jupyter notebook generated is like any other jupyter notebook, you can install any extenal libraries with `pip`. Example:

```
pip install pandas
```
Note: Only spark jobs will be sent to the spark cluster, any code other than spark will be run on local compute.