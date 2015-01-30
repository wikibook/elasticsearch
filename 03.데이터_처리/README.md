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


예제 3.23 book_1 파일의 내용을 읽어 들여 도큐먼트 데이터로 저장
```
curl -XPUT localhost:9200/books/book/1 -d @book_1

curl -XGET localhost:9200/books/book/1?pretty
```


예제 3.24 update_1 파일의 내용을 읽어 들여 업데이트 스크립트 실행
```
echo '{
  "doc" : {
    "category" : "ICT"
  }
}' > update_1

curl -XPOST localhost:9200/books/book/1/_update -d @update_1

curl -XGET localhost:9200/books/book/1?pretty
```


###3.2.5 벌크(_bulk) API를 이용한 배치 작업

예제 3.25 벌크 API를 이용한 /books/book/1, 2 도큐먼트 입력
```
curl -XPOST localhost:9200/_bulk -d '
{ "index" : { "_index" : "books", "_type" : "book", "_id" : "1" } }
{ "title" : "Elasticsearch Guide", "author" : "Kim", "pages" : 250 }
{ "index" : { "_index" : "books", "_type" : "book", "_id" : "2" } }
{ "title" : "Elasticsearch Easy Guide", "author" : "Lee", "pages" : 300 }'

curl -XGET localhost:9200/books/book/1?pretty

curl -XGET localhost:9200/books/book/2?pretty
```


<bulk_1> 파일 내용
```
{ "delete" : { "_index" : "books", "_type" : "book", "_id" : "1" } }
{ "update" : { "_index" : "books", "_type" : "book", "_id" : "2" } }
{ "doc" : {"date" : "2014-05-01"} }
{ "create" : { "_index" : "books", "_type" : "book", "_id" : "3" } }
{ "title" : "Elasticsearch Guide II", "author" : "Park", "pages" : 400 }
```


예제 3.26 bulk_1 파일의 내용을 벌크로 실행
```
curl -XPOST localhost:9200/_bulk?pretty --data-binary @bulk_1
```


예제 3.27 벌크로 처리된 books/book/1, 2, 3 도큐먼트
```
curl -XGET localhost:9200/books/book/1?pretty

curl -XGET localhost:9200/books/book/2?pretty

curl -XGET localhost:9200/books/book/3?pretty
```


예제 3.28 벌크 API를 이용한 /books/book/1, 2도큐먼트 입력
```
curl -XPOST localhost:9200/books/book/_bulk -d '
{ "index" : { "_id" : "1" } }
{ "title" : "Elasticsearch Guide", "author" : "Kim", "pages" : 250 }
{ "index" : { "_id" : "2" } }
{ "title" : "Elasticsearch Easy Guide", "author" : "Lee", "pages" : 300 }'
```


###3.2.6 벌크 UDP API

예제 3.29 엘라스틱서치에서 bulk UDP API 실행
```
bin/elasticsearch
```


예제 3.30 bulk_udp 파일에 벌크 입력 데이터 저장
```
curl -XDELETE localhost:9200/books

echo '{ "index" : { "_index" : "books", "_type" : "book", "_id" : "1" } }
{ "title" : "Elasticsearch Guide", "author" : "Kim", "pages" : 250 }
{ "index" : { "_index" : "books", "_type" : "book", "_id" : "2" } }
{ "title" : "Elasticsearch Easy Guide", "author" : "Lee", "pages" : 300 }' > bulk_udp

cat bulk_udp
```


예제 3.31 nc 명령으로 벌크 UDP 입력을 통한 도큐먼트 생성
```
cat bulk_udp | nc -w 0 -u localhost 9700

curl -XGET localhost:9200/books/book/1?pretty

curl -XGET localhost:9200/books/book/2?pretty
```


##3.3 포스트맨을 이용한 윈도에서 엘라스틱서치 사용


크롬 웹 스토어에서 포스트맨 검색 - https://chrome.google.com/webstore/search/postman
