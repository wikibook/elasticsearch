#8. 매핑

##8.1 매핑(_mapping) API

예제 8.1 books 인덱스의 매핑 확인
```
curl 'http://localhost:9200/books/_mapping?pretty'
```


예제 8.2 books/book 인덱스/타입에 read 필드 추가
```
curl -XPUT 'http://localhost:9200/books/_mapping/book' -d '
{
  "book" : {
    "properties" : {
      "read" : { "type" : "boolean" }
    }
  }
}'
```


예제 8.3 read 필드 추가 후 books 인덱스의 매핑 확인
```
curl 'http://localhost:9200/books/_mapping?pretty'
```

##8.2 내장필드

예제 8.4 인덱스 생성 시 내장필드 설정
```
PUT <호스트>/<인덱스> -d '
{
  "mappings" : {
    "<타입명>" : {
      "<내장필드명>" : {
        … <필드 내용> …
      }
    }
  }
}'
```


예제 8.5 인덱스 생성 후 내장필드 설정
```
PUT <호스트>/<인덱스>/_mapping/<타입> -d '
{
  "<타입명>" : {
    "<내장필드명>" : {
      … <필드 내용> …
    }
  }
}'
```


###8.2.1 _id

예제 8.6 _id 저장하도록 store 옵션 설정.
```
{
  "books" : {
    "_id" : {
      "index" : "not_analyzed",
      "store" : true
    }
  }
}
```


예제 8.7 books 인덱스의 id를 title 필드로 설정한 뒤 데이터 입력
```
curl -XDELETE 'http://localhost:9200/books'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_id" : { "path" : "title" }
    }
  }
}'

curl -XPOST localhost:9200/_bulk --data-binary @5_1_books.json
```


예제 8.8 새로 입력된 데이터 검색
```
curl 'http://localhost:9200/books/_search?pretty'
```


예제 8.9 아이디를 이용해서 도큐먼트 조회
```
curl 'http://localhost:9200/books/book/King%20Lear?pretty'
```

###8.2.2 _source

예제 8.10 books 인덱스의 _source 필드를 enabled : false로 설정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_source" : {"enabled" : false}
    }
  }
}'
```


예제 8.11 검색어 prince로 books 인덱스 검색
```
curl 'http://localhost:9200/books/_search?q=prince&pretty'
```


예제 8.12 title, author, category 필드만 저장하도록 지정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_source" : {
        "includes" : ["title", "author", "category"]
      }
    }
  }
}'
```


예제 8.13 검색어 prince로 검색
```
curl 'http://localhost:9200/books/_search?q=prince&pretty'
```


예제 8.14 p로 시작하는 필드는 저장하지 않도록 지정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_source" : {
        "excludes" : ["p*"]
      }
    }
  }
}'
```


예제 8.15 검색어 prince로 검색
```
curl 'http://localhost:9200/books/_search?q=prince&pretty'
```

###8.2.3 _all

예제 8.16 _all 내장필드를 이용해서 plot 필드는 검색하지 않도록 지정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_all" : { "enabled" : true },
      "properties" : {
        "title" : {
          "include_in_all" : true,
          "type" : "string"
        },
        "plot" : {
          "include_in_all" : false,
          "type" : "string"
        }
      }
    }
  }
}'
```


예제 8.17 검색어 prince로 검색
```
curl 'http://localhost:9200/books/_search?q=prince&pretty'
```

###8.2.4 _analyzer

예제 8.18 analyze_value 필드의 값을 분석기로 설정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_analyzer" : { "path" : " analyze_value" }
    }
  }
}'
```

###8.2.5 _timestamp

예제 8.19 _timestamp 내장필드를 이용해서 타임스탬프 저장
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_timestamp" : {
        "enabled" : true,
        "store" : true
      }
    }
  }
}'
```


예제 8.20 검색어 prince로 title 필드와 _timestamp 필드 검색
```
curl -XGET 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : [ "title" , "_timestamp" ],
  "query" : {
    "term" : { "_all" : "prince" }
  }
}'
```

###8.2.6 _ttl(time to live)

