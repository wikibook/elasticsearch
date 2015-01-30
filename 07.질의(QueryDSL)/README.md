#7. 질의(QueryDSL)

##7.1 쿼리

###7.1.1 텀, 텀즈 쿼리

예제 7.1 title 필드 값이 prince인 도큐먼트를 텀 쿼리로 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "term" : {
      "title" : "prince"
    }
  }
}'
```


예제 7.2 Prince로 검색하면 저장된 텀과 다르므로 결과에 나타나지 않음
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "term" : {
      "title" : "Prince"
    }
  }
}'
```


예제 7.3 텀즈 쿼리를 이용해서 prince, king 텀 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "terms" : {
      "title" : ["prince","king"]
    }
  }
}'
```


예제 7.4 the, and, of 3개의 텀 중 2개 이상을 포함하는 도큐먼트 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "terms" : {
      "title" : ["the","and","of"],
      "minimum_should_match" : 2
    }
  }
}'
```

###7.1.2 매치, 다중 매치(multi match) 쿼리

예제 7.5 title 필드를 The And로 매치 쿼리 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "match" : {
      "title" : "The And"
    }
  }
}'
```


예제 7.6 operator를 이용해서 검색 조건을 and로 변경
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "match" : {
      "title" : {
        "query" : "The And",
        "operator" : "and"
      }
    }
  }
}'
```


예제 7.7 analyzer를 이용해서 질의문에 공백 형태소 적용
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "match" : {
      "title" : {
        "query" : "prince king",
        "analyzer" : "whitespace"
      }
    }
  }
}'
```


예제 7.8 type: phrase를 사용해 and the 구문을 포함하는 도큐먼트 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "match" : {
      "title" : {
        "query" : "and the",
        "type" : "phrase"
      }
    }
  }
}'
```


예제 7.9 다중 매치 쿼리를 이용해서 prince king을 title, plot 필드에서 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "multi_match" : {
      "fields" : [ "title" , "plot" ],
      "query" : "prince king"
    }
  }
}'
```


###7.1.3 불 쿼리

예제 7.10 불 쿼리를 이용한 다중 조건 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "bool" : {
      "must" : {
        "term" : { "title" : "the" }
      },
      "must_not" : {
        "term" : { "plot" : "prince" }
      },
      "should" : [
        { "term" : { "title" : "time" } },
        { "term" : { "title" : "world" } }
      ]
    }
  }
}'
```


###7.1.4 문자열 쿼리

예제 7.11 문자열 쿼리로 title 필드에서 prince 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "query_string" : {
      "query" : "title:prince"
    }
  }
}'
```


예제 7.12 default_filed, default_operator로 문자열 쿼리 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "query_string" : {
      "query" : "prince king",
      "default_field" : "plot",
      "default_operator" : "and"
    }
  }
}'
```

###7.1.5 접두어 쿼리

예제 7.13 접두어 쿼리로 질의어 prin 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "prefix" : {
      "title" : "prin"
    }
  }
}'
```

###7.1.6 범위 쿼리

예제 7.14 범위 쿼리로 pages 필드가 50~149인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "range" : {
      "pages" : { "gte" : 50, "lt" : 150 }
    }
  }
}'
```


예제 7.15 범위 쿼리로 written 필드가 1600-01-01 ~ 1699-12-31인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "range" : {
      "written" : {
        "gte" : "1600-01-01",
        "lt" : "1699-12-31"
      }
    }
  }
}'
```

###7.1.7 전체 매치 쿼리

예제 7.16 전체 매치 쿼리
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "match_all" : {}
  }
}'
```


###7.1.8 퍼지 쿼리

예제 7.17 퍼지 쿼리를 이용한 질의어 tree 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "fuzzy" : {
      "title" : "tree"
    }
  }
}'
```


예제 7.18 퍼지 쿼리를 이용해 page 필드가 80~120인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "fuzzy" : {
      "pages" : {
        "value" : 100,
        "fuzziness" : 20
      }
    }
  }
}'
```

##7.2 필터

###7.2.1 텀, 텀즈 필터

예제 7.19 텀 필터를 이용해서 title 필드가 prince인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "term" : {
      "title" : "prince"
    }
  }
}'
```


예제 7.20 텀즈 필터를 이용해서 title 필드가 prince, king 인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "terms" : {
      "title" : ["prince", "king"]
    }
  }
}'
```


예제 7.21 title 필드가 and, the인 값을 execution: and로 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "terms" : {
      "title" : ["and", "the"],
      "execution" : "and"
    }
  }
}'
```

###7.2.2 범위 필터

예제 7.22 범위 필터로 pages 필드가 50~149인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "range" : {
      "pages" : { "gte" : 50, "lt" : 150 }
    }
  }
}'
```

###7.2.3 and, or, not 필터

예제 7.23 not 필터로 pages 필드가 50~149가 아닌 도큐먼트 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "not" : {
      "range" : {
        "pages" : { "gte" : 50, "lt" : 150 }
      }
    }
  }
}'
```


예제 7.24 and 필터로 pages 필드가 50~149이면서 title: the인 값 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "and" : [
      {
        "range" : {
          "pages" : { "gte" : 50, "lt" : 150 }
        }
      },
      {
        "term" : { "title" : "the" }
      }
    ]
  }
}'
```


###7.2.4 불 필터

예제 7.25 불 필터를 이용한 다중 조건 검색
```
curl 'localhost:9200/books/_search?pretty' -d '
{
  "filter" : {
    "bool" : {
      "must" : {
        "term" : { "title" : "the" }
      },
      "must_not" : {
        "term" : { "plot" : "prince" }
      },
      "should" : [
        { "term" : { "title" : "time" } },
        { "term" : { "title" : "world" } }
      ]
    }
  }
}'
```

###7.2.5 위치 필터

예제 7.26 geo_bounding_box 필터로 사각형 영역의 값을 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "filter" : {
    "geo_bounding_box" : {
      "location" : {
        "top_left" : { "lat" : 38.00, "lon" : 126.00 },
        "bottom_right" : { "lat" : 37.00, "lon" : 127.00 }
      }
    }
  }
}'
```


예제 7.27 geo_distance 필터로 위도 경도 37.52, 126.98을 중심으로 5km 이내에 있는 도큐먼트 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "filter" : {
    "geo_distance" : {
      "distance" : "5km",
      "location" : { "lat" : 37.52, "lon" : 126.98 }
    }
  }
}'
```


예제 7.28 geo_distance_range 필터로 5km~10km 사이에 있는 도큐먼트 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "filter" : {
    "geo_distance_range" : {
      "from" : "5km",
      "to" : "10km",
      "location" : { "lat" : 37.52, "lon" : 126.98 }
    }
  }
}'
```


예제 7.29 geo_polygon 필터로 삼각형 영역 사이의 값 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "filter" : {
    "geo_polygon" : {
      "location" : {
        "points" : [
          { "lat" : 38.00, "lon" : 127.00 },
          { "lat" : 37.00, "lon" : 127.00 },
          { "lat" : 38.00, "lon" : 128.00 }
        ]
      }
    }
  }
}'
```
