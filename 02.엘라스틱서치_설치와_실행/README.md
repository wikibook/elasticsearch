#2. 엘라스틱서치 설치와 실행


##2.1 엘라스틱서치 설치


###2.1.1 엘라스틱서치 설치 파일 비교


그림 2.1 엘라스틱서치 내려받기 (http://www.elasticsearch.org/overview/elkdownloads/)


###2.1.2 유닉스 운영체제에서 설치와 실행


예제 2.1 bin/elasticsearch 실행
```
bin/elasticsearch
```


예제 2.2 백그라운드로 실행 중인 엘라스틱서치의 로그 파일 내용
```
bin/elasticsearch -d

cat logs/elasticsearch.log
```


예제 2.3 curl -XGET http://localhost:9200 명령으로 엘라스틱서치 정보 확인
```
curl -XGET http://localhost:9200
```


예제 2.4 ps 명령으로 백그라운드로 실행중인 엘라스틱서치 프로세스 검색
```
ps -ef | grep elasticsearch
```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```
kill 30985

ps -ef | grep elasticsearch
```


예제 2.6 -p 옵션으로 es.pid 파일에 프로세스 ID 저장
```
bin/elasticsearch -d -p es.pid

ls

cat es.pid

ps -ef | grep elasticsearch
```


예제 2.7 kill 명령으로 엘라스틱서치 프로세스 종료 (31070 => 프로세스 ID 입력)
```
kill 31070

ls
```


예제 2.8 start.sh, stop.sh 파일 생성
```
echo 'bin/elasticsearch -d -p es.pid' > start.sh

echo 'kill `cat es.pid`' > stop.sh

chmod 755 start.sh stop.sh

ls
```


예제 2.9 start.sh, stop.sh 실행
```
./start.sh

ls

ps -ef | grep elasticsearch

./stop.sh

ls

ps -ef | grep elasticsearc
```


###2.1.3 유닉스 서비스로 설치 및 실행


예제 2.10 엘라스틱서치 rpm 설치
```
sudo rpm -ivh elasticsearch-1.1.1.rpm
```


예제 2.11 엘라스틱서치 실행 파일과 설정 파일
```
ls /etc/init.d/elasticsearch

ls /etc/elasticsearch/
```


예제 2.12 엘라스틱서치 서비스 시작
```
sudo service elasticsearch start

ps -ef | grep elasticsearch
```


예제 2.13 엘라스틱서치 서비스 종료
```
sudo service elasticsearch stop

ps -ef | grep elasticsearch
```


예제 2.14 서버 시작 시 엘라스틱서치 서비스가 자동으로 시작되게 설정
```
sudo /sbin/chkconfig --add elasticsearch
```


###2.1.4 윈도 운영체제에서 설치 및 실행


###2.1.5 윈도 서비스로 설치 및 실행


예제 2.15 윈도 명령 프롬프트에서 service.bat 실행
```
service.bat
```


예제 2.16 윈도 운영체제에 엘라스틱서치 서비스 설치
```
service.bat install
```


##2.2 엘라스틱서치 프로그램 구조


예제 2.17 엘라스틱서치 실행 디렉터리 – 엘라스틱서치 실행 전
```
ls
```


예제 2.18 엘라스틱서치 bin/ 디렉터리 조회
```
ls bin/
```


예제 2.19 엘라스틱서치 config/ 디렉터리 조회
```
ls config/
```


예제 2.20 엘라스틱서치 lib/ 디렉터리 조회
```
ls lib/
```


예제 2.21 엘라스틱서치 실행 프로세스 조회
```
bin/elasticsearch -d

ps -ef | grep elasticsearch
```


예제 2.22 엘라스틱서치 실행 디렉터리 – 엘라스틱서치 실행 후
```
ls
```


예제 2.23 엘라스틱서치 logs/ 디렉터리 조회
```
ls logs/
```


예제 2.24 엘라스틱서치 data/ 디렉터리 조회
```
ls data/

tree data/
```


##2.3 환경 설정


###2.3.1 bin/elasticsearch.in.sh


예제 2.25 엘라스틱서치 실행 프로세스 조회
```
bin/elasticsearch -d

ps -ef | grep elasticsearch
```


<ES_MIN_MEM=512m, ES_MAX_MEM=2g> 파일
```
if [ "x$ES_MIN_MEM" = "x" ]; then
  ES_MIN_MEM=256m
fi
if [ "x$ES_MAX_MEM" = "x" ]; then
  ES_MAX_MEM=1g
fi
if [ "x$ES_HEAP_SIZE" != "x" ]; then
  ES_MIN_MEM=$ES_HEAP_SIZE
  ES_MAX_MEM=$ES_HEAP_SIZE
Fi
```


<elasticsearch.in.sh> ES_MIN_MEM=512m, ES_MAX_MEM=2g로 변경
```
if [ "x$ES_MIN_MEM" = "x" ]; then
  ES_MIN_MEM=512m
fi
if [ "x$ES_MAX_MEM" = "x" ]; then
  ES_MAX_MEM=2g
fi
if [ "x$ES_HEAP_SIZE" != "x" ]; then
  ES_MIN_MEM=$ES_HEAP_SIZE
  ES_MAX_MEM=$ES_HEAP_SIZE
Fi
```


예제 2.26 elasticsearch.in.sh 설정 변경 후 실행 프로세스 조회
```
ps -ef | grep elasticsearch
```


<elasticsearch.in.sh> ES_HEAP_SIZE=1g 설정 추가
```
ES_HEAP_SIZE=1g
if [ "x$ES_MIN_MEM" = "x" ]; then
  ES_MIN_MEM=512m
fi
if [ "x$ES_MAX_MEM" = "x" ]; then
  ES_MAX_MEM=2g
fi
if [ "x$ES_HEAP_SIZE" != "x" ]; then
  ES_MIN_MEM=$ES_HEAP_SIZE
  ES_MAX_MEM=$ES_HEAP_SIZE
Fi
```


예제 2.27 elasticsearch.in.sh 설정 변경 후 실행 프로세스 조회
```
ps -ef | grep elasticsearch
```


###2.3.2 config/elasticsearch.yml


예제 2.28 클러스터 설정 확인
```
curl -XGET localhost:9200/_cluster/stats?pretty=true
```


예제 2.29 노드명 변경 후 엘라스틱서치 실행
```
bin/elasticsearch
```


예제 2.30 path.logs 설정 변경 후 엘라스틱서치 디렉터리 구조 확인
```
ls

ls test_log/
```


예제 2.31 path.logs 설정 변경 후 엘라스틱서치 디렉터리 구조 확인
```
ls

ls test_log/
```


###2.3.3 config/logging.yml


예제 2.32 엘라스틱서치 로그 파일 조회
```
ls test_log/
```


##2.4 플러그인 설치


예제 2.33 헤드 플러그인 설치.
```
bin/plugin --install mobz/elasticsearch-head
```


예제 2.34 설치된 플러그인 확인
```
bin/plugin --list
```


예제 2.35 실행 중인 노드 상태 확인
```
curl -XGET localhost:9200/_nodes?pretty=true
```


예제 2.36 헤드 플러그인 삭제 후 설치된 플러그인 확인
```
bin/plugin --remove head

bin/plugin --list
```
