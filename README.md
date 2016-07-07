Installation :

https://docs.mongodb.com/manual/tutorial/install-mongodb-on-amazon/
https://docs.mongodb.com/ecosystem/platforms/amazon-ec2/

sudo –i

vi /etc/yum.repos.d/mongodb-org-2.6.repo

Mettre ça dans le fichier :

[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2013.03/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc

sudo yum install -y mongodb-org

mkdir /data /log /journal

mkfs.ext4 /dev/sdb
mkfs.ext4 /dev/sdc
mkfs.ext4 /dev/sdd

echo '/dev/sdb /data ext4 defaults,auto,noatime,noexec 0 0
/dev/sdc /journal ext4 defaults,auto,noatime,noexec 0 0
/dev/sdd /log ext4 defaults,auto,noatime,noexec 0 0' | sudo tee -a /etc/fstab

mount /data
mount /journal
mount /log

chown mongod:mongod /data /journal /log

ln -s /journal /data/journal

vi /etc/mongod.conf

Mettre ça dedans :
dbpath = /data
logpath = /log/mongod.log

sudo chkconfig mongod off

vi /etc/security/limits.conf

Mettre dedans :
* soft nofile 64000
* hard nofile 64000
* soft nproc 32000
* hard nproc 32000

vi /etc/security/limits.d/90-nproc.conf

Mettre dedans :
* soft nproc 32000
* hard nproc 32000

blockdev --setra 32 /dev/xvda
echo 'ACTION=="add", KERNEL=="xvda", ATTR{bdi/read_ahead_kb}="16"' | sudo tee -a /etc/udev/rules.d/85-ebs.rules

blockdev --setra 32 /dev/sdb
echo 'ACTION=="add", KERNEL=="sdb", ATTR{bdi/read_ahead_kb}="16"' | sudo tee -a /etc/udev/rules.d/85-ebs.rules

sudo blockdev --setra 32 /dev/sdc
echo 'ACTION=="add", KERNEL=="sdc", ATTR{bdi/read_ahead_kb}="16"' | sudo tee -a /etc/udev/rules.d/85-ebs.rules

sudo blockdev --setra 32 /dev/sdd
echo 'ACTION=="add", KERNEL=="sdd", ATTR{bdi/read_ahead_kb}="16"' | sudo tee -a /etc/udev/rules.d/85-ebs.rules

service mongod start

Mongo
