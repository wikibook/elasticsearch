#6. 페이셋과 어그리게이션

##4.1 클러스터와 노드

###4.1.1 노드 바인딩



```
curl -XPUT http://localhost:9200/hotels/ -d '
{
  "mappings" : {
    "hotel" : {
      "properties" : {
        "name" : { "type" : "string" },
        "stars" : { "type" : "long" },
        "rooms" : { "type" : "long" },
        "location" : { "type" : "geo_point" },
        "city" : { "type" : "string" },
        "address" : { "type" : "string" },
        "internet" : { "type" : "boolean" },
        "service" : { "type" : "string", "index" : "not_analyzed" },
        "checkin": { "type" : "date" , "format" : "dateOptionalTime"}
      }
    }
  }
}'
```

### hotels 데이터 입력
```
curl -XPOST localhost:9200/_bulk --data-binary @6_1_hotels.json
```
