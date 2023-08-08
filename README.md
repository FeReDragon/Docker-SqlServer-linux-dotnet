## Docker-SQLServer-Linux-DotNet

🔥 **Docker com SQL Server para WebAPI com .NET** 🔥

Este guia apresenta um processo básico para criar uma WebAPI .NET que se conecta a um banco de dados SQL Server, tudo dentro de contêineres Docker... 🚀 

**Requisitos:**
- .NET SDK
- Docker
- Docker Compose

🛠️ **Configuração:**

**Estrutura do Projeto:**

1. Crie uma nova pasta para o projeto e, dentro dela, crie sua WebAPI .NET:

```bash
dotnet new webapi -n MinhaWebAPI
```

2. **Dockerfile** para a WebAPI:

No diretório da WebAPI, crie o Dockerfile:

```Dockerfile
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build 
WORKDIR /src 
COPY ["MinhaWebAPI.csproj", "./"] 
RUN dotnet restore "./MinhaWebAPI.csproj" 
COPY . . 
RUN dotnet build "MinhaWebAPI.csproj" -c Release -o /app/build

FROM build AS publish 
RUN dotnet publish "MinhaWebAPI.csproj" -c Release -o /app/publish
FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS final 
WORKDIR /app 
COPY --from=publish /app/publish . 
ENTRYPOINT ["dotnet", "MinhaWebAPI.dll"]
```

3. **Docker Compose:**

No diretório raiz, crie o `docker-compose.yaml`:

```yaml
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
```

4. **Configuração da WebAPI:**

Em `appsettings.json` da WebAPI:

```json
{ 
  ... 
  "ConnectionStrings": { 
    "ServerConnection": "Server=127.0.0.1,1433;Database=mydatabase;User Id=sa;Password=P@ssw0rd123!;Encrypt=False;" 
  } 
}
```

5. **Executando o Projeto:**

Execute:

```bash
docker-compose up --build
```

🎉 **Conclusão:**

Sua WebAPI .NET agora está rodando dentro de um contêiner Docker, conectada ao SQL Server em outro contêiner. Acesse a API em http://localhost:8001. O SQL Server estará ouvindo na porta 1433.
