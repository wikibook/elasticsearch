input {
  stdin {
    codec => json
  }
}

filter {
  if [os] == "Macintosh" {
    drop {}
  }
}

output {
  stdout {
    codec => json
  }
}


{ "title": "Romeo and Juliet", "author": "William Shakespeare", "category":"Tragedies" }
{ "title": "Hamlet", "author": "William Shakespeare", "category":"Tragedies" }


input {
  stdin { codec => json }
}

filter {
  if [title] == "Hamlet" {
    mutate {
      replace => [ "title", "This is a story about %{title}" ]
    }
  }
}

output {
  stdout { codec => json }
}