예제 8.21 _ttl 내장필드를 이용해서 2일 뒤 데이터가 삭제되도록 설정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_ttl" : {
        "enabled" : true,
        "default" : "2d"
      }
    }
  }
}'
```

##8.3 데이터 타입

###8.3.1 문자열

예제 8.22 필드 옵션을 적용해서 books 인덱스 매핑 생성
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "properties" : {
        "title" : { "type" : "string", "boost" : 2.0 },
        "category" : { "type" : "string", "index" : "not_analyzed" }
      }
    }
  }
}'
```


예제 8.23 title 필드에 boost 2.0 적용 후 prince로 검색
```
curl 'http://localhost:9200/books/_search?q=prince&pretty'
```


예제 8.24 category 필드를 science로 검색
```
curl 'http://localhost:9200/books/_search?q=category:science&pretty'
```


예제 8.25 category 필드를 Science fiction으로 검색
```
curl 'http://localhost:9200/books/_search?q=category:Science%20fiction&pretty'
```

###8.3.2 숫자

예제 8.26 num_val 필드의 ignore_malformed 옵션을 true로 설정
```
curl -XPUT 'http://localhost:9200/test_nums' -d '
{
  "mappings" : {
    "test_num" : {
      "properties" : {
        "num_val" : { "type" : "integer", "ignore_malformed" : true }
      }
    }
  }
}'
```


예제 8.27 num_val 필드 값에 hello 입력 후 확인
```
curl -XPUT 'http://localhost:9200/test_nums/test_num/1' -d '
{
  "num_val" : "hello"
}'

curl 'http://localhost:9200/test_nums/test_num/1'
```


예제 8.28 num_val 필드의 통계 페이셋 확인
```
curl 'localhost:9200/test_nums/_search?pretty' -d '
{
  "facets" : {
    "num_stat" : {
      "statistical" : { "field" : "num_val" }
    }
  }
}'
```

###8.3.3 날짜

###8.3.4 불린

###8.3.5 바이너리

###8.3.6 객체

예제 8.29 name, age, married 필드를 가지는 user 필드 값
```
{
  "user" : {
    "name" : "Kim",
    "age" : 30,
    "married" : true
  }
}
```


예제 8.30 test_users 인덱스에 객체 타입의 user 필드 매핑 설정
```
curl -XPUT 'http://localhost:9200/test_users' -d '
{
  "mappings" : {
    "test_user" : {
      "properties" : {
        "user" : {
          "type" : "object",
          "properties" : {
            "name" : { "type" : "string" },
            "age" : { "type" : "integer" },
            "married" : { "type" : "boolean" }
          }
        }
      }
    }
  }
}'
```


예제 8.31 user 필드에 gender 필드 값을 추가로 입력
```
curl -XPUT 'http://localhost:9200/test_users/test_user/1' -d '
{
  "user" : {
    "name" : "Kim", "age" : 30, "married" : true, "gender" : "male"
  }
}'
```


예제 8.32 test_users 인덱스의 매핑 확인
```
curl 'http://localhost:9200/test_users/_mapping?pretty'
```


예제 8.33 dynamic 옵션을 false로 설정
```
curl -XPUT 'http://localhost:9200/test_users' -d '
{
  "mappings" : {
    "test_user" : {
      "properties" : {
        "user" : {
          "type" : "object",
          "dynamic" : false,
          "properties" : {
            "name" : { "type" : "string" },
            "age" : { "type" : "integer" },
            "married" : { "type" : "boolean" }
          }
        }
      }
    }
  }
}'
```


예제 8.34 test_users 인덱스의 매핑 확인
```
curl 'http://localhost:9200/test_users/_mapping?pretty'
```


예제 8.35 test_users 인덱스의 매핑 확인
```
curl 'localhost:9200/test_users/_search?pretty' -d '
{
  "fields" : [ "user.name", "user.age", "user.married", "user.gender" ]
}'
```

###8.3.7 중첩

예제 8.36 user 필드의 name, age, married 하위 필드
```
{
  "user" : [
    { "name" : "Kim", "age" : 30, "married" : true },
    { "name" : "Lee", "age" : 28, "married" : false }
  ]
}
```


예제 8.37 객제 필드의 하위 필드가 실제로 저장되는 형태
```
{
  "user.name" : [ "Kim", "Lee" ],
  "user.age" : [ 30, 28 ],
  "user.married" : [ true, false ]
}
```


