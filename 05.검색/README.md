### books 데이터 입력
```
curl -XPOST localhost:9200/_bulk --data-binary @5_1_books.json
```

### magazines 데이터 입력
```
curl -XPOST localhost:9200/_bulk --data-binary @5_2_magazines.json
```
