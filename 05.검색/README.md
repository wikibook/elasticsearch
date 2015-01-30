#5. 검색

##4.1 클러스터와 노드

###4.1.1 노드 바인딩

### books 데이터 입력
```
curl -XPOST localhost:9200/_bulk --data-binary @5_1_books.json
```

### magazines 데이터 입력
```
curl -XPOST localhost:9200/_bulk --data-binary @5_2_magazines.json
```
