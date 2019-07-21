```
차세대OSS사업Unit 김중호 09261
```

### PART1

### User training with a UID of 3800
```
sudo useradd training -u 3800
```

### Set the password for user “training” to “training”
```
sudo passwd training
```


### Create the group skcc and add training to it
```
sudo groupadd skcc
sudo gpasswd -a training skcc
```

### Give training sudo capabilities
```
sudo chmod 644 /etc/sudoers
sudo bash -c 'echo training ALL=NOPASSWD: ALL >> /etc/sudoers'
sudo chmod 440 /etc/sudoers
```


### add hostIP and domain name
```
sudo bash -c 'echo 172.31.45.0 cm.com cm >> /etc/hosts'
sudo bash -c 'echo 172.31.41.176 m1.com m1 >> /etc/hosts'
sudo bash -c 'echo 172.31.47.25 d1.com d1 >> /etc/hosts'
sudo bash -c 'echo 172.31.34.255 d2.com d2 >> /etc/hosts'
sudo bash -c 'echo 172.31.42.90 d3.com d3 >> /etc/hosts'
sudo hostnamectl set-hostname cm

getent hosts
```

### List the Linux release you are using
```
cat /etc/redhat-release
```

### List the file system capacity for the first node (master node)
```
df -Ph
```

### List the command and output for yum repolist enabled
```
sudo yum repolist enabled
```

### List the /etc/passwd entries for training (only in master name node)

### List the /etc/group entries for skcc (only in master name node)

### List output of the flowing commands:
1. getent group skcc
2. getent passwd training


### Install a MySQl server
```
sudo yum install -y mariadb-server
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

### mysql jdbc driver 설치
```
sudo wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
tar zxvf mysql-connector-java-5.1.46.tar.gz 
sudo mkdir -p /usr/share/java/ 
cd mysql-connector-java-5.1.46 
sudo cp mysql-connector-java-5.1.46-bin.jar /usr/share/java/mysql-connector-java.jar
```

### mariadb 권한설정..all Y enter 
```
sudo /usr/bin/mysql_secure_installation
```

### Create databases
```
cd ~
echo "CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;" >> create_database.sql
echo "GRANT ALL ON scm.* TO 'scm-user'@'%' IDENTIFIED BY 'password';" >> create_database.sql

echo "CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;"  >> create_database.sql
echo "GRANT ALL ON amon.* TO 'amon-user'@'%' IDENTIFIED BY 'password'; " >> create_database.sql
 
echo "CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;" >> create_database.sql 
echo "GRANT ALL ON rman.* TO 'rman-user'@'%' IDENTIFIED BY 'password'; " >> create_database.sql
 
echo "CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci; " >> create_database.sql
echo "GRANT ALL ON hue.* TO 'hue-user'@'%' IDENTIFIED BY 'password';" >> create_database.sql 
 
echo "CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;" >> create_database.sql 
echo "GRANT ALL ON metastore.* TO 'metastore-user'@'%' IDENTIFIED BY 'password'; " >> create_database.sql
 
echo "CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci; " >> create_database.sql
echo "GRANT ALL ON sentry.* TO 'sentry-user'@'%' IDENTIFIED BY 'password'; " >> create_database.sql
 
echo "CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci; " >> create_database.sql
echo "GRANT ALL ON oozie.* TO 'oozie-user'@'%' IDENTIFIED BY 'password';"  >> create_database.sql

echo "CREATE DATABASE hive DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci; " >> create_database.sql
echo "GRANT ALL ON hive.* TO 'hive-user'@'%' IDENTIFIED BY 'password';"  >> create_database.sql

echo "FLUSH PRIVILEGES;" >> create_database.sql
echo "SHOW DATABASES;" >> create_database.sql

mysql -u root -p < create_database.sql
```


### Install Cloudera Manager
### pre-setting
```
sudo sh -c "echo 'vm.swappiness=1'>> /etc/sysctl.conf" 
sudo sh -c "echo never > /sys/kernel/mm/transparent_hugepage/defrag" 
sudo sh -c "echo never > /sys/kernel/mm/transparent_hugepage/enabled" 
sudo sh -c "echo 'echo never > /sys/kernel/mm/transparent_hugepage/defrag' >> /etc/rc.local" 
sudo sh -c "echo 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' >> /etc/rc.local" 

sudo sed -i 's/^\(SELINUX\s*=\s*\).*$/\1disabled/' /etc/selinux/config getenforce 
```

### repository add
```
sudo yum install -y wget
sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
sudo sed -i 's/^baseurl=.*/baseurl=https:\/\/archive.cloudera.com\/cm5\/redhat\/7\/x86_64\/cm\/5.15.2\//' /etc/yum.repos.d/cloudera-manager.repo
sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera
```

### install cloudera server
```
sudo yum install -y cloudera-manager-daemons cloudera-manager-server
```


### ntp설정
```
sudo yum install -y ntp

