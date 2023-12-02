This project was done as a test for the set up of an elasticsearch cluster (Elasticearch and Kibana) with APM.

It installs the APM .net agent on the basket api which can now be monitored (tracing) from the Kibana console.

THe microservices based .net application was forked from https://github.com/aspnetrun/run-aspnetcore-microservices.git

To run this, run the commands below in order

docker compose up config
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d


The first command sets up the elastic credentials
The second spins up all the resources



Note - This was later modified to use elastic cloud on GCP instead of running APM locally because of issues experienced connecting APM to Elasticsearch.

If you are able to get it working, please share how for this to be updated or create a pull request.