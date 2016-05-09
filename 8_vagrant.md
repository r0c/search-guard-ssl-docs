# Vagrant demo

To start the vagrant demo do the following:

* Install vagrant and virtual box (if not already installed)
* vagrant plugin install vagrant-hosts (if not already installed)
* vagrant up es1
* vagrant up es2
* vagrant up es3
* You can connect to the cluster via "curl -Ss --insecure https://10.0.3.111:9200/_cluster/health\?pretty"