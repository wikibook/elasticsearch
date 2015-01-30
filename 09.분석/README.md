curl -XDELETE 'http://localhost:9200/books'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "whitespace",
          "filter" : [ "lowercase", "stop" ]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "standard" : {
          "type" : "standard",
          "stopwords" : [ "in", "the", "world" ],
          "max_token_length" : 512
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=standard&pretty' -d 'Around the World in Eighty Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "standard" : {
          "type" : "keyword"
        }
      }
    }
  }
}'

echo 'in
the
world' > config/stopword_list.txt

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "stop" : {
          "type" : "stop",
          "stopwords_path" : "stopword_list.txt"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=stop&pretty' -d 'Around the World in Eighty Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "pattern" : {
          "type" : "pattern",
          "pattern" : "\\d"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=pattern&pretty' -d 'Around the World in 80 Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "language" : {
          "type" : "cjk"
        }
      }
    }
  }
}'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "language" : {
          "tokenizer":  "standard",
          "filter": [ "cjk_width", "cjk_bigram" ]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=language&pretty' -d '삼국지(三國志)'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "snowball" : {
          "type" : "snowball",
          "pattern" : "english"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=snowball&pretty' -d 'Around the World in Eighty Days'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "type" : "standard"
        }
      }
    }
  }
}'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "my_tokenizer"
        }
      },
      "tokenizer" : {
        "my_tokenizer" : {
          "type" : "standard"
        }
      }
    }
  }
}'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "my_ngram"
        }
      },
      "tokenizer" : {
        "my_ngram" : {
          "type" : "nGram",
          "min_gram" : "2",
          "max_gram" : "3",
          "token_chars": [ "letter", "digit" ]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Aro!und the World in 8!0 Days'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "my_edgeNGram"
        }
      },
      "tokenizer" : {
        "my_edgeNGram" : {
          "type" : "edgeNGram",
          "min_gram" : "2",
          "max_gram" : "4",
          "token_chars": [ "letter", "digit" ]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in 80 Days'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "my_hierarcy"
        }
      },
      "tokenizer" : {
        "my_hierarcy" : {
          "type" : "path_hierarchy"
          , "replacement" : "$"
          , "skip" : 1
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Program/Search/Elasticsearch'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard"
        }
      }
    }
  }
}'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["my_tokenfilter"]
        }
      },
      "filter" : {
        "my_tokenfilter" : {
          "type" : "length"
          , "min" : 3
          , "max" : 5
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["stop", "my_tokenfilter"]
        }
      },
      "filter" : {
        "my_tokenfilter" : {
          "type" : "shingle"
          ,"max_shingle_size" : 3
          ,"min_shingle_size" : 3
          ,"output_unigrams" : false
          ,"token_separator" : "-"
          ,"filler_token" : "*"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "whitespace",
          "filter" : ["my_tokenfilter"]
        }
      },
      "filter" : {
        "my_tokenfilter" : {
          "type" : "word_delimiter"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d "Father's Wi-Fi SmartPhone, SD3000-12-Delux"


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["my_keyword", "my_stemmer"]
        }
      },
      "filter" : {
        "my_keyword" : {
          "type" : "keyword_marker"
          ,"keywords" : ["swimming"]
        },
        "my_stemmer" : {
          "type" : "stemmer"
          , "name" : "english"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Birds are flying, fishes are swimming, children are playing.'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["keyword_repeat", "my_stemmer", "my_unique"]
        }
      },
      "filter" : {
        "my_stemmer" : {
          "type" : "stemmer"
          , "name" : "english"
        },
        "my_unique" : {
          "type" : "unique"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Birds are flying, fishes are swimming, children are playing.'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["snowball", "lowercase", "my_filter"]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "synonym"
          ,"synonyms" : [ "quick, fast", "jump, hop => hop"]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'The Quick Rabbit Jumped'

echo 'quick, fast
jump, hop => hop' > config/synonym.txt

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["snowball", "lowercase", "my_filter"]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "synonym"
          ,"synonyms_path" : "synonym.txt"
        }
      }
    }
  }
}'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["my_filter"]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "dictionary_decompounder"
          ,"word_list" : ["base", "ball"]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'I play baseball and basketball'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["reverse"]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : [ "my_filter" ]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "truncate"
          ,"length" : 4
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : [ "my_filter" ]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "hunspell"
          ,"locale" : "en_US"
          ,"dedup" : true
        }
      }
    }
  }
}'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : [ "my_filter" ]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "cjk_bigram"
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d '삼국지(三國志)'

curl -XPOST 'http://localhost:9200/_analyze?pretty' -d '삼국지(三國志)'

curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : [ "my_filter" ]
        }
      },
      "filter" : {
        "my_filter" : {
          "type" : "keep"
          ,"keep_words" : ["the", "Eighty", "Days"]
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'


curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "standard" : {
          "type" : "standard",
          "stopwords" : [ "in", "the", "world" ],
          "max_token_length" : 512
        }
      }
    }
  }
}'

bin/plugin -url https://dl-web.dropbox.com/spa/grpekzky9x5y6mc/elastic-analysis-korean/public/elasticsearch-analysis-korean-1.3.0.zip -install analysis-korean
-> Installing analysis-korean...
Trying https://dl-web.dropbox.com/spa/grpekzky9x5y6mc/elastic-analysis-korean/public/elasticsearch-analysis-korean-1.3.0.zip...
Downloading .............................DONE
Installed analysis-korean into /home/ec2-user/elasticsearch-1.3.2/plugins/analysis-korean

curl -XPUT http://localhost:9200/books -d '
{
  "settings" : {
    "index": {
      "analysis": {
        "analyzer": {
          "korean_analyzer": {
            "type": "kr_analyzer"
          }
        }
      }
    }
  }
}'

curl -XPOST 'http://localhost:9200/_analyze?pretty' -d '동해물과 백두산이 마르고 닳도록'

curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=korean_analyzer&pretty' -d '동해물과 백두산이 마르고 닳도록'
