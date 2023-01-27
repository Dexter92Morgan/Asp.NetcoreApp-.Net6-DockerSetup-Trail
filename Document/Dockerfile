FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build-env
WORKDIR /App

# Copy everything
COPY . ./
# Restore as distinct layers
RUN dotnet nuget add source https://edgedev.myget.org/F/edgenuity/auth/69af20c8-a4c1-4dfb-a5dc-10c17017143a/api/v3/index.json -n Edgenuity
RUN dotnet restore
# Build and publish a release
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:6.0
WORKDIR /App
COPY --from=build-env /App/out .

ENTRYPOINT ["dotnet", "Edgenuity.Armstrong.ALA.User.API.dll","--environment=Local"]
---------------------------------------------------------------------------------------------------

# Stage 1
FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build
WORKDIR /build
COPY . .
RUN dotnet restore
RUN dotnet publish -c Release -o /app
# Stage 2
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1 AS final
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "Core3Api.dll"]
---------------------------------------------------------------

#See https://aka.ms/containerfastmode to understand how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /src
COPY ["Docker_Trail.csproj", "."]
RUN dotnet restore "./Docker_Trail.csproj"
COPY . .
WORKDIR "/src/."
RUN dotnet build "Docker_Trail.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Docker_Trail.csproj" -c Release -o /app/publish

FROM base AS final	
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Docker_Trail.dll"]