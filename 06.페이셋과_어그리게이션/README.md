#6. 페이셋과 어그리게이션

##6.1 페이셋

예제 6.1 hotels 인덱스 매핑 설정
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


예제 6.2 hotels 인덱스 데이터 입력
```
curl -XPOST localhost:9200/_bulk --data-binary @6_1_hotels.json
```


###6.1.1 텀 페이셋

예제 6.3 service 필드의 텀 페이셋 검색
```
$ curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "query" : {
    "term" : { "name" : "seoul" }
  },
  "facets" : {
    "term_service" : {
      "terms" : {
        "field" : "service"
      }
    }
  }
}'
```


예제 6.4 상위 3개의 텀 페이셋 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "query" : {
    "term" : { "name" : "seoul" }
  },
  "facets" : {
    "term_service" : {
      "terms" : {
        "field" : "service",
        "size" : 3
      }
    }
  }
}'
```


예제 6.5 페이셋을 텀의 알파벳 오름차순으로 표시
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "query" : {
    "term" : { "name" : "seoul" }
  },
  "facets" : {
    "term_service" : {
      "terms" : { "field" : "service", "order" : "term" }
    }
  }
}'
```


###6.1.2 범위 페이셋

예제 6.6 stars 필드의 값을 범위별로 페이셋으로 표시
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "range_stars" : {
      "range" : {
        "field" : "stars",
        "ranges" : [ {"to": 3}, {"from": 3, "to": 5}, {"from": 5} ]
      }
    }
  }
}'
```


예제 6.6 stars 필드의 값을 범위별로 페이셋으로 표시 - 2
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "range_service" : {
      "range" : {
        "stars" : [ {"to": 3}, {"from": 3, "to": 5}, {"from": 5} ]
      }
    }
  }
}'
```


예제 6.7 stars 필드로 간격을 구분하고 price 값을 집계한 페이셋
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "range_stars" : {
      "range" : {
        "key_field" : "stars",
        "value_field" : "price",
        "ranges" : [ {"to": 3}, {"from": 3, "to": 5}, {"from": 5} ]
      }
    }
  }
}'
```


###6.1.3 히스토그램 페이셋

예제 6.8 rooms 필드의 값을 100 간격으로 구분한 페이셋
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "histo_rooms" : {
      "histogram" : {
        "field" : "rooms",
        "interval" : 100
      }
    }
  }
}'
```


예제 6.9 rooms 필드를 100 간격으로 구분하고 price 값을 집계한 페이셋
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "histo_rooms" : {
      "histogram" : {
        "key_field" : "rooms",
        "value_field" : "price",
        "interval" : 100
      }
    }
  }
}'
```


###6.1.4 날짜 히스토그램 페이셋

예제 6.10 checkin 필드의 값을 1개월 간격으로 구분한 페이셋
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "histo_checkin" : {
      "date_histogram" : {
        "field" : "checkin",
        "interval" : "month"
      }
    }
  }
}'
```


###6.1.5 필터와 질의 페이셋

예제 6.11 텀 페이셋 결과에 service : Spa 필터 적용
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "term_name" : {
      "terms" : { "field" : "service" },
      "facet_filter" : {
        "term" : { "service" : "Spa" }
      }
    }
  }
}'
```


예제 6.12 필터 페이셋을 사용해서 service : Spa인 텀 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "term_filter" : {
      "filter" : {
        "term" : { "service" : "Spa" }
      }
    }
  }
}'
```


예제 6.13 질의 페이셋을 사용해서 name이 seoul 또는 hotel인 값 검색
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "term_query" : {
      "query" : {
        "match" : { "name" : "seoul OR hotel" }
      }
    }
  }
}'
```


###6.1.6 통계 페이셋

예제 6.14 통계 페이셋을 사용해서 price 필드 정보 출력
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "my_facet" : {
      "statistical" : { "field" : "price" }
    }
  }
}'
```


