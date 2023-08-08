# Use a SDK image to build the app
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src
COPY ["MinhaWebAPI.csproj", "./"]
RUN dotnet restore "./MinhaWebAPI.csproj"
COPY . .
RUN dotnet build "MinhaWebAPI.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "MinhaWebAPI.csproj" -c Release -o /app/publish

# Use a runtime image to run the app
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "MinhaWebAPI.dll"]

