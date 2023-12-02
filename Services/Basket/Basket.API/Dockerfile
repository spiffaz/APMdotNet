#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.
ARG AGENT_VERSION=1.20.0

FROM mcr.microsoft.com/dotnet/aspnet:5.0-buster-slim AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:5.0-buster-slim AS build
ARG AGENT_VERSION
# install zip curl
RUN apt update && apt add zip wget

# pull down the zip file based on ${AGENT_VERSION} ARG and unzip
RUN wget -q https://github.com/elastic/apm-agent-dotnet/releases/download/v${AGENT_VERSION}/elastic_apm_profiler_${AGENT_VERSION}-linux-x64.zip && \
    unzip elastic_apm_profiler_${AGENT_VERSION}-linux-x64.zip -d /elastic_apm_profiler

WORKDIR /src
COPY ["Services/Basket/Basket.API/Basket.API.csproj", "Services/Basket/Basket.API/"]
COPY ["BuildingBlocks/EventBus.Messages/EventBus.Messages.csproj", "BuildingBlocks/EventBus.Messages/"]
COPY ["BuildingBlocks/Common.Logging/Common.Logging.csproj", "BuildingBlocks/Common.Logging/"]
RUN dotnet restore "Services/Basket/Basket.API/Basket.API.csproj"
COPY . .
WORKDIR "/src/Services/Basket/Basket.API"
RUN dotnet build "Basket.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Basket.API.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /elastic_apm_profiler
COPY --from=publish /elastic_apm_profiler .
WORKDIR /app
COPY --from=publish /app/publish .

# # Configures whether profiling is enabled for the currently running process.
ENV CORECLR_ENABLE_PROFILING=1
# # Specifies the GUID of the profiler to load into the currently running process.
ENV CORECLR_PROFILER={FA65FE15-F085-4681-9B20-95E04F6C03CC}
# # Specifies the path to the profiler DLL to load into the currently running process (or 32-bit or 64-bit process).
ENV CORECLR_PROFILER_PATH=/elastic_apm_profiler/libelastic_apm_profiler.so

# # Specifies the home directory of the profiler auto instrumentation. 
ENV ELASTIC_APM_PROFILER_HOME=/elastic_apm_profiler
# # Specifies the path to the integrations.yml file that determines which methods to target for auto instrumentation.
ENV ELASTIC_APM_PROFILER_INTEGRATIONS=/elastic_apm_profiler/integrations.yml
# # Specifies the log level at which the profiler should log. 
ENV ELASTIC_APM_PROFILER_LOG=warn

# Core configuration options / Specifies the service name (ElasticApm:ServiceName).
ENV ELASTIC_APM_SERVICE_NAME=NetApi-Elastic
# Core configuration options / Specifies the environment (ElasticApm:Environment)
ENV ELASTIC_APM_ENVIRONMENT=Development
# Core configuration options / Specifies the sample rate (ElasticApm:TransactionSampleRate).
# 1.0 : Dev purpose only, should be lowered in Production to reduce overhead.
ENV ELASTIC_APM_TRANSACTION_SAMPLE_RATE=1.0 

# Reporter configuration options / Specifies the URL for your APM Server (ElasticApm:ServerUrl).
ENV ELASTIC_APM_SERVER_URL=https://host.docker.internal:8200
# Reporter configuration options / Specifies if the agent should verify the SSL certificate if using HTTPS connection to the APM server (ElasticApm:VerifyServerCert). 
ENV ELASTIC_APM_VERIFY_SERVER_CERT=false 
# Reporter configuration options / Specifies the path to a PEM-encoded certificate used for SSL/TLS by APM server (ElasticApm:ServerCert).
# ENV ELASTIC_APM_SERVER_CERT=

# Supportability configuration options / Sets the logging level for the agent (ElasticApm:LogLevel).
ENV ELASTIC_APM_LOG_LEVEL=Debug

ENTRYPOINT ["dotnet", "Basket.API.dll"]