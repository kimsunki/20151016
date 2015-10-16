# 20151016

수업내용 

centos 
•전체 또는 부분 데이터에 대해 다양한 분석
•대용량 처리를 위해 분산, 병렬 처리 필요
        
Hbase
•원본 데이터를 실시간으로 저장, 조회, 처리를 위한 저장소
•Hadoop의 서브프로젝트로 시작하여 지금은 Apache 정식 프로젝트가 되었으며, HBase란 이름은 Hadoop database에서 유래됨
•Hadoop의 “분산된,확장가능한,대용량 데이터를 다루는” 특징을 물려받으면서 ” Random, realtime read/write access”라는 고유한 데이터베이스스러운 특징도 가지고 있음.
•관계형 데이터베이스가 아닌 NoSQL 데이터베이스로 분류되고, 기존 SQL문을 일부만 지원하며, column-oriented 로 데이터를 저장.

OpenTSDB

•HBase와 연계하여 시계열 데이터를 관리
•OpenTSDB is a tool that includes a time series database (built on hBase) and a web UI that uses GNUPlot for charting.


# root 권한으로 필요시 수행
   yum -y update
   
   
   <HBASE IMSTALL -> 오픈소으이기때문에 버전이 바뀔수 있음>
    cd /usr/local
    mkdir data
    cd data
    wget http://www.apache.org/dist/hbase/stable/hbase-1.1.2-bin.tar.gz
    tar xvfz hbase-1.1.2-bin.tar.gz
    cd hbase-1.1.2
    hbase_rootdir=${TMPDIR-'/usr/local/data'}/tsdhbase
    iface=lo'uname | sed -n s/Darwin/0/p'
    
    
<hbase-site.sml correction>
    vim conf/hbase-site.xml
  
  삽입 하기
    <configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>file:///DIRECTORY/hbase</value>
    </property>
    <property>
        <name>hbase.zookp.property.eataDir</name>
        <value>/DRECTORY/zookeeper</value>
    </property>
</configuration>
    
    
    *<hbase.sh Run>
    ./bin/start-hbase.sh
    
    
    
    *<GNUPLOT INSTALL>
    cd /usr/local
    yum install ant ant-nodeps lzo-devel.x86_64
    yum list \*gd\*
    yum install gd-devel.i686
    wget http://sourceforge.net/projects/gnuplot/files/gnuplot/4.6.3/gnuplot-4.6.3.tar.gz
    tar zxvf gnuplot-4.6.3.tar.gz
    cd gnuplot-4.6.3
    ./configure
    make install
    yum install gnuplot
    
    
    *<OpenTSDB INSTALL>
    
    cd /usr/local
    # 필요시 yum install git
    git clone git://github.com/OpenTSDB/opentsdb.git
    cd opentsdb
    yum install autoconf
    yum install automake
    ./build.sh  <- It takes about 10 minutes to complete.
    env COMPRESSION=NONE HBASE_HOME=/usr/local/data/hbase-1.1.2 ./src/create_table.sh 
    tsdtmp=${TMPDIR-'/usr/local/data'}/tsd
    mkdir -p "$tsdtmp"
    
    
    *<OpenTSDB RUN>
    ./build/tsdb tsd --port=4242 --staticroot=build/staticroot --cachedir=/usr/local/data --auto-metric

    # 웹브라우저에서 확인
    http://127.0.0.1:4242
    
    
    
    * Data Input Test(Restful 방법)
    
    sudo yum install python-setuptools python-setuptools-devel
    sudo easy_install pip
    pip install requests

    vim post_test.py
    
    
 <CODE>
import time
import requests
import json

url = "http://127.0.0.1:4242/api/put"

data = {
    "metric": "foo.bar",
    "timestamp": time.time(),
    "value": 2015,
    "tags": {
       "host": "mypc"
    }
}

ret = requests.post(url, data=json.dumps(data))
print "ok"
<CODE END>

python post_test.py
=> 결과로 OK 떠야함 
# 웹브라우저에서 확인
 http://127.0.0.1:4242
 
 # 웹브라우저에서 확인
http://127.0.0.1:4242/api/query?start=2015/10/14-00:00:00&end=2015/10/14-08:19:49&m=sum:foo.bar

# 결과
[{"metric":"foo.bar","tags":{"host":"mypc"},"aggregateTags":[],"dps":{"1444835983":2015.0}}]

    
    

