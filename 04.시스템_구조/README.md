#4. 시스템 구조

##4.1 클러스터와 노드

###4.1.1 노드 바인딩

예제 4.1 헤드 플러그인 설치
```
bin/plugin --install mobz/elasticsearch-head
```


예제 4.2 클러스터명을 es_test로 설정
```
cluster.name: es_test
```


예제 4.3 엘라스틱서치 실행
```
bin/elasticsearch
```


예제 4.4 새로운 엘라스틱서치 실행
```
bin/elasticsearch
```


예제 4.6 9200, 9201 포트의 REST API 상태 확인
```
curl -XGET localhost:9200

curl -XGET localhost:9201
```


예제 4.7 9200 포트로 데이터 입력 후 9201 포트로 조회
```
curl -XPUT http://localhost:9200/books/book/1 -d '
{
  "title" : "Elasticsearch Guide",
  "author" : "Kim",
  "date" : "2014-05-01",
  "pages" : 250
}'

curl -XGET http://localhost:9201/books/book/1
```


예제 4.8 클러스터명 es_test_2로 변경 후 엘라스틱서치 실행
```
bin/elasticsearch
```


###4.1.2 마스터 노드와 데이터 노드

예제 4.9 /es_test/es_1의 config/elasticsearch.yml 파일 설정
```
cluster.name: es_test
node.name: "Node1"
node.master: true
node.data: false
```


예제 4.10 /es_test/es_2의 config/elasticsearch.yml 파일 설정
```
cluster.name: es_test
node.name: "Node2"
node.master: false
node.data: true
```


예제 4.11 /es_test/es_1, /es_test/es_2 엘라스틱서치 실행
```
bin/elasticsearch

bin/elasticsearch
```


예제 4.12 /es_test/es_2에 -- node.name=Node3 옵션으로 엘라스틱서치 실행
```
bin/elasticsearch --node.name=Node3
```


##4.2 샤드와 복사본

예제 4.13 magazines 인덱스의 샤드와 복사본 설정
```
curl -XPUT localhost:9200/magazines -d '
{
  "settings" : {
    "number_of_shards" : 2,
    "number_of_replicas" : 0
  }
}'
```


예제 4.14 config/elasticsearch.yml 파일에서 샤드와 복사본 개수 설정
```
index.number_of_shards: 5
index.number_of_replicas: 1
```


##4.3 네트워크 바인딩과 디스커버리

###4.3.1 젠 디스커버리

예제 4.15 192.168.1.10 서버에서 엘라스틱서치 실행
```
bin/elasticsearch
```


예제 4.16 config/elasticsearch.yml 파일에서 젠 디스커버리 유니캐스트 설정
```
discovery.zen.ping.multicast.enabled: false
discovery.zen.ping.unicast.hosts: ["192.168.1.10", "192.168.1.11"]
```


예제 4.17 192.168.1.10, 192.168.1.11 서버에서 유니캐스트로 바인딩
```
bin/elasticsearch

bin/elasticsearch
```


예제 4.18 서버 1의 config/elasticsearch.yml 파일 설정
```
cluster.name: es_test
network.bind_host: 192.168.0.40
network.publish_host: 121.131.44.50
discovery.zen.ping.multicast.enabled: false
discovery.zen.ping.unicast.hosts: ["192.168.0.40", "54.199.179.186"]
```


예제 4.19 서버 2의 config/elasticsearch.yml 파일 설정
```
cluster.name: es_test
network.bind_host: 172.31.3.238
network.publish_host: 54.199.179.186
discovery.zen.ping.multicast.enabled: false
discovery.zen.ping.unicast.hosts: ["172.31.3.238", "121.131.44.50"]
```


예제 4.20 서버 1 엘라스틱서치 노드 실행
```
bin/elasticsearch
```


예제 4.21 서버 2 엘라스틱서치 노드 실행
```
bin/elasticsearch
```


###4.3.2 아마존 EC2 디스커버리

예제 4.22 EC2에 엘라스틱서치 설치
```
wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.1.1.tar.gz

tar xvfz elasticsearch-1.1.1.tar.gz
```


예제 4.23 aws 플러그인 설치.
```
bin/plugin -install elasticsearch/elasticsearch-cloud-aws/2.1.1
```


예제 4.24 EC2 인스턴스에서의 config/elasticsearch.yml 파일 설정
```
cluster.name: es_test

cloud:
  aws:
    access_key: AKIAIEKJMRZYRL43P52A
    secret_key: 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
    region: ap-northeast-1

discovery:
  type: ec2
```


예제 4.25 AWS EC2에서 엘라스틱서치 실행 - 1
```
bin/elasticsearch
```


예제 4.26 AWS EC2에서 엘라스틱서치 실행 - 2
```
bin/elasticsearch
```