예제 8.38 test_users 인덱스에 user 필드의 하위 필드 데이터 입력
```
curl -XPUT 'http://localhost:9200/test_users/test_user/1' -d '
{
  "user" : [
    { "name" : "Kim", "age" : 30, "married" : true },
    { "name" : "Lee", "age" : 28, "married" : false }
  ]
}'
```


예제 8.39 must(AND) 조건으로 user.name : kim, user.age : 28 검색
```
curl 'http://localhost:9200/test_users/_search?pretty' -d '
{
  "query" : {
    "bool" : {
      "must" : [
        { "match" : { "user.name" : "kim" } },
        { "match" : { "user.age" : 28 } }
      ]
    }
  }
}'
```


예제 8.40 중첩 타입으로 user 필드의 매핑 설정
```
curl -XPUT 'http://localhost:9200/test_users' -d '
{
  "mappings" : {
    "test_user" : {
      "properties" : {
        "user" : {
          "type" : "nested",
          "properties" : {
            "name" : { "type" : "string" },
            "age" : { "type" : "integer" },
            "married" : { "type" : "boolean" }
          }
        }
      }
    }
  }
}'
```


예제 8.41 중첩 필드의 하위 필드들이 실제로 저장되는 형태
```
{
  "user.name" : "Kim",
  "user.age" : 30,
  "user.married" : true
},
{
  "user.name" : "Lee",
  "user.age" : 28,
  "user.married" : false
}
```


예제 8.42 nested 질의문 방법
```
{
  "nested" : {
    "path" : <중첩 타입의 상위 필드명>,
    "score_mode" : <스코어 방식>,
    "query" : {
      <질의문>
    }
  }
}
```


예제 8.43 must(AND) 조건으로 user.name : kim, user.age : 30 검색
```
curl 'localhost:9200/test_users/_search?pretty' -d '
{
  "query" : {
    "nested" : {
      "path" : "user",
      "score_mode" : "avg",
      "query" : {
        "bool" : {
          "must" : [
            { "match" : { "user.name" : "kim" } },
            { "match" : { "user.age" : 30 } }
          ]
        }
      }
    }
  }
}'
```


예제 8.44 must(AND) 조건으로 user.name : kim, user.age : 28 검색
```
curl 'localhost:9200/test_users/_search?pretty' -d '
{
  "query" : {
    "nested" : {
      "path" : "user",
      "score_mode" : "avg",
      "query" : {
        "bool" : {
          "must" : [
            { "match" : { "user.name" : "kim" } },
            { "match" : { "user.age" : 28 } }
          ]
        }
      }
    }
  }
}'
```

###8.3.8 좌표

예제 8.45 하위 필드 lat, lon의 숫자 값으로 입력
```
{
  "location" : {
    "lat" : 37.525308,
    "lon" : 126.926644
  }
}
```


예제 8.46 문자열로 입력. 위도-경도 순으로 입력
```
{
  "location" : "37.525308, 126.926644"
}
```


예제 8.47 배열로 입력. 경도-위도 순서로 입력
```
{
  "location" : [ 126.926644, 37.525308 ]
}
```


예제 8.48 위치 해시(geohash) 값으로 입력
```
{
  "location" : " wydm2r30v0mm"
}
```


예제 8.49 test_geos 인덱스의 매핑 설정
```
curl -XPUT http://localhost:9200/test_geos/ -d '
{
  "mappings" : {
    "test_geo" : {
      "properties" : {
        "name" : { "type" : "string" },
        "location" : { "type" : "geo_point" }
      }
    }
  }
}'
```


예제 8.50 name, location 값 입력
```
curl -XPUT 'http://localhost:9200/test_geos/test_geo/1' -d '
{
  "name" : "Conrad Seoul",
  "location" : "37.525308, 126.926644"
}'
```


예제 8.51 location 필드의 위도 37.53~37.52, 경도 126.92~126.93 범위의 값 검색
```
curl 'http://localhost:9200/test_geos/_search?pretty' -d '
{
  "filter" : {
    "geo_bounding_box" : {
      "location" : {
        "top_left" : { "lat" : 37.53, "lon" : 126.92 },
        "bottom_right" : { "lat" : 37.52, "lon" : 126.93 }
      }
    }
  }
}'
```


