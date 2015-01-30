#2. 엘라스틱서치 설치와 실행
##2.1 엘라스틱서치 설치
###2.1.1 엘라스틱서치 설치 파일 비교


그림 2.1 엘라스틱서치 내려받기 (http://www.elasticsearch.org/overview/elkdownloads/)


###2.1.2 유닉스 운영체제에서 설치와 실행


예제 2.1 bin/elasticsearch 실행.
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



예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```


예제 2.5 kill 명령으로 엘라스틱서치 프로세스 종료
```

```
