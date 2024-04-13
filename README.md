OpenTelemetryTestApp

Following this blog
[https://learn.microsoft.com/en-us/dotnet/core/diagnostics/observability-with-otel](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/observability-with-otel)


## My Steps
I'm using docker setup to test this. So here are my docker commands for creating the necessary containers.

# Network for separation
```
docker network create -d bridge minhazul-net
```

# App

### At first we'll need to build our app. Use these command to build and run the app
```
docker build -t i_otel_test .

docker run -dit --name otel_test --network minhazul-net -p15070:80 i_otel_test
```
GOTO: http://localhost:15070

To test nested greetings: http://localhost:15070/NestedGreeting?nestlevel=0
**Unfortunately nested greetings more than nestLevel 0 doesn't work for docker configuration.
To test this we can just run the app using `dotnet run` and hit `http://localhost:5070/NestedGreeting?nestlevel=5`

# Prometheus
```
docker run -d -it -p 9090:9090 --network minhazul-net --name=prometheus -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

GOTO: http://localhost:9090
Check this endpoint to see if your app's metrics page is working: http://localhost:9090/targets


# Grafana
```
docker run -dit -p 3000:3000 --network minhazul-net --name grafana -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource"  grafana/grafana-enterprise
```

GOTO: http://localhost:3000
Default username is `admin` default password is `admin`
To add prometheus as a datasource use this url: http://prometheus:9090

# Jaegar
```
docker run -dit --name jaeger --network minhazul-net -e COLLECTOR_ZIPKIN_HOST_PORT=:9411 -e COLLECTOR_OTLP_ENABLED=true -p 16686:16686 -p 4317:4317 jaegertracing/all-in-one:1.56
```

GOTO: http://localhost:16686