# School Site Test VM

A Vagrant virtual machine that provisions and configures

## Requirements

### Ansible

Ansible roles are listed in ansible/requirements.yml and can be installed using
`ansible-galaxy`.  Roles can be installed at the host-level or bundled into your
local working copy of the repository. If you choose the latter option, add an
empty ansible/roles folder and an ansible.cfg file at ansible/ansible.cfg.

Your ansible.cfg file should look like:

```
[defaults]

roles_path = <your path>/schoolsite-test-vm/ansible/roles
```

Install the roles with:

```
$ ansible-galaxy install -r ansible/requirements.yml
```

You may need a *sudo* on the front or a *--force* on the end.

## Setup

### Hosts file

The ansible playbook will configure apache to serve school sites on subdomains
of schools.dev, but your OS does not know that it should send those requests to
the VM. You can remedy this with a proxy that runs on your host machine, but I
didn't like that idea, so I added all this to my /etc/hosts file:

```
192.168.33.2 schools.dev
192.168.33.2 aes.schools.dev
192.168.33.2 ahs.schools.dev
192.168.33.2 arl.schools.dev
192.168.33.2 bbes.schools.dev
192.168.33.2 bbms.schools.dev
192.168.33.2 bmms.schools.dev
192.168.33.2 bpes.schools.dev
192.168.33.2 bses.schools.dev
192.168.33.2 bwes.schools.dev
192.168.33.2 cces.schools.dev
192.168.33.2 ces.schools.dev
192.168.33.2 chs.schools.dev
192.168.33.2 cles.schools.dev
192.168.33.2 cls.schools.dev
192.168.33.2 cms.schools.dev
192.168.33.2 cres.schools.dev
192.168.33.2 dles.schools.dev
192.168.33.2 dms.schools.dev
192.168.33.2 does.schools.dev
192.168.33.2 dres.schools.dev
192.168.33.2 ees.schools.dev
192.168.33.2 elms.schools.dev
192.168.33.2 emms.schools.dev
192.168.33.2 fes.schools.dev
192.168.33.2 fqms.schools.dev
192.168.33.2 fres.schools.dev
192.168.33.2 gces.schools.dev
192.168.33.2 ges.schools.dev
192.168.33.2 ghs.schools.dev
192.168.33.2 gms.schools.dev
192.168.33.2 hc.schools.dev
192.168.33.2 hcasc.schools.dev
192.168.33.2 hcms.schools.dev
192.168.33.2 hes.schools.dev
192.168.33.2 hhs.schools.dev
192.168.33.2 hms.schools.dev
192.168.33.2 hohs.schools.dev
192.168.33.2 hses.schools.dev
192.168.33.2 ies.schools.dev
192.168.33.2 jhes.schools.dev
192.168.33.2 lems.schools.dev
192.168.33.2 les.schools.dev
192.168.33.2 lfes.schools.dev
192.168.33.2 lkms.schools.dev
192.168.33.2 lrhs.schools.dev
192.168.33.2 lwes.schools.dev
192.168.33.2 mhhs.schools.dev
192.168.33.2 mhms.schools.dev
192.168.33.2 mrhs.schools.dev
192.168.33.2 mvms.schools.dev
192.168.33.2 mwes.schools.dev
192.168.33.2 mwms.schools.dev
192.168.33.2 nes.schools.dev
192.168.33.2 nto.schools.dev
192.168.33.2 omhs.schools.dev
192.168.33.2 omms.schools.dev
192.168.33.2 ples.schools.dev
192.168.33.2 pms.schools.dev
192.168.33.2 pres.schools.dev
192.168.33.2 pvms.schools.dev
192.168.33.2 rbes.schools.dev
192.168.33.2 res.schools.dev
192.168.33.2 rhhs.schools.dev
192.168.33.2 rhs.schools.dev
192.168.33.2 sch.schools.dev
192.168.33.2 ses.schools.dev
192.168.33.2 sfes.schools.dev
192.168.33.2 sjles.schools.dev
192.168.33.2 thes.schools.dev
192.168.33.2 tres.schools.dev
192.168.33.2 tses.schools.dev
192.168.33.2 tvms.schools.dev
192.168.33.2 ves.schools.dev
192.168.33.2 wates.schools.dev
192.168.33.2 waves.schools.dev
192.168.33.2 wes.schools.dev
192.168.33.2 wfes.schools.dev
192.168.33.2 wlhs.schools.dev
192.168.33.2 wlms.schools.dev
```

This list can be generated by executing the [hosts_example.py](#hosts_examplepy)
utility on the guest machine so you can easily copy and paste it to your hosts
file.

### Databases

All the database exports should be placed in the database/ directory with this
naming convention:

```
<school-code>.bak.sql.gz
```

### Drupal installation folders

The source files of the Drupal sites should be placed in the data/ directory and
should be named after the school code:

```
data/
  aes/
  ahs/
  etc...
```

### Run it

Then you should be able to:

```
$ vagrant up
```

## Usage

If you are going active development on a theme of module, you can place it in
extensions/modules or extensions/themes. After provisioning the vagrant box,
you can place the module or theme in it's installation location with a symbolic
link like this:

```
$ ln -s \
    /vagrant/extensions/modules/my_module \
    /var/www/schools/bses/sites/all/modules/my_module
```

## Utilities

The utilities folder contains scripts that help perform various tedious tasks.

### process_backup.py

This is a python script that organizes a backup folder which is organized like
this:

```
/backup_location/
  es/
    site_es_aes-2016-01-15.bak.sql.gz
    site_es_aes-2016-01-15.tar.gz
  hs/
    site_hs_ahs-2016-01-15.bak.sql.gz
    site_hs_ahs-2016-01-15.tar.gz
```

And makes it like this:

```
/destination/
  data/
    aes/
      <drupal-files>
    ahs/
      <drupal-files>
  database/
    aes.bak.sql.gz
    ahs.bak.sql.gz
```

Which is what ansible will expect. Here's an example:

```
$ ./utilities/process_backup.py -b ~/Downloads/2016-01-15/ -d ~/Sites/schoolsite-test-vm/
```

### reset_school.py

This is a script that is meant to be run from the guest. It resets a school to
it's original state when it was imported:

```
$ /vagrant/utilities/reset_school.py bses
```

### hosts_example.py

Generates example entries for your /etc/hosts file. This should be run on the
guest and it uses the actual vhost entries to generate the list.

```
$ /vagrant/utilities/hosts_example.py
```

### batch_command.py

Iterates over the school installations and performs the command on all of them.
For instance, this would clear caches on all schools:

```
$ /vagrant/utilities/batch_command.py "drush cc all"
```

You can also use tokens *target* and *school_code* to substitute the target 
directory. For instance, this will add a module you are developing to all 
schools as a symbolic link:

```
$ /vagrant/utilities/batch_command.py "ln -sfn /vagrant/extensions/modules/mymodule {target}/sites/all/modules/mymodule"
```

And this will revert all schools to thier original state:

```
$ /vagrant/utilities/batch_command.py "/vagrant/utilities/reset_school.py {school_code}"
```
