
Setting up an ELK stack

One of the more popular logging stacks is composed of Elasticsearch, Logstash and Kibana or ELK. The following example lab demonstrates how to set up an example deployment which can be used for logging.  

We are all about containers, and our logging solution will be container-ized!
First, lets create some space to put some data

docker volume create --name orca-elasticsearch-data

We haven't composed our containers so that you can see and start each container separately

#docker run -d 
 --name elasticsearch 
 -v orca-elasticsearch-data:/usr/share/elasticsearch/data 
 elasticsearch elasticsearch -Des.network.host=0.0.0.0

#docker run -d 
 -p 514:514 
 --name logstash 
 --link elasticsearch:es 
 logstash 
 sh -c "logstash -e 'input { syslog { } } output { stdout { } elasticsearch { hosts => [ \"es\" ] } } filter { json { source => \"message\" } }'"

#docker run -d 
 --name kibana 
 --link elasticsearch:elasticsearch 
 -p 5601:5601 
 kibana

You can then browse to your host system running kibana, port 5601 and browse log/event entries. You should specify the "time" field for indexing.
Note: When deployed in production, you should secure kibana (not described in this doc)

Some Example Searches

Here are a few examples demonstrating some ways to view the aggregated log data:

-- Show all the modifications on the system

type:"api" AND (tags:"post" OR tags:"put" OR tags:"delete")

-- Show all access from a given user

username:"admin"

-- Show all authentication failures on the system

type:"auth fail" 


Now that we have a logging solution setup, we can later instruct DUCP to use our logging solution to aggregate logging.
