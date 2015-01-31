#10. ELK 스택

##10.1 로그스태시

###10.1.1 입력, 출력, 필터

###10.1.2 입력 및 출력 설정


예제 10.1 로그스태시 설정 파일
```
input {
  <입력 경로> {
    <옵션명> => <옵션값>
  }
}

filter {
  <필터> {
    <옵션명> => <옵션값>
  }
  if [<필드명>] == <필드값>{
    <필터> {
      <옵션명> => <옵션값>
    }
  }
}

output {
  <출력 경로> {
    <옵션명> => <옵션값>
  }
}
```


예제 10.2 logstash.conf 파일에서 입력과 출력을 각 표준 입력, 표준 출력으로 설정
```
input {
  stdin { }
}

output {
  stdout { }
}
```


예제 10.3 표준 입력으로 Hello world 입력 후 표준 출력
```
bin/logstash -f logstash.conf

Hello world
```


예제 10.4 logstash.conf 파일에 표준 출력 옵션으로 codec => json 추가
```
output {
  stdout {
    codec => json
  }
}
```


예제 10.5 표준 입력으로 Hello world를 입력하면 JSON 형식으로 출력
```
bin/logstash -f logstash.conf

Hello world
```


예제 10.6 logstash.conf 파일에 /data/output.txt로 파일 출력 추가
```
output {
  stdout { codec => json }
  file {
    codec => json
    path => "/data/output.txt"
  }
}
```


예제 10.7 /data/output.txt 파일로 출력
```
cat /data/output.txt
```


예제 10.8 logstash.conf 파일에 표준 입력 옵션으로 codec => json 추가
```
input {
  stdin {
    codec => json
  }
}
```


예제 10.9 JSON 형식의 데이터 표준 입력
```
bin/logstash -f logstash.conf

{ "title": "Romeo and Juliet", "author": "William Shakespeare", "category":"Tragedies" }
```


###10.1.3 필터 설정

예제 10.10 logstash.conf 파일에 mutate 필터의 add_field 옵션 추가
```
input {
  stdin { codec => json }
}

filter {
  mutate {
    add_field => { "plot" => "This is a story about %{title}" }
  }
}

output {
  stdout { codec => json }
}
```


예제 10.11 mutate 필터 설정 후 JSON 형식의 데이터 표준 입력
```
bin/logstash -f logstash.conf

{ "title": "Romeo and Juliet", "author": "William Shakespeare", "category": "Tragedies" }
```


예제 10.12 logstash.conf 파일에 mutate 필터의 replace 옵션 추가
```
filter {
  mutate {
    replace => [ "title", "This is a story about %{title}" ]
  }
}
```


예제 10.13 mutate 필터 설정 후 JSON 형식의 데이터 표준 입력
```
bin/logstash -f logstash.conf

{ "title": "Romeo and Juliet", "author": "William Shakespeare", "category":"Tragedies" }
```


예제 10.14 logstash.conf 파일에 title 값이 Hamlet인 데이터만 mutate 적용
```
filter {
  if [title] == "Hamlet" {
    mutate {
      replace => [ "title", "This is a story about %{title}" ]
    }
  }
}
```


예제 10.15 조건문 설정 후 JSON 형식의 데이터 표준 입력
```
bin/logstash -f logstash.conf

{ "title": "Romeo and Juliet", "author": "William Shakespeare", "category": "Tragedies" }

{ "title": "Hamlet", "author": "William Shakespeare", "category": "Tragedies" }
```

###10.1.4 엘라스틱서치 설정

예제 10.16 logstash.conf 파일에 /data/logs/*.log 파일 입력
```
input {
  file {
    codec => json
    path => "/data/logs/*.log"
  }
}
```


예제 10.17 logstash.conf 파일에 elasticsearch 출력 추가
```
input {
  file {
    codec => json
    path => "/data/logs/*.log"
  }
}

output{
  elasticsearch{
    host => "localhost"
    cluster => "es_test"
    node_name => "Logstash-test"
    index => "weblog"
    index_type => "weblog-%{+YYYY.MM.dd}"
  }
}
```

##10.2 키바나

###10.2.1 대시보드 설정

###10.2.2 행과 패널

###10.2.3 hits 패널

###10.2.4 terms 패널

###10.2.5 Histogram 패널

###10.2.6 Table 패널

###10.2.7 질의와 필터

###10.2.8 검색어 쿼리
