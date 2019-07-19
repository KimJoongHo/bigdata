sudo useradd training -u 3800
sudo passwd training


sudo groupadd skcc
sudo gpasswd -a training skcc

sudo chmod 644 /etc/sudoers
sudo bash -c 'echo training ALL=NOPASSWD: ALL >> /etc/sudoers'
sudo chmod 440 /etc/sudoers


sudo bash -c 'echo 172.31.45.0 cm cm.com >> /etc/hosts'
sudo bash -c 'echo 172.31.41.176 m1 m1.com >> /etc/hosts'
sudo bash -c 'echo 172.31.47.25 d1 d1.com >> /etc/hosts'
sudo bash -c 'echo 172.31.34.255 d2 d2.com >> /etc/hosts'
sudo bash -c 'echo 172.31.42.90 d3 d3.com >> /etc/hosts'

getent hosts


cat /etc/redhat-release


sudo yum install -y mariadb-server
sudo systemctl enable mariadb
sudo systemctl start mariadb

sudo sh -c "echo 'vm.swappiness=1'>> /etc/sysctl.conf" 
sudo sh -c "echo never > /sys/kernel/mm/transparent_hugepage/defrag" 
sudo sh -c "echo never > /sys/kernel/mm/transparent_hugepage/enabled" 
sudo sh -c "echo 'echo never > /sys/kernel/mm/transparent_hugepage/defrag' >> /etc/rc.local" 
sudo sh -c "echo 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' >> /etc/rc.local" 

sudo cat /etc/rc.local 
sudo sed -i 's/^\(SELINUX\s*=\s*\).*$/\1disabled/' /etc/selinux/config getenforce 


#repository 설정
sudo yum install -y wget
sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/
sudo sed -i 's/^baseurl=.*/baseurl=https:\/\/archive.cloudera.com\/cm5\/redhat\/7\/x86_64\/cm\/5.15.2\//' /etc/yum.repos.d/cloudera-manager.repo

sudo rpm --import https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/RPM-GPG-KEY-cloudera
sudo yum install -y cloudera-manager-daemons cloudera-manager-server


sudo hostnamectl set-hostname cm


#ntp설정
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


#sshd 설정
sudo sed -i 's/PermitRootLogin/#PermitRootLogin/' /etc/ssh/sshd_config
sudo bash -c 'echo PermitRootLogin yes >> /etc/ssh/sshd_config'
#sudo sed -i 's/^PermitRootLogin .*/PermitRootLogin yes/' /etc/ssh/sshd_config
sudo sed -i 's/^PasswordAuthentication .*/PasswordAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd


#jdk 설정 및 path추가
sudo yum install -y oracle-j2sdk*

cd ~
sudo sed -i 's/export PATH/#export PATH/' .bash_profile
sudo bash -c 'echo JAVA_HOME=/usr/java/jdk1.7.0_67-cloudera >> .bash_profile'
sudo bash -c 'echo export PATH=\$JAVA_HOME/bin:\$PATH >> .bash_profile'


CREATE DATABASE test DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;

GRANT ALL ON test.* TO 'training'@'%' IDENTIFIED BY 'training';




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
