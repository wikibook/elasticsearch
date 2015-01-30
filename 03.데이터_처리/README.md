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