sudo sed -i 's/^server 0.centos.pool.ntp.org/#server 0.centos.pool.ntp.org/' /etc/ntp.conf
sudo sed -i 's/^server 1.centos.pool.ntp.org/#server 1.centos.pool.ntp.org/' /etc/ntp.conf
sudo sed -i 's/^server 2.centos.pool.ntp.org/#server 2.centos.pool.ntp.org/' /etc/ntp.conf
sudo sed -i 's/^server 3.centos.pool.ntp.org/#server 3.centos.pool.ntp.org/' /etc/ntp.conf

sudo bash -c 'echo server kr.pool.ntp.org >> /etc/ntp.conf'
sudo bash -c 'echo server time.bora.net >> /etc/ntp.conf'
sudo bash -c 'echo server time.kornet.net >> /etc/ntp.conf'

sudo chkconfig ntpd on
sudo systemctl start ntpd
```

### sshd 설정
```
sudo sed -i 's/PermitRootLogin/#PermitRootLogin/' /etc/ssh/sshd_config
sudo bash -c 'echo PermitRootLogin yes >> /etc/ssh/sshd_config'
#sudo sed -i 's/^PermitRootLogin .*/PermitRootLogin yes/' /etc/ssh/sshd_config
sudo sed -i 's/^PasswordAuthentication .*/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd
```

### jdk 설치 및 path추가
```
sudo yum install -y oracle-j2sdk*

sudo sed -i 's/export PATH/#export PATH/' ~/.bash_profile
sudo bash -c 'echo JAVA_HOME=/usr/java/jdk1.7.0_67-cloudera >> ~/.bash_profile'
sudo bash -c 'echo export PATH=\$JAVA_HOME/bin:\$PATH >> ~/.bash_profile'
```

### scm db 설정 및 start cloudera scm server
```
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm-user password
sudo rm /etc/cloudera-scm-server/db.mgmt.properties 
sudo systemctl start cloudera-scm-server
```
### 



### PART2
### Create test database
```
CREATE DATABASE test DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;

GRANT ALL ON test.* TO 'training'@'%' IDENTIFIED BY 'training';
```



create external table authors
(
id int
,first_name string
,last_name string
,email string
,brithdate string
,added string
)
location '/home/authors/'
;

create table posts
(
id int
,author_id int
,title string
,description string
,content string
,date string
)
location '/home/posts/'
;


sqoop import \
  --dirver com.mysql.jdbc.Driver \
  --connect jdbc:mysql://cm.com/test/ \
  --username root \
  --password training \
  --table authors
  --target-dir /home/authors






part 2


p1

set hive.cli.print.header=true;
use problem1;

select a.id, a.type, a.status, a.amount
, a.amount - b.am_avg different
from account a
,(select type, avg(amount) am_avg
   from account
   where status = 'Closed'
   group by type
) b
where a.type = b.type
and a.status = 'Active'


limit 2
;

p2

CREATE TABLE employee
 (id INT
,fname STRING
,lname STRING
,address STRING
,city STRING
,state STRING
,zip STRING
,birthday STRING
,hireday STRING
)
  STORED AS PARQUET
location '/user/training/problem2/data/employee/'
;




p3.


use problem3;

create table solution
as
select id, fname, lname, hphone
from customer a
, account b
where a.id = b.custid
and b.amount > 0


p4?


p5

select fname || ',' || lname || ',' || zip
from customer
limit 2;


select concat(concat(concat(fname ,',') , concat(lname,',') ), zip) output
from customer
where city = 'Palo Alto'
and state = 'CA'

union all

select concat(concat(concat(fname ,',') , concat(lname,',') ), zip) output
from employee
where city = 'Palo Alto'
and state = 'CA'



p6

create table solution 
as
select id, fname, lname, address, city, state, zip
,substr(birthday,7) birthyear
from employee
;



p7
select concat(concat(lname,','),fname) from 
(
select fname, lname
 from employee a
where city = 'Seattle'
order by lname, fname
) a

limit 10;




p8
sqoop export \
  --connect jdbc:mysql://localhost/problem8 \
  --username cloudera \
  --password cloudera \
  --table solution \
  --export-dir /user/training/problem8/data/customer/



p9

create table solution
as
select concat('A',id) id
,fname ,lname ,address, city, state, zip, birthday
from customer
;


p10

create view solution
as
select 
a.id id
,a.fname fname
,a.lname lname
,a.state state
,a.zip zipcode
,b.charge charge
,b.tstamp billdate
from customer a, billing b
where a.id = b.id
and b.charge is not null
;

limit 2;
