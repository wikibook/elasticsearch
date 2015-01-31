#9. 분석

##9.1 분석

###9.1.1 분석 과정

###9.1.2 _analyze API

예제 9.1 _analyze API를 사용해서 whitespace 토크나이저 적용
```
curl -XPOST 'http://localhost:9200/_analyze?tokenizer=whitespace&pretty' -d 'Around the World in Eighty Days'
```


예제 9.2 lowercase 토큰필터 적용
```
curl -XPOST 'http://localhost:9200/_analyze?tokenizer=whitespace&filters=lowercase&pretty' -d 'Around the World in Eighty Days'
```


예제 9.3 stop 토큰필터 적용
```
curl -XPOST 'http://localhost:9200/_analyze?tokenizer=whitespace&filters=lowercase,stop&pretty' -d 'Around the World in Eighty Days'
```

###9.1.3 사용자 정의 분석기

예제 9.4 my_analyzer 분석기 생성
```
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
```


예제 9.5 my_analyzer 분석기를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze -d 'Around the World in Eighty Days'
```

##9.2 분석기

###9.2.1 standard 분석기

예제 9.6 standard 분석기 생성
```
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
```


예제 9.7 standard 분석기를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=standard&pretty' -d 'Around the World in Eighty Days'
```

###9.2.2 simple 분석기

###9.2.3 whitespace 분석기

###9.2.4 stop 분석기


예제 9.8 stopword_list.txt 파일에 스톱워드 in, the, world 입력
```
echo 'in
the
world' > config/stopword_list.txt
```


예제 9.9 stopword_list.txt 파일 내용을 스톱워드로 사용하도록 stop 분석기 설정
```
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
```


예제 9.10 stop 분석기를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=stop&pretty' -d 'Around the World in Eighty Days'
```

###9.2.5 keyword 분석기

###9.2.6 pattern 분석기

예제 9.11 대문자 [A-Z]를 패턴으로 토큰을 분할하도록 stop 분석기 설정
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "pattern" : {
          "type" : "pattern",
          "lowercase" : false,
          "pattern" : "[A-Z]"
        }
      }
    }
  }
}'
```


예제 9.12 pattern 분석기를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=pattern&pretty' -d 'Around the World in Eighty Days'
```


예제 9.13 숫자 \d를 패턴으로 토큰을 분할하도록 stop 분석기 설정
```
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
```


예제 9.14 pattern 분석기를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=pattern&pretty' -d 'Around the World in 80 Days'
```

###9.2.7 다국어 분석기

예제 9.15 영어(english) 분석기 적용
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "language" : {
          "type" : "english"
        }
      }
    }
  }
}'
```


예제 9.16 영어 분석기로 "삼국지(三國志)" 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=language&pretty' -d '삼국지(三國志)'
```


예제 9.17 cjk 분석기 적용
```
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
```


예제 9.18 cjk 분석기로 "삼국지(三國志)" 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=language&pretty' -d '삼국지(三國志)'
```

###9.2.8 snowball 분석기

예제 9.19 snowball 분석기 적용
```
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
```


예제 9.20 snowball 분석기로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=snowball&pretty' -d 'Around the World in Eighty Days'
```

##9.3 토크나이저

예제 9.21 standard 분석기 생성 - standard 토크나이저 적용
```
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
```


예제 9.22 사용자 정의 standard 토크나이저 생성 후 분석기에 적용
```
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
```

###9.3.1 standard 토크나이저

###9.3.2 nGram 토크나이저

예제 9.23 nGram 토크나이저를 my_analyzer 분석기에 적용
```
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
```


예제 9.24 nGram 토크나이저로 토큰 분할
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in 80 Days'
```


###9.3.3 edgeNGram 토크나이저

예제 9.25 edgeNGram 토크나이저를 my_analyzer 분석기에 적용
```
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
```


예제 9.26 edgeNGram 토크나이저로 토큰 분할
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in 80 Days'
```


###9.3.4 keyword 토크나이저

###9.3.5 letter 토크나이저

###9.3.6 lowercase 토크나이저

###9.3.7 whitespace 토크나이저

###9.3.8 pattern 토크나이저

###9.3.9 uax_url_email 토크나이저

###9.3.10 path_hierarchy 토크나이저

예제 9.27 path_hierarchy 토크나이저로 Program/Search/Elasticsearch 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Program/Search/Elasticsearch'
```


예제 9.28 replacement: "$", skip: 1 옵션 적용
```
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
          "type" : "path_hierarchy",
          "replacement" : "$",
          "skip" : 1
        }
      }
    }
  }
}'
```


예제 9.29 옵션 적용 후 Program/Search/Elasticsearch 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Program/Search/Elasticsearch'
```

###9.3.11 classic 토크나이저

##9.4 토큰필터


예제 9.30 standard 분석기 생성 - standard 토큰필터 적용
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["standard"]
        }
      }
    }
  }
}'
```


예제 9.31 standard 타입의 사용자 정의 토큰필터 생성 후 분석기에 적용
```
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
          "type" : "standard"
        }
      }
    }
  }
}'
```

###9.4.1 standard 토큰필터

###9.4.2 asciifolding 토큰필터

###9.4.3 length 토큰필터


예제 9.32 length 토큰필터로 길이가 최소 3, 최대 5인 토큰만 저장하도록 설정
```
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
          "type" : "length",
          "min" : 3, "max" : 5
        }
      }
    }
  }
}'
```


예제 9.33 길이가 최소 3 최대 5인 토큰들 저장
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'
```


###9.4.4 lowercase 토큰필터

###9.4.5 uppercase 토큰필터

###9.4.6 nGram 토큰필터

###9.4.7 edgeNGram 토큰필터

###9.4.8 porter_stem 토큰필터

###9.4.9 shingle 토큰필터

예제 9.34 최소, 최대 길이를 각각 3으로 shingle 토큰필터 적용
```
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
          "type" : "shingle",
          "max_shingle_size" : 3,
          "min_shingle_size" : 3
        }
      }
    }
  }
}'
```


예제 9.35 shingle 토큰필터를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'
```


예제 9.36 output_unigrams: false, token_separator: "-", filler_token: "*" 옵션 추가 적용
```
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
          "type" : "shingle",
          "max_shingle_size" : 3,
          "min_shingle_size" : 3,
          "output_unigrams" : false,
          "token_separator" : "-",
          "filler_token" : "*"
        }
      }
    }
  }
}'
```


예제 9.37 shingle 토큰필터를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'
```


###9.4.10 stop 토큰필터

###9.4.11 word_delimiter 토큰필터

예제 9.38 word_delimiter 토큰필터를 적용
```
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
```


예제 9.39 word_delimiter 토큰필터를 이용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d "Father's Wi-Fi SmartPhone, SD3000-12-Delux"
```

###9.4.12 stemmer 토큰필터

###9.4.13 keyword_marker 토큰필터

예제 9.40 keyword_marker 토큰필터와 stemmer 토큰필터를 적용
```
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
          "type" : "keyword_marker",
          "keywords" : ["swimming"]
        },
        "my_stemmer" : {
          "type" : "stemmer",
          "name" : "english"
        }
      }
    }
  }
}'
```


예제 9.41 keyword_marker 토큰필터와 stemmer 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Birds are flying, fishes are swimming, children are playing'
```

###9.4.14 keyword_repeat 토큰필터


예제 9.42 keyword_repeat 토큰필터와 stemmer 토큰필터를 적용
```
curl -XPUT 'http://localhost:9200/books' -d '
{
  "settings" : {
    "analysis" : {
      "analyzer" : {
        "my_analyzer" : {
          "tokenizer" : "standard",
          "filter" : ["keyword_repeat", "my_stemmer"]
        }
      },
      "filter" : {
        "my_stemmer" : {
          "type" : "stemmer",
          "name" : "english"
        }
      }
    }
  }
}'
```


예제 9.43 keyword_repeat 토큰필터와 stemmer 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Birds are flying, fishes are swimming, children are playing.'
```

###9.4.15 unique 토큰필터


예제 9.44 unique 토큰필터를 추가로 적용
```
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
        "my_stemmer" : { "type" : "stemmer", "name" : "english" },
        "my_unique" : { "type" : "unique" }
      }
    }
  }
}'
```


예제 9.45 unique 토큰필터를 추가로 적용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Birds are flying, fishes are swimming, children are playing.'
```

###9.4.16 snowball 토큰필터

###9.4.17 synonym 토큰필터

예제 9.46 synonym 토큰필터를 사용해서 동의어 설정
```
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
          "type" : "synonym",
          "synonyms" : [ "quick, fast", "jump, hop => hop"]
        }
      }
    }
  }
}'
```


예제 9.47 synonym 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'The Quick Rabbit Jumped'
```


예제 9.48 config/synonym.txt 파일에 동의어 사전 입력
```
echo 'quick, fast
jump, hop => hop' > config/synonym.txt
```


예제 9.49 config/synonym.txt 파일을 사용하도록 설정
```
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
          "type" : "synonym",
          "synonyms_path" : "synonym.txt"
        }
      }
    }
  }
}'
```

###9.4.18 합성어 토큰필터


예제 9.50 합성어 토큰필터의 분해어 base, ball 등록
```
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
          "type" : "dictionary_decompounder",
          "word_list" : ["base", "ball"]
        }
      }
    }
  }
}'
```


예제 9.51 합성어 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'I play baseball and basketball'
```

###9.4.19 reverse 토큰필터

예제 9.52 reverse 토큰필터 적용
```
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
```


예제 9.53 reverse 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'
```

###9.4.20 truncate 토큰필터

예제 9.54 truncate 토큰필터 적용
```
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
          "type" : "truncate",
          "length" : 4
        }
      }
    }
  }
}'
```


예제 9.55 truncate 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'
```

###9.4.21 trim 토큰필터

###9.4.22 limit 토큰필터

###9.4.23 hunspell 토큰필터

예제 9.56 hunspell 토큰필터 적용
```
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
          "type" : "hunspell",
          "locale" : "en_US"
        }
      }
    }
  }
}'
```

###9.4.24 cjk_bigram 토큰필터

예제 9.57 cjk_bigram 토큰필터 적용
```
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
```


예제 9.58 cjk_bigram 토큰필터로 "삼국지(三國志)" 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d '삼국지(三國志)'
```

###9.4.25 킵워드 토큰필터

예제 9.59 킵워드 the, Eighty, Days를 지정
```
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
          "type" : "keep",
          "keep_words" : ["the", "Eighty", "Days"]
        }
      }
    }
  }
}'
```


예제 9.60 킵워드 토큰필터로 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=my_analyzer&pretty' -d 'Around the World in Eighty Days'
```

##9.5 한글 형태소 분석기

예제 9.61 한글 형태소 분석기 플러그인 설치
```
bin/plugin -url https://dl-web.dropbox.com/spa/grpekzky9x5y6mc/elastic-analysis-korean/public/elasticsearch-analysis-korean-1.3.0.zip -install analysis-korean
```


예제 9.62 한글 형태소 분석기 korean_analyzer 등록
```
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
```


예제 9.63 한글 형태소 분석기를 적용해서 문장 분석
```
curl -XPOST 'http://localhost:9200/books/_analyze?analyzer=korean_analyzer&pretty' -d '동해물과 백두산이 마르고 닳도록'
```
