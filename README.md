# README

Setup freeipa on a centos machine and run commands using json api.

### Requirements

* VirtualBox
* Vagrant

### Setup

After installing virtual box and vagrant.

```
# add vagrant box
vagrant box add CentOS_6.4_x86_64 http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20130731.box

# clone repository
git clone git@github.com:theeban275/freeipa-server-test.git
cd freeipa-server-test

# start vm and ssh into vm
vagrant up
vagrant ssh

# setup hosts on vm
sudo vi /etc/hosts

# remove ipa-server* from first line
# add at end of file 192.168.33.20 ipa-server.example.org ipaserver

# install freeipa
sudo yum -y install ipa-server bind bind-dyndb-ldap
sudo ipa-server-install

# enter default values and appropriate passwords and enter yes to install
```

### Call JSON API

```
# first get a kerberos ticket
kinit admin

# run ipa user-find --all using using json api
curl -v  \
         -H referer:https://`hostname`/ipa  \
         -H "Content-Type:application/json" \
         -H "Accept:applicaton/json"\
         --negotiate -u : \
         --delegation always \
         --cacert /etc/ipa/ca.crt  \
         -d  '{"method":"user_find","params":[[""],{"all":"true"}],"id":0}' \
         -X POST       https://`hostname`/ipa/json
```

Note the return results use the ldap attributes in the result. To get the equivalent
results using the command line use.

```
ipa user-find --all --raw
```

Also if you want to know the mappings between command line options to json options use
the following.

```
ipa show-mappings user-find
```

### Useful links

* [API](https://git.fedorahosted.org/cgit/freeipa.git/tree/API.txt)

