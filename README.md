# Vagrant 2.2.16 RSA SHA1 Keypair Issue

This repo attempts to provide a reproduction script for the issues with
Vagrant 2.2.16's dropping support for RSA keys that use SHA1 signatures.

https://github.com/hashicorp/vagrant/issues/12344

It contains a script that creates RSA keys using three different methods:

1. `ssh-keygen -t rsa …`
1. `ssh-keygen -t rsa-sha2-512 …`
1. `aws ec2 create-key-pair …`

Using `OpenSSH_7.9p1, LibreSSL 2.7.3` both `rsa` and `rsa-sha2-512` failed
because Vagrant 2.2.16 notes that they're deprecated `SHA1` signature
keys. `aws ec2 create-key-pair` _succeeds_ but is not a tenable solution
for various common AWS workflows, including but not limited to use with
OpsWorks where managing the key is expected to happen outside of EC2.
Still, it's a decent workaround that could be used in conjunction with
`.ssh/config` as a stopgap.

## Setup

You'll need at least the following software installed. Testing was done at
the versions shown:

```
$ vagrant -v; ssh -V; timeout --version | head -n1; aws --version
Vagrant 2.2.16
OpenSSH_7.9p1, LibreSSL 2.7.3
timeout (GNU coreutils) 8.32
aws-cli/2.2.8 Python/3.9.5 Darwin/18.7.0 source/x86_64 prompt/off
```

You'll also need an AWS account with a public subnet and SSH accessible
security group. The script launches `t3.nano` instances so costs should be
minimal. That said: Caveat emptor.

Install `vagrant-aws` before running the script:

```
vagrant plugin install --local
```


## Usage

Run the script like the following:

```
./repro subnet-<redacted> sg-<redacted>
```

This will write a log file to `./repro.log` which _should_ be safe to gist
like:

```
gist -ocp repro.log <(grep -E 'vagrant.+(failed|succeeded)' repro.log)
```

Again please be sure to preview `./repro.log` _and_ the resulting gist to
be sure that it doesn't contain any sensitive material.

## Example From My Box

```
vagrant-2.2.16-sha1-bug-default-rsa failed to come up
vagrant-2.2.16-sha1-bug-explicit-rsa-sha2-512 failed to come up
vagrant-2.2.16-sha1-bug-aws-created succeeded
```

https://gist.github.com/timvisher/710f7e18b7a0da79e631cb6866893859