예제 8.52 name, location 값 입력
```
curl -XPUT 'http://localhost:9200/test_geos/test_geo/1' -d '
{
  "name" : "Conrad Seoul",
  "location" : "37.525308, 486.926644"
}'
```


예제 8.53 location 필드의 위도 37.53~37.52, 경도 126.92~126.93 범위의 값 검색
```
curl 'http://localhost:9200/test_geos/_search?pretty' -d '
{
  "filter" : {
    "geo_bounding_box" : {
      "location" : {
        "top_left" : { "lat" : 37.53, "lon" : 126.92 },
        "bottom_right" : { "lat" : 37.52, "lon" : 126.93 }
      }
    }
  }
}'
```


예제 8.54 normalize와 validate 옵션을 false로 매핑 설정
```
curl -XPUT http://localhost:9200/test_geos/ -d '
{
  "mappings" : {
    "test_geo" : {
      "properties" : {
        "name" : { "type" : "string" },
        "location" : {
          "type" : "geo_point",
          "normalize" : false,
          "validate" : false
        }
      }
    }
  }
}'
```


예제 8.55 location 필드의 위도 37.53~37.52, 경도 126.92~126.93 범위의 값 검색
```
curl 'http://localhost:9200/test_geos/_search?pretty' -d '
{
  "filter" : {
    "geo_bounding_box" : {
      "location" : {
        "top_left" : { "lat" : 37.53, "lon" : 126.92 },
        "bottom_right" : { "lat" : 37.52, "lon" : 126.93 }
      }
    }
  }
}'
```


예제 8.56 normalize와 validate 옵션을 false로 매핑 설정
```
curl -XPUT http://localhost:9200/test_geos/ -d '
{
  "mappings" : {
    "test_geo" : {
      "properties" : {
        "location" : {
          "type" : "geo_point"
          ,"geohash_prefix" : true
          ,"geohash_precision" : 10
        }
      }
    }
  }
}'
```


예제 8.57 위치 해시값 wydm2r30v0mm으로 검색
```
curl 'localhost:9200/test_geos/_search?pretty' -d '
{
  "query" : {
    "filtered" : {
      "query" : {
        "match_all" : {}
      },
      "filter" : {
        "geohash_cell": { "location": "wydm2r30v0mm" }
      }
    }
  }
}'
```


예제 8.58 위치 해시값 wydm2r30v 로 검색
```
curl 'localhost:9200/test_geos/_search?pretty' -d '
{
  "query" : {
    "filtered" : {
      "query" : {
        "match_all" : {}
      },
      "filter" : {
        "geohash_cell": { "location": "wydm2r30v" }
      }
    }
  }
}'
```


###8.3.9 위치 모형

예제 8.59 location 필드를 geo_shape 타입으로 매핑정보 설정
```
curl -XPUT http://localhost:9200/test_geos/ -d '
{
  "mappings" : {
    "test_geo" : {
      "properties" : {
        "location" : {
          "type" : "geo_shape",
          "precision": 10
        }
      }
    }
  }
}'
```


예제 8.60 점. -77.03653, 38.387676 좌표의 점을 나타냄
```
{
  "location" : {
    "type" : "point",
    "coordinates" : [-77.03653, 38.897676]
  }
}
```


예제 8.61 선. 좌표 -77.03653, 38.387676에서부터 -77.009051, 38.889939까지 연결된 선을 나타냄
```
{
  "location" : {
    "type" : "linestring",
    "coordinates" : [[-77.03653, 38.897676], [-77.009051, 38.889939]]
  }
}
```


예제 8.62 다각형. corrdinates 값은 2차 배열로 되어 있으며 1번째 배열 값은 좌표들을 연결한 형태의 전체 다각형을, 그리고 2번째 배열 값은 좌표들을 연결한 형태의 구멍이 1번째 배열의 다각형 안에 있음을 나타냄
```
{
  "location" : {
    "type" : "polygon",
    "coordinates" : [
      [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0] ],
      [ [100.2, 0.2], [100.8, 0.2], [100.8, 0.8], [100.2, 0.8], [100.2, 0.2] ]
    ]
  }
}
```


