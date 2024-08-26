#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
USER app
WORKDIR /app
EXPOSE 8080

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
# Install clang/zlib1g-dev dependencies for publishing to native
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    clang zlib1g-dev
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
RUN dotnet nuget add source https://nuget.cloud.302.cl/v3/index.json -n nuget.cloud.302.cl
COPY ["BancoEstadoOperationsApi/BancoEstadoOperationsApi.csproj", "BancoEstadoOperationsApi/"]
RUN dotnet restore "./BancoEstadoOperationsApi/BancoEstadoOperationsApi.csproj"
COPY . .
WORKDIR "/src/BancoEstadoOperationsApi"
RUN dotnet build "./BancoEstadoOperationsApi.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./BancoEstadoOperationsApi.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=true

FROM mcr.microsoft.com/dotnet/runtime-deps:8.0 AS final
WORKDIR /app
EXPOSE 8080
COPY --from=publish /app/publish .
ENTRYPOINT ["./BancoEstadoOperationsApi"]