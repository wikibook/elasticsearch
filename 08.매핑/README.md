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

curl 'http://localhost:9200/books/_search?pretty'

curl 'http://localhost:9200/books/book/King%20Lear?pretty'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "mappings" : {
    "book" : {
      "_source" : {"enabled" : false}
    }
  }
}'

curl 'http://localhost:9200/books/_search?q=prince&pretty'

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


curl -XGET 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title","_timestamp"],
  "query" : {
    "term" : { "_all" : "prince" }
  }
}'


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


curl -XPUT 'http://localhost:9200/test_nums/test_num/1' -d '
{
  "num_val" : "hello"
}'

curl 'http://localhost:9200/test_nums/test_num/1'



curl 'localhost:9200/test_nums/_search?pretty' -d '
{
  "facets" : {
    "num_stat" : {
      "statistical" : { "field" : "num_val" }
    }
  }
}'


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

curl 'http://localhost:9200/test_users/_mapping?pretty'

curl -XPUT 'http://localhost:9200/test_users/test_user/1' -d '
{
  "user" : {
    "name" : "Kim", "age" : 30, "married" : true, "gender" : "male"
  }
}'

curl 'http://localhost:9200/test_users/test_user/1'

curl 'localhost:9200/test_users/_search?pretty' -d '
{
  "fields" : ["user.name","user.age","user.married","user.gender"]
}'


curl -XPUT 'http://localhost:9200/test_users/test_user/1' -d '
{
  "user" : [
    { "name" : "Kim", "age" : 30, "married" : true },
    { "name" : "Lee", "age" : 28, "married" : false }
  ]
}'

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

curl 'localhost:9200/test_users/_search?pretty' -d '
{
  "fields" : ["user.name","user.age","user.married"],
  "query" : {
    "term" : { "user.name" : "kim" }
  }
}'

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

curl 'http://localhost:9200/test_users/_search?q=kim%20AND%2030&pretty'

curl 'http://localhost:9200/test_users/_search?q=kim%20AND%20lee&pretty'

curl 'localhost:9200/test_users/_search?pretty' -d '
{
  "query" : {
    "term" : { "user.name" : "kim" }
  }
}'


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

curl -XDELETE http://localhost:9200/test_geos/

curl -XPUT http://localhost:9200/test_geos/ -d '
{
  "mappings" : {
    "test_geo" : {
      "properties" : {
        "name" : { "type" : "string" },
        "location" : {
          "type" : "geo_point",
          "normalize" : true
        }
      }
    }
  }
}'

curl -XPUT 'http://localhost:9200/test_geos/test_geo/1' -d '
{
  "name" : "Conrad Seoul",
  "location" : "37.525308, 126.926644"
  }
}'

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

curl 'http://localhost:9200/test_geos/_mapping?pretty'



curl 'http://localhost:9200/test_geos/_search?pretty' -d '
{
  "filter" : {
    "geo_distance" : {
      "distance" : "5km",
      "location" : { "lat" : 37.52, "lon" : 126.98 }
    }
  }
}'


curl 'localhost:9200/test_geos/_search?pretty' -d '
{
  "aggs" : {
    "geohash_location" : {
      "geohash_grid" : {
        "field" : "location",
        "precision" : 12
      }
    }
  }
}'

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

-- 위치 형태.

curl -XDELETE http://localhost:9200/test_geos/

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


curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title","title.tokens"],
  "query" : {
    "term" : { "title" : "the" }
  }
}'

curl -XDELETE 'http://localhost:9200/books'

curl -XPOST localhost:9200/_bulk --data-binary @5_1_books.json

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

curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title", "author", "pk_data"],
  "query" : {
    "term" : { "_all" : "prince" }
  }
}'

curl 'http://localhost:9200/books/_mapping?pretty'

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

curl 'http://localhost:9200/books/_search?pretty' -d '
{
  "fields" : ["title", "title_1", "title_2"],
  "query" : {
    "term" : { "_all" : "prince" }
  }
}'