예제 6.15 stars 필드별로 price 필드 정보 출력
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "my_facet" : {
      "terms_stats" : {
        "key_field" : "stars",
        "value_field" : "price"
      }
    }
  }
}'
```

###6.1.7 위치 거리 페이셋

예제 6.16 geo_distance 페이셋을 이용해 3km, 6km 간격으로 호텔 분리
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "geo" : {
      "geo_distance" : {
        "location" : {
          "lat" : 37.52,
          "lon" : 126.98
        },
        "ranges" : [
          { "to" : 3000 },
          { "from" : 3000, "to" : 6000 },
          { "from" : 6000, "to" : 9000 },
          { "from" : 9000 }
        ]
      }
    }
  }
}'
```


예제 6.17 거리 간격별 price 필드 값 계산
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "geo" : {
      "geo_distance" : {
        "location" : {
          "lat" : 37.52,
          "lon" : 126.98
        },
        "value_field" : "price",
        "ranges" : [
          { "to" : 3000 },
          { "from" : 3000, "to" : 6000 },
          { "from" : 6000, "to" : 9000 },
          { "from" : 9000 }
        ]
      }
    }
  }
}'
```


예제 6.18 km 단위를 이용해서 거리 간격별 price 필드 값 계산
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "facets" : {
    "geo" : {
      "geo_distance" : {
        "location" : {
          "lat" : 37.52,
          "lon" : 126.98
        },
        "value_field" : "price",
        "ranges" : [
          { "to" : 3 },
          { "from" : 3, "to" : 6 },
          { "from" : 6, "to" : 9 },
          { "from" : 9 }
        ],
        "unit" : "km"
      }
    }
  }
}'
```

##6.2 어그리게이션

###6.2.1 최소, 최대, 합, 평균, 개수 어그리게이션

예제 6.19 price 필드의 최소(min) 값
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_min" : {
      "min" : { "field" : "price" }
    }
  }
}'
```


예제 6.20 price 필드의 최대(max) 값
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_max" : {
      "max" : { "field" : "price" }
    }
  }
}'
```


예제 6.21 price 필드의 합(sum)
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_sum" : {
      "sum" : { "field" : "price" }
    }
  }
}'
```


예제 6.22 price 필드의 평균(avg)
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_avg" : {
      "avg" : { "field" : "price" }
    }
  }
}'
```


예제 6.23 price 필드의 개수(value_count)
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_cnt" : {
      "value_count" : { "field" : "price" }
    }
  }
}'
```


###6.2.2 상태, 확장 상태 어그리게이션

예제 6.24 price 필드의 상태 정보
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_stats" : {
      "value_stats" : { "field" : "price" }
    }
  }
}'
```


예제 6.25 price 필드의 확장 상태 정보
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "price_ex_stats" : {
      "extended_stats" : { "field" : "price" }
    }
  }
}'
```

###6.2.3 글로벌 어그리게이션

예제 6.26 name 필드에 seoul을 포함한 도큐먼트의 평균 price
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "query" : {
    "term" : { "name" : "seoul" }
  },
  "aggs" : {
    "avg_price" : {
      "avg" : { "field" : "price" }
    }
  }
}'
```


