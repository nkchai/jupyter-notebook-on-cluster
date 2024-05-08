# jupyter-notebook-on-cluster
# jupyter-notebook-on-cluster
#### Generate a Public-key crypto key pair

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

#### Connecting to the cluster via ssh

After your key is succesfully added to your account you need to do the following things to connect to the cluster:
* Connect the school VPN visit https://vpn.iit.edu and download the university VPN (cisco) software (watch out your will have to authenticate via your second factor)
* Connect via SSH
  * This is the example syntax
  * `ssh -i "~\.ssh\id_ed25519_spark_edge_key" hajek@system26.rice.iit.edu`

#### Adding Environment Variables
* Setup two environment variables in the `.bashrc` file. 
  * These credentials will be in a file named `cred.txt` located in your spark-edge server home directory. Use `cat creds.txt` to get the contents of the file.
  * Add these two values to the end of `.bashrc`
  * The `ACCESSKEY` is your hawk user name and the other one is your `SECRETKEY`

```bash
export SECRETKEY=
export ACCESSKEY=
```

* Source the the `.bashrc` file by typing type the command: `. ~/.bashrc`

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
* Add the following lines to the config file. Change the contents of the file to yours.
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

