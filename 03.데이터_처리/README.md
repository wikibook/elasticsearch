#3. 데이터 처리


##3.1 엘라스틱서치의 데이터 구조


##3.2 엘라스틱서치 데이터 처리


###3.2.1 데이터 입력


예제 3.1 엘라스틱서치 실행
```
./start.sh
ps -ef | grep elasticsearch
```


예제 3.2 /books/book/1 도큐먼트 데이터 입력
```
curl -XPUT http://localhost:9200/books/book/1 -d '
{
  "title" : "Elasticsearch Guide",
  "author" : "Kim",
  "date" : "2014-05-01",
  "pages" : 250
}'
```


예제 3.3 /books/book/1 도큐먼트 데이터 확인
```
curl -XGET http://localhost:9200/books/book/1
```


예제 3.4 /books/book에 임의의 id로 도큐먼트 생성
```
curl -XPOST localhost:9200/books/book -d '
{
  "title" : "Elasticsearch Guide",
  "author" : "Kim",
  "started" : "2014-05-1",
  "pages" : 250
}'
```


예제 3.5 /books/book/1 도큐먼트 새로 입력
```
curl -XPUT http://localhost:9200/books/book/1 -d '
{
  "title" : "Elasticsearch Guide",
  "author" : [ "Kim", "Lee" ],
  "date" : "2014-05-01",
  "pages" : 300
}'
```


예제 3.6 수정된 /books/book/1 도큐먼트 확인
```
curl -XGET http://localhost:9200/books/book/1?pretty=true
```


예제 3.7 /books/book/1/_source 데이터 확인
```
curl -XGET http://localhost:9200/books/book/1/_source
```


###3.2.2 데이터 삭제

예제 3.8 /books/book/1/ 도큐먼트 삭제
```
curl -XDELETE http://localhost:9200/books/book/1
```


예제 3.9 /books/book/1/ 도큐먼트 삭제 후 확인
```
curl -XGET http://localhost:9200/books/book/1
```


예제 3.10 /books/book/1 도큐먼트 삭제 후 다시 데이터 입력
```
curl -XPOST http://localhost:9200/books/book/1 -d '
{
  "title" : "Elasticsearch Guide",
  "author" : [ "Kim", "Lee" ],
  "date" : "2014-05-01",
  "pages" : 300
}'
```


예제 3.11 books/book 필드 삭제 후 도큐먼트 데이터 입력
```
curl -XDELETE http://localhost:9200/books/book
curl -XPOST http://localhost:9200/books/book/1 -d '
{
  "title" : "Elasticsearch Guide",
  "author" : [ "Kim", "Lee" ],
  "date" : "2014-05-01",
  "pages" : 300
}'
```


예제 3.12 /books 인덱스 삭제 후 /books/book/1 도큐먼트 확인
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```


예제 2.1 bin/elasticsearch 실행.
```

```
