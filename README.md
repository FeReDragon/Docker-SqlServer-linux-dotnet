# Docker-SqlServer-linux-dotnet

Docker com SQL Server para WebAPI com .NET

Este guia apresenta um processo básico para criar uma WebAPI .NET que se conecta a um banco de dados SQL Server, tudo dentro de contêineres Docker.
Requisitos:

    .NET SDK
    Docker
    Docker Compose

Configuração:

1. Estrutura do Projeto:

Crie uma nova pasta para o projeto e, dentro dela, crie sua WebAPI .NET:

dotnet new webapi -n MinhaWebAPI

2. Dockerfile para a WebAPI:

Dentro do diretório da WebAPI, crie um arquivo chamado Dockerfile com o seguinte conteúdo:

Dockerfile

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

3. Docker Compose:

No diretório raiz do projeto, crie um arquivo docker-compose.yaml com o seguinte conteúdo:

version: '3.4'
services:
  minha-webapi:
    image: minha-webapi:latest
    build:
      context: .
      dockerfile: Dockerfile
    environment:
      - ConnectionStrings__ServerConnection=Server=127.0.0.1,1433;Database=mydatabase;User Id=sa;Password=P@ssw0rd123!;Encrypt=False;
    depends_on:
      - sql_server
    ports:
      - "8001:80"
  
  sql_server:
    image: mcr.microsoft.com/mssql/server
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=P@ssw0rd123!
    ports:
      - "1433:1433"


4. Configuração da WebAPI:

No arquivo appsettings.json da WebAPI, adicione:

json

{
  ...
  "ConnectionStrings": {
    "ServerConnection": "Server=127.0.0.1,1433;Database=mydatabase;User Id=sa;Password=P@ssw0rd123!;Encrypt=False;"
  }
}

5. Executando o Projeto:

Na pasta raiz do projeto, execute:

bash

docker-compose up --build

Conclusão:

Agora você deve ter uma WebAPI .NET rodando em um contêiner Docker e conectada a um banco de dados SQL Server em outro contêiner. Você pode acessar a API pelo endereço http://localhost:8001 e o banco de dados SQL Server estará escutando na porta 1433.