# HBase Setup

## Apache Pseudo distributed environment (or standalone)

https://qiita.com/mkyz08/items/b2eee09dd9fc9473dbb4

## Cloudera Single Node Cluster

https://github.com/YoshiyukiKono/SingleNodeCDPCluster

### Provisioning Cluster without CDSW
- Create a Centos 7 VM with at least 8 vCPUs/ 32 GB RAM. Choose the plain vanilla Centos image, not a cloudera-centos image.
- OS disk size: at least 50 GB.


```
sudo yum install -y git

git clone https://github.com/YoshiyukiKono/SingleNodeCDPCluster
cd SingleNodeCDPCluster
```

```
sudo su

./setup.sh aws templates/base.json
```
