# Elastic APM for monitoring .Net APIs

![image](https://github.com/spiffaz/APMdotNet/assets/35563797/3d9342ba-524d-4313-b883-cfedfa244abb)

This project was done as a test for the set up of an elasticsearch cluster (Elasticearch and Kibana) with APM.

It installs the APM .net agent on the basket api which can now be monitored (tracing) from the Kibana console.

The microservices-based .net application was forked from https://github.com/aspnetrun/run-aspnetcore-microservices.git

![image](https://github.com/spiffaz/APMdotNet/assets/35563797/bd724074-c8e3-48c7-b14a-41bb62f7016b)

Before running this, go to the Dockerfule in ``` Services/Basket/Basket.API/Dockerfile ```. Modify to your own keys (if using elastic cloud) or take out the key reference completely to use your local APM. 

They can also be updated as environment variables.

PS: The current keys in the config are no longer valid :)

## To run this, run the commands below in order:

```docker compose up config```

```docker compose -f docker-compose.yml -f docker-compose.override.yml up -d```


The first command sets up the elastic credentials
The second spins up all the resources

## The resources to be created are:

Logstash - (Actually not needed, can be commented out)

APM Server

Catalog API -> http://host.docker.internal:8000/swagger/index.html

Basket API -> http://host.docker.internal:8001/swagger/index.html

Discount API -> http://host.docker.internal:8002/swagger/index.html

Ordering API -> http://host.docker.internal:8004/swagger/index.html

Shopping.Aggregator -> http://host.docker.internal:8005/swagger/index.html

API Gateway -> http://host.docker.internal:8010/Catalog

Rabbit Management Dashboard -> http://host.docker.internal:15672 -- guest/guest

Portainer -> http://host.docker.internal:9000 -- admin/admin1234

pgAdmin PostgreSQL -> http://host.docker.internal:5050 -- admin@aspnetrun.com/admin1234

Elasticsearch -> http://host.docker.internal:9200

Kibana -> http://host.docker.internal:5601

Web Status -> http://host.docker.internal:8007

Web UI -> http://host.docker.internal:8006

See the official documentation on how to instrument a dot net application usuing a Dockerfile here - https://www.elastic.co/guide/en/apm/agent/dotnet/current/setup-auto-instrumentation.html#_docker_containers

I found this guide useful - https://medium.zenika.com/send-net-application-traces-to-elasticsearch-using-elastic-apm-rum-agent-d7ff111b1ef



Note - This was later modified to use elastic cloud on GCP instead of running APM locally because of issues experienced connecting APM to Elasticsearch.

If you are able to get it working, please share how for this to be updated or create a pull request.