예제 8.63 다중점. 102.0, 2.0 좌표와 103.0, 2.0 좌표 두 개의 점을 나타냄
```
{
  "location" : {
    "type" : "multipoint",
    "coordinates" : [
      [102.0, 2.0], [103.0, 2.0]
    ]
  }
}
```


예제 8.64 다중선. 선 배열을 값으로 가지고 있는 2차 배열
```
{
  "location" : {
    "type": "MultiLineString",
    "coordinates": [
      [ [100.0, 0.0], [101.0, 1.0] ],
      [ [102.0, 2.0], [103.0, 3.0] ]
    ]
  }
}
```


예제 8.65 다중다각형. 다각형 2차 배열을 값으로 가지고 있는 3차 배열
```
{
  "location" : {
    "type" : "multipolygon",
    "coordinates" : [
      [ [[102.0, 2.0], [103.0, 2.0], [103.0, 3.0], [102.0, 3.0], [102.0, 2.0]] ],
      [
        [[100.0, 0.0], [101.0, 0.0], [101.0, 1.0], [100.0, 1.0], [100.0, 0.0]],
        [[100.2, 0.2], [100.8, 0.2], [100.8, 0.8], [100.2, 0.8], [100.2, 0.2]]
      ]
    ]
  }
}
```


예제 8.66 사각형. 두 좌표를 대칭점으로 하는 사각형이다.
```
{
  "location" : {
    "type" : "envelope",
    "coordinates" : [ [-45.0, 45.0], [45.0, -45.0] ]
  }
}
```


예제 8.67 원. coordinates 좌표를 중심으로 radius 반지름만큼의 크기를 가진 원을 나타냄
```
{
  "location" : {
    "type" : "circle",
    "coordinates" : [-45.0, 45.0],
    "radius" : "100m"
  }
}
```


예제 8.68 컬렉션. geometries 필드에 서로 다른 위치 형태의 값들을 배열로 입력
```
{
  "location" : {
    "type": "geometrycollection",
    "geometries": [
      {
        "type": "point",
        "coordinates": [100.0, 0.0]
      },
      {
        "type": "linestring",
        "coordinates": [ [101.0, 0.0], [102.0, 1.0] ]
      }
    ]
  }
}
```

##8.4 다중필드

###8.4.1 다중필드

예제 8.69 title 필드의 다중필드로 raw 필드 설정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "properties" : {
        "title" : {
          "type" : "string", "index" : "analyzed",
          "fields" : {
            "raw" : { "type" : "string", "index" : "not_analyzed"}
          }
        }
      }
    }
  }
}'
```


예제 8.70 title.raw 필드 검색
```
curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "query" : {
    "term" : { "title.raw" : "The Prince and the Pauper" }
  }
}'
```

###8.4.2 토큰 수

예제 8.71 title 필드의 토큰 수를 저장하는 tokens 다중 필드
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "properties" : {
        "title" : {
          "type" : "string", "index" : "analyzed",
          "fields" : {
            "tokens" : {
              "type" : "token_count",
              "store" : true,
              "analyzer" : "standard"
            }
          }
        }
      }
    }
  }
}'
```


예제 8.72 title 필드 검색어 the 로 title, title.tokens 필드 출력
```
curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title", "title.tokens"],
  "query" : {
    "term" : { "title" : "the" }
  }
}'
```


##8.4 필드 복사

예제 8.73 title, author 필드의 값을 pk_data 필드에 복사
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "properties" : {
        "title" : { "type" : "string", "copy_to" : "pk_data" },
        "author" : { "type" : "string", "copy_to" : "pk_data" },
        "pk_data" : { "type" : "string", "store" : true }
      }
    }
  }
}'
```


예제 8.74 title, author, pk_data 필드 값 확인
```
curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title", "author", "pk_data"],
  "query" : {
    "term" : { "_all" : "prince" }
  }
}'
```


예제 8.75 title 필드의 값을 title_1, title_2 필드에 복사
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "properties" : {
        "title" : { "type" : "string", "copy_to" : ["title_1", "title_2"] },
        "title_1" : { "type" : "string", "store" : true },
        "title_2" : { "type" : "string", "store" : true }
      }
    }
  }
}'
```


예제 8.76 title, title_1, title_2 필드 값 확인
```
curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title", "title_1", "title_2"],
  "query" : {
    "term" : { "_all" : "prince" }
  }
}'
```
