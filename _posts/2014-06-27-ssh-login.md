Title: SSH Login Without Password
Date: 2014-06-27 13:20
Category: Coding
Tags: ssh
Slug: ssh-login

Suppose you want to access to Linux machine B from windows machine A. Here is the steps to setup passwordless ssh login.

## Generate public/private keys on local machine A

1. Use PuttyGEN to generate public/private keys and save them to the same directory on *machine A*. For the public key, copy the generated key from the textbox rather than clicking the save button (use `ssh-keygen -t dsa` to generate the keys in ~/.ssh if on linux)

2. Copy the generated public key to machine B

## Configure public key on the server B

```
mkdir ~/.ssh
cat public_key >> .ssh/authorized_keys
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
```

## Login from machine A without password

1. Use Putty to ssh to the server (with the private key loaded at Putty->SSH->Auth)


## Useful links

* http://manjeetdahiya.com/2011/03/03/passwordless-ssh-login/
* http://askubuntu.com/questions/204400/ssh-public-key-no-supported-authentication-methods-available-server-sent-publ