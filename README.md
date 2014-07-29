# ANSIBLE

## Ansible
[http://julien.ponge.org/blog/scalable-and-understandable-provisioning-with-ansible-and-vagrant/](http://julien.ponge.org/blog/scalable-and-understandable-provisioning-with-ansible-and-vagrant/)

Install specific software such as
  * mysql
  * pyenv
  * nvm
  * rvm

Since `.bashrc` only works for interactive shell, make sure that you insert your lines at the beginning of this file. But maybe it's better to not rely on this behavior

### Run ansible for single machine
    ansible-playbook --limit imac-2.local user.yml

## DOCKER
[Vagrant, Docker and Ansible](http://devo.ps/blog/2013/09/25/vagrant-docker-and-ansible-wtf.html)

[SETUP DOCKER ON OSX](http://blog.javabien.net/2014/03/03/setup-docker-on-osx-the-no-brainer-way/)

### Remove all docker container
    docker stop $(docker ps -a -q)
    docker rm $(docker ps -a -q)


## My ansible playbooks.

### SETUP MACHINE
On the target Linux machine, do the following steps

1. Install `build-essential`, `zlib1g-dev`, `openssl` and `curl`

    sudo apt-get install build-essential zlib1g-dev openssl curl libz-dev libreadline-dev libncursesw5-dev libssl-dev libgdbm-dev libsqlite3-dev libbz2-dev python-apt

2. Install Pyenv
3. Install Ansible

    sudo su
    export PATH=[pyenv_shim]:$PATH
    pip install ansible

Then go to `/home/$USER/.pyenv/versions/2.7.5/bin/`, and chown the ansible file to current user

4. Copy ssh-key to machine

### Usage

``` yaml
---
  - hosts: webservers

  - include: python/apt.yml
  - include: apt/dotdeb.yml
    vars:
      with_php54: false

  - include: iptables/persistent.yml
    vars:
      iptables_file_rules: ../../an_iptables_file_rules

  - include: python/mysqldb.yml
  - include: mysql/mysql.yml
    vars:
      mysql_root_password: TheUserRootPassword
      mysqld:
        bind_address: 127.0.0.1
        key_buffer: 16M
        max_connection: 100
        # skip archive storage engine
        skip-archive: ~
        # you can add other key/value

  - include: php5/fpm.yml
    vars:
      delete_default_pool: true
  - include: php5/fpm-pool.yml
    vars:
      name: foo
      listen: /var/run/php5-fpm-foo.sock
      pm_process_idle_timeout: 20s
      pm_max_requests: 100
      php_flag:
        display_errors: off
        # you can add other key/value
      php_admin_flag:
        log_errors: on
        # you can add other key/value
      php_admin_value:
        memory_limit: 64M
        # you can add other key/value
  - include: php5/fpm-pool.yml
    vars:
      name: bar
      listen: /var/run/php5-fpm-bar.sock

  - include: nginx/nginx.yml
    vars:
      delete_default_vhost: true
  - include: nginx/vhost-redirect.yml
    vars:
      name: redirect_$server_name
      listen: "*:80"
      server_name: example.org
      redirect_url: http://www.$server_name

  - include: memcached/memcached.yml
    vars:
      listen: 127.0.0.1
      listen: 11211
      memory: 64
      max_connections: 1024

  - include: swap/swappiness.yml
    vars:
      swappiness: 60
```
