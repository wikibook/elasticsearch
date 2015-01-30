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
curl -XDELETE http://localhost:9200/books
curl -XGET http://localhost:9200/books/book/1
```


###3.2.3 데이터 업데이트(_update) API

예제 3.13 /books/book/1 도큐먼트 확인
```
curl -XGET localhost:9200/books/book/1
```


예제 3.14 books/book/1 도큐먼트에 category 필드 추가
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "doc" : {
    "category" : "ICT"
  }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.15 books/book/1 도큐먼트의 author 필드를 Lee로 변경
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "doc" : {
    "author" : "Lee"
  }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.16 books/book/1 도큐먼트의 pages 필드에 50을 더함
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "script" : "ctx._source.pages += 50"
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.17 author 필드의 값 "Lee"를 배열 ["Lee"]로 변경
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "doc" : {
    "author" : ["Lee"]
  }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.18 author 필드에 값 "Kim"을 추가
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "script" : "ctx._source.author += new_author",
  "params" : { "new_author" : "Kim" }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.19 author 필드가 "Kim"을 포함하는 경우 pages 필드값을 100으로 변경
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "script" : "if(ctx._source.author.contains(auth)) { ctx._source.pages = 100 } else { ctx._source.pages = 200 }",
  "params" : { "auth" : "Kim" }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.20 pages 필드가 100 이하일 때 도큐먼트 삭제
```
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "script" : "ctx._source.pages <= page_cnt ? ctx.op = \"delete\" : ctx.op = \"none\"",
  "params" : { "page_cnt" : 100 }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.21 도큐먼트가 없다면 도큐먼트 생성. 도큐먼트가 있다면 counter 필드 1 증가
```
curl -XGET localhost:9200/books/book/1?pretty
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "script" : "ctx._source.counter += count",
  "params" : { "count" : 1 },
  "upsert" : { "counter" : 0 }
}'
curl -XGET localhost:9200/books/book/1?pretty
curl -XPOST localhost:9200/books/book/1/_update -d '
{
  "script" : "ctx._source.counter += count",
  "params" : { "count" : 1 },
  "upsert" : { "counter" : 0 }
}'
curl -XGET localhost:9200/books/book/1?pretty
```


###3.2.4 파일을 이용한 데이터 처리

예제 3.22 입력할 데이터를 book_1 파일에 저장
```
echo '{
  "title" : "Elasticsearch Guide",
  "author" : [ "Kim", "Lee" ],
  "date" : "2014-05-01",
  "pages" : 300
}' > book_1
cat book_1
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