예제 6.27 global 어그리게이션 버킷에 담긴 도큐먼트의 평균 price
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "query" : {
    "term" : { "name" : "seoul" }
  },
  "aggs" : {
    "all_price" : {
      "global" : {},
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```

###6.2.4 필터, 누락 어그리게이션

예제 6.28 필터 어그리게이션 버킷에 담긴 도큐먼트의 price 평균
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "filter_name" : {
      "filter" : {
        "term" : { "name" : "seoul" }
      },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```


예제 6.29 service 필드가 존재하지 않는 도큐먼트의 price 평균
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "missing_service" : {
      "missing" : { "field" : "service" },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```


###6.2.5 텀 어그리게이션

예제 6.30 stars 필드값의 텀별로 price 필드 값 평균 계산
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "term_stars" : {
      "terms" : { "field" : "stars" },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```


예제 6.31 텀 명으로 내림차순 정렬
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "term_stars" : {
      "terms" : {
        "field" : "stars",
        "order" : { "_term" : "desc" }
      },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```


예제 6.32 avg_price 하위 어그리게이션 값으로 오름차순 정렬
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "term_stars" : {
      "terms" : {
        "field" : "stars",
        "order" : { "avg_price" : "asc" }
      },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```

###6.2.6 범위, 날짜 범위 어그리게이션

예제 6.33 rooms 필드의 0~500, 500~1000, 1000~ 간격으로 price 평균값 계산
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "range_room" : {
      "range" : {
        "field" : "rooms",
        "ranges" : [{"to":500}, {"from":500, "to":1000}, {"from":1000}]
      },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```


예제 6.34 range 어그리게이션 버킷에 키 속성 추가
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "range_room" : {
      "range" : {
        "field" : "rooms",
        "keyed" : true,
        "ranges" : [{"to":500}, {"from":500, "to":1000}, {"from":1000}]
      },
      "aggs" : {
        "avg_price" : {
          "avg" : { "field" : "price" }
        }
      }
    }
  }
}'
```


예제 6.35 date_range 어그리게이션을 이용해서 날짜 범위 구분
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "date_r_checkin" : {
      "date_range" : {
        "field" : "checkin",
        "format" : "yyyy-MM-dd",
        "ranges" : [ { "to" : "now-4M" }, { "from" : "now-4M" } ]
      }
    }
  }
}'
```


예제 6.36 date_range 어그리게이션을 이용해서 날짜 범위 구분
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "date_r_checkin" : {
      "date_range" : {
        "field" : "checkin",
        "format" : "yyyy-MM-dd",
        "ranges" : [ { "to" : "2014-03-05T12:30:45" }, { "from" : "2014-03-05T12:30:45" } ]
      }
    }
  }
}'
```

###6.2.7 히스토그램, 날짜 히스토그램 어그리게이션

예제 6.37 rooms 필드를 500 간격으로 구분한 히스토그램 어그리게이션
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "histo_rooms" : {
      "histogram" : {
        "field" : "rooms",
        "interval" : 500
      }
    }
  }
}'
```


예제 6.38 min_doc_count: 0으로 지정해서 값이 없는 구간 표시
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "histo_rooms" : {
      "histogram" : {
        "field" : "rooms",
        "interval" : 300,
        "min_doc_count" : 0
      }
    }
  }
}'
```


예제 6.39 stats 하위 어그리게이션의 sum 값을 오름차순으로 정렬
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "histo_rooms" : {
      "histogram" : {
        "field" : "rooms",
        "interval" : 300,
        "order" : { "state_rooms.sum" : "asc" }
      },
      "aggs" : {
        "state_rooms" : {
          "stats" : { }
        }
      }
    }
  }
}'
```


예제 6.40 날짜 히스토그램을 이용해 1개월 간격으로 checkin 필드 구분
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "histo_checkin" : {
      "date_histogram" : {
        "field" : "checkin",
        "interval" : "1M",
        "format" : "yyyy-MM-dd E"
      }
    }
  }
}'
```

###6.2.8 위치 거리, 위치 해시 그리드 어그리게이션

예제 6.41 3km 간격으로 위치 거리 어그리게이션 버킷 구분
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "geo_location" : {
      "geo_distance" : {
        "field" : "location",
        "origin" : "37.52, 126.98",
        "distance_type" : "plane",
        "unit" : "km",
        "ranges" : [{"to":3},{"from":3,"to":6},{"from":6,"to":9},{"from":9}]
      }
    }
  }
}'
```


예제 6.42 precision: 5 정밀도 값으로 위치 해시 그리드 구분
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "geohash_location" : {
      "geohash_grid" : {
        "field" : "location",
        "precision" : 5
      }
    }
  }
}'
```


예제 6.43 위도: 37~38 / 경도: 126~128에 속하는 도큐먼트 필터링
```
curl 'localhost:9200/hotels/_search?pretty' -d '
{
  "aggs" : {
    "geohash_filter" : {
      "filter" : {
        "geo_bounding_box" : {
          "location" : {
            "top_left" : "38.00, 126.00",
            "bottom_right" : "37.00, 127.00"
          }
        }
      },
      "aggs" : {
        "geoghash_location" : {
          "geohash_grid" : {
            "field" : "location",
            "precision" : 5
          }
        }
      }
    }
  }
}'
```
