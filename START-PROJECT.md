### Running Services:

- PostgreSQL Database: Running on port 5432
- GraphQL Engine Server: Running on port 8080
- Hasura Console: Accessible at http://localhost:8080/console

### What You Can Do Now:

- Access the console at http://localhost:8080/console (should have opened in your browser)
- Start creating tables, relationships, and GraphQL APIs
- Test GraphQL queries and mutations

### To Stop the Services:

```bash
docker stop hasura-graphql-engine hasura-postgres
```

### To Restart the Services:

```bash
docker start hasura-postgres hasura-graphql-engine
```

### Optional:

For Development Console (if you want to develop the frontend):
If you want to develop the frontend console itself with hot-reloading:

1. Create a .env file in console-ce with:

```
NODE_ENV=development
NX_DATA_API_URL=http://localhost:8080
NX_SERVER_VERSION=v2.0.0
NX_CONSOLE_MODE=server
NX_IS_ADMIN_SECRET_SET=false
NX_HASURA_CONSOLE_TYPE=oss
```

2. Run:

```bash
cd frontend
npx nx run console-ce:serve
```

This will start the development server on http://localhost:4200 with hot-reloading.

The production console served by the GraphQL Engine at port 8080 is ready to use right now!

---

### To start the Docker containers again after stopping them, simply run:

```powershell
docker start hasura-postgres hasura-graphql-engine
```

### To verify they're running:

```powershell
docker ps
```

You should see both containers listed and their STATUS should show "Up".

### To check the console is accessible:

Open your browser and go to: **http://localhost:8080/console**

---

### Complete Docker Commands Reference:

**Start the containers:**
```powershell
docker start hasura-postgres hasura-graphql-engine
```

**Stop the containers:**
```powershell
docker stop hasura-graphql-engine hasura-postgres
```

**Restart the containers:**
```powershell
docker restart hasura-postgres hasura-graphql-engine
```

**Check container status:**
```powershell
docker ps
```

**View logs (if something isn't working):**
```powershell
docker logs hasura-graphql-engine
# or
docker logs hasura-postgres
```

**Remove containers completely (if you want to start fresh):**
```powershell
docker rm -f hasura-graphql-engine hasura-postgres
```

After removing, you'd need to recreate them with the original `docker run` commands from the setup.

---

## START OVER AGAIN

To start completely from scratch, follow these steps:

## 1. Clean up existing containers and networks

```powershell
# Stop and remove all Hasura-related containers
docker stop hasura-graphql-engine hasura-postgres 2>$null
docker rm hasura-graphql-engine hasura-postgres 2>$null

# Remove the network
docker network rm hasura-network 2>$null

# Optional: Remove any other containers from docker-compose
docker compose -f docker-compose.yaml -f docker-compose/databases.yaml down -v
```

## 2. Set up everything again

```powershell
# Navigate to project directory
cd "c:\Fahed\2022-2026 Engineering\Projects\graphql-engine"

# Start PostgreSQL
docker run -d --name hasura-postgres -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres -e POSTGRES_DB=postgres postgis/postgis:16-3.4

# Create network and connect PostgreSQL
docker network create hasura-network
docker network connect hasura-network hasura-postgres

# Start Hasura GraphQL Engine
docker run -d --name hasura-graphql-engine --network hasura-network -p 8080:8080 -e HASURA_GRAPHQL_DATABASE_URL="postgresql://postgres:postgres@hasura-postgres:5432/postgres" -e HASURA_GRAPHQL_ENABLE_CONSOLE=true hasura/graphql-engine:latest

# Wait a few seconds for startup
Start-Sleep -Seconds 5

# Verify everything is running
docker ps

# Open the console
Start-Process "http://localhost:8080/console"
```

## 3. If you also need to rebuild the frontend assets:

```powershell
cd frontend

# Make sure you're using Node 16
nvm use 16

# Install dependencies (if not already done)
yarn install

# Build console assets
yarn nx build-server-assets console-ce

# If the build-server-assets fails, manually copy:
if (Test-Path "dist/apps/server-assets-console-ce") { Remove-Item -Recurse -Force "dist/apps/server-assets-console-ce" }
Copy-Item -Recurse "dist/apps/console-ce" "dist/apps/server-assets-console-ce"
```

That's it! The console should be accessible at **http://localhost:8080/console**