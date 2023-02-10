# 1. Add index books
PUT books
{
  "settings": 
  {
    "number_of_shards": 3,
    "number_of_replicas": 2
  }
}

# 2. Add custom book to books
PUT books/_doc/14602826
{
  "author_name":"Yearsley, Ann",
  "title": "Poems on several occasions",
  "country": "England",
  "language": "English",
  "year": 1786
}

# check it
GET books/_doc/14602826

# 3. Dynamically created mapping
GET books/_mapping

# 4. Delete index
DELETE books

# 5. 
# Create an index with customized mapping
# 1. Author name and title should be used for full-text search; country, language and year should be used for filtering (DocValues)

PUT books
{
  "settings": 
  {
    "number_of_shards": 3,
    "number_of_replicas": 2
  },
  "mappings": {
    "properties": {
      "author_name": {
        "type": "text"
      },
      "title": {
        "type": "text"
      },
      "language": {
        "type": "keyword"
      },
      "country": {
        "type": "keyword"
      },
      "year": {
        "type": "long"
      }
    }
  }
}
GET books/_mapping

# 6. In preparation of inserting a bulk of data, disable automatic refreshing
PUT books/_settings
{
  "index": {
    "number_of_replicas" : 2,
    "refresh_interval": -1
  }
}

# 7. Insert 10 books in bulk
POST _bulk
{ "index" : { "_index" : "books", "_id" : "14602830"}}
{"author_name": "A, T.", "title": "A Satyr against Vertue. (A poem: supposed to be spoken by a Town-Hector )", "country": "England", "language": "English", "year": "1679"}
{ "index" : { "_index" : "books", "_id" : "14602831"}}
{"author_name": "A, T.", "title": "The Aeronaut, a poem; founded almost entirely, upon a statement, printed in the newspapers, of a voyage from Dublin, in October, 1812", "country": "Ireland", "language": "English", "year": "1816"}
{ "index" : { "_index" : "books", "_id" : "14602832"}}
{"author_name": "Albert, Prince Consort, consort of Victoria, Queen of Great Britain", "title": "The Prince Albert, a poem", "country": "Ireland", "language": "English", "year": "1868"}
{ "index" : { "_index" : "books", "_id" : "14602833"}}
{"author_name": "Anslow, Robert", "title": "The Defeat of the Spanish Armada, A.D. 1588. A tercentenary ballad, A.D. 1888", "country": "England", "language": "English", "year": "1888"}
{ "index" : { "_index" : "books", "_id" : "14602834"}}
{"author_name": "Anslow, Robert", "title": "A Familiar Answer to a Familiar Letter", "country": "England", "language": "English", "year": "1720"}
{ "index" : { "_index" : "books", "_id" : "14602835"}}
{"author_name": "Anslow, Robert", "title": "The Irish Home Rule Bill. A poetical pamphlet, etc", "country": "England", "language": "English", "year": "1893"}
{ "index" : { "_index" : "books", "_id" : "14602836"}}
{"author_name": "Anslow, Robert", "title": "Confessions of a Coquette, while staying at Scarboro, Whitby, & Bridlington. By Azucena", "country": "England", "language": "English", "year": "1888"}
{ "index" : { "_index" : "books", "_id" : "14602837"}}
{"author_name": "Bellamy, James William", "title": "Jonah. The Seatonian Prize Poem for the year 1815", "country": "England", "language": "English", "year": "1815"}
{ "index" : { "_index" : "books", "_id" : "14602838"}}
{"author_name": "Brabant, Henry, Sir", "title": "The Eve of the Revolution; in Newcastle-upon-Tyne. (The Case of Sir Henry Brabant, knt, Mayor of Newcastle upon Tyne, most humbly offered to your Majesties Royal consideration.)", "country": "England", "language": "English", "year": "1848"}
{ "index" : { "_index" : "books", "_id" : "14602839"}}
{"author_name": "Brierley, Benjamin", "title": "Humorous Rhymes", "country": "England", "language": "English", "year": "1889"}

#8. Get the number of documents that are now in your index. Can you guess the right number?
GET books/_count
#returns 0 as we have disabled refresh
GET books/_refresh
get books/_count

# 9. Perform three queries with keyword of your choice:
  # 1. Full-text
  # 2. Exact match
  # 3. Range on years
  
# 1. full text (or)
GET books/_search
{
  "query": {
    "match": {
      "title": {
        "query": "the irish home"
      }
    }
  }
}

# 2. Exact Match (and)
GET books/_search
{
  "query": {
    "match_phrase": {
      "title": {
        "query": "The Irish Home"
      }
    }
  }
}

# 3. Range search
GET books/_search
{
  "query": {
    "range": {
      "year": {
        "gte": 1850,
      "lte": 2000
      }
    }
  }
}
