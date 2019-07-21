
# 차세대OSS사업Unit 김중호 09261


---------------
PART1
---------------

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
![1](https://user-images.githubusercontent.com/6895482/61592851-6deb3d00-ac13-11e9-83cd-4225babd757d.PNG)


### Give training sudo capabilities
```
sudo chmod 644 /etc/sudoers
sudo bash -c 'echo training ALL=NOPASSWD: ALL >> /etc/sudoers'
sudo chmod 440 /etc/sudoers
```
![2](https://user-images.githubusercontent.com/6895482/61592855-7e9bb300-ac13-11e9-9a04-8aaca7fe0da8.PNG)
![3](https://user-images.githubusercontent.com/6895482/61592861-8f4c2900-ac13-11e9-9f49-8b0857c6ac9f.PNG)


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
![hosts](https://user-images.githubusercontent.com/6895482/61593111-62e5dc00-ac16-11e9-99d9-3a5d523418b8.PNG)


### List the Linux release you are using
```
cat /etc/redhat-release
```
![5](https://user-images.githubusercontent.com/6895482/61592870-b0ad1500-ac13-11e9-9d7e-61541f3ad737.PNG)


### List the file system capacity for the first node (master node)
```
df -Ph
```
![6](https://user-images.githubusercontent.com/6895482/61592886-cde1e380-ac13-11e9-8d72-d4a0a62b3851.PNG)

### List the command and output for yum repolist enabled
```
sudo yum repolist enabled
```
![7](https://user-images.githubusercontent.com/6895482/61592891-d89c7880-ac13-11e9-9c35-209d3d14ac5e.PNG)


### List the /etc/passwd entries for training (only in master name node)
![8](https://user-images.githubusercontent.com/6895482/61592894-e3570d80-ac13-11e9-9567-0de0d4694c11.PNG)

### List the /etc/group entries for skcc (only in master name node)
![9](https://user-images.githubusercontent.com/6895482/61592897-eb16b200-ac13-11e9-875d-01786a698d4e.PNG)


### List output of the flowing commands:
#### getent group skcc
![10](https://user-images.githubusercontent.com/6895482/61592907-02ee3600-ac14-11e9-84f7-a05aa3e31a41.PNG)
#### getent passwd training
![11](https://user-images.githubusercontent.com/6895482/61592910-08e41700-ac14-11e9-9805-8839da2e4791.PNG)


### Install a MySQl server
```
sudo yum install -y mariadb-server
sudo systemctl enable mariadb
sudo systemctl start mariadb
```
![12](https://user-images.githubusercontent.com/6895482/61592916-14cfd900-ac14-11e9-9ed7-b263ae6ac2e3.PNG)

### show mysql version
```
mysql --version
```
![14](https://user-images.githubusercontent.com/6895482/61592921-2dd88a00-ac14-11e9-8bd0-ab4d8e9b59da.PNG)



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
![13](https://user-images.githubusercontent.com/6895482/61592919-21543180-ac14-11e9-91ec-0e6289e9ac38.PNG)


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
![16](https://user-images.githubusercontent.com/6895482/61592941-61b3af80-ac14-11e9-9184-29f535f29dfe.PNG)

### install cloudera server
```
sudo yum install -y cloudera-manager-daemons cloudera-manager-server
```
![15](https://user-images.githubusercontent.com/6895482/61592933-49dc2b80-ac14-11e9-936e-5768066994ba.PNG)



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
![17](https://user-images.githubusercontent.com/6895482/61592957-8740b900-ac14-11e9-83e8-80a4109af67b.PNG)


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
![18](https://user-images.githubusercontent.com/6895482/61592963-9cb5e300-ac14-11e9-9ca9-2eab06f21176.PNG)


### scm db 설정 및 start cloudera scm server
```
sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm-user password
sudo rm /etc/cloudera-scm-server/db.mgmt.properties 
sudo systemctl start cloudera-scm-server
```
![19](https://user-images.githubusercontent.com/6895482/61592969-a93a3b80-ac14-11e9-8bb6-69863fddfbfa.PNG)


### check cloudera hosts IP
![20](https://user-images.githubusercontent.com/6895482/61592980-cd961800-ac14-11e9-8793-5f031cb1f137.PNG)

### check greenlight CM main page
![cm server main](https://user-images.githubusercontent.com/6895482/61593008-1e0d7580-ac15-11e9-8e76-cd6fd5425da5.PNG)

### Create test database
```
CREATE DATABASE test DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;

GRANT ALL ON test.* TO 'training'@'%' IDENTIFIED BY 'training';
```
![23](https://user-images.githubusercontent.com/6895482/61593030-557c2200-ac15-11e9-916c-7c6231d018a4.PNG)

### create authors tables and posts tables
![21](https://user-images.githubusercontent.com/6895482/61593043-78a6d180-ac15-11e9-8ad8-cb4f889e7cd4.PNG)
![22](https://user-images.githubusercontent.com/6895482/61593045-7a709500-ac15-11e9-9acc-55e8f30f05f0.PNG)


### Extract tables authors and posts from the database and create Hive tables.
#### Create authors and posts directories in your HDFS home directory
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
```

### Use Sqoop to import the data from authors and posts
```
sqoop import \
--connect jdbc:mysql://cm.com/test \
--username training \
--password training \
--table posts \
--fields-terminated-by "\t" \
--target-dir /user/training/posts
```
```
sqoop import \
--connect jdbc:mysql://cm.com/test \
--username training \
--password training \
--table authors \
--fields-terminated-by "\t" \
--target-dir /user/training/authors
```

.
.
.


---------------
PART2
---------------



### PROBLEM1

```
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
```
![part2 p1](https://user-images.githubusercontent.com/6895482/61593120-97599800-ac16-11e9-90f7-3395c549053a.PNG)



### PROBLEM2 
```
USE PROBLEM2;

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
```
![part2 p2](https://user-images.githubusercontent.com/6895482/61593121-97599800-ac16-11e9-99a6-a25abe27374e.PNG)
![part2 p2_2](https://user-images.githubusercontent.com/6895482/61593122-97f22e80-ac16-11e9-82ea-11fdd25262ab.PNG)


### PROBLEM3
```
use problem3;

create table solution
as
select id, fname, lname, hphone
from customer a
, account b
where a.id = b.custid
and b.amount > 0
```
![part2 p3](https://user-images.githubusercontent.com/6895482/61593123-97f22e80-ac16-11e9-9c53-043952d81946.PNG)
![part2 p3_2](https://user-images.githubusercontent.com/6895482/61593124-97f22e80-ac16-11e9-87c0-2c553701d362.PNG)


### PROBLEM4
PASS

### PROBLEM5
```
USE PROBLEM5; 

select concat(concat(concat(fname ,',') , concat(lname,',') ), zip) output
from customer
where city = 'Palo Alto'
and state = 'CA'

union all

select concat(concat(concat(fname ,',') , concat(lname,',') ), zip) output
from employee
where city = 'Palo Alto'
and state = 'CA'
```
![part2 p5](https://user-images.githubusercontent.com/6895482/61593125-97f22e80-ac16-11e9-9af5-e6c913a09045.PNG)
![part2 p5_2](https://user-images.githubusercontent.com/6895482/61593126-988ac500-ac16-11e9-9e27-a2e8975531e5.PNG)


### PROBLEM6
```
USE PROBLEM6; 
create table solution 
as
select id, fname, lname, address, city, state, zip
,substr(birthday,7) birthyear
from employee
;
```
![part2 p6](https://user-images.githubusercontent.com/6895482/61593127-988ac500-ac16-11e9-8c76-74c59e5f5351.PNG)
![part2 p6_2](https://user-images.githubusercontent.com/6895482/61593128-988ac500-ac16-11e9-8f7a-2bfdec946199.PNG)


### PROBLEM7
```
USE PROBLEM7; 
select concat(concat(lname,','),fname) from 
(
select fname, lname
 from employee a
where city = 'Seattle'
order by lname, fname
) a
```
![part2 p7](https://user-images.githubusercontent.com/6895482/61593129-99235b80-ac16-11e9-8165-b11726eb5c17.PNG)
![part2 p7_2](https://user-images.githubusercontent.com/6895482/61593130-99235b80-ac16-11e9-8db7-f86aca605454.PNG)



### PROBLEM8
```
sqoop export \
  --connect jdbc:mysql://localhost/problem8 \
  --username cloudera \
  --password cloudera \
  --table solution \
  --fields-terminated-by '\t' \
  --export-dir /user/training/problem8/data/customer/
```

### PROBLEM9
```
USE PROBLEM9; 
create table solution
as
select concat('A',id) id
,fname ,lname ,address, city, state, zip, birthday
from customer
;
```
![part2 p9](https://user-images.githubusercontent.com/6895482/61593132-99235b80-ac16-11e9-9fd9-dc6a57292805.PNG)
![part2 p9_2](https://user-images.githubusercontent.com/6895482/61593133-99bbf200-ac16-11e9-92b2-09856cdcd84a.PNG)


### PROBLEM10
```
USE PROBLEM10; 
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
```
![part2 p10](https://user-images.githubusercontent.com/6895482/61593134-99bbf200-ac16-11e9-8690-de5f56321b0f.PNG)
![part2 p10_2](https://user-images.githubusercontent.com/6895482/61593135-99bbf200-ac16-11e9-838d-e89109675730.PNG)
