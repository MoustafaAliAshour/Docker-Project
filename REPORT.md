## Building Images
```bash
# Build database image
docker build -t <mostafaashour>/oslab-mysql ./db

# Build backend image
docker build -t <mostafaashour>/oslab-backend ./backend

# Build frontend image
docker build -t <mostafaashour>/oslab-frontend ./frontend
```

### Pushing to Docker Hub
```bash
docker login

docker push <mostafaashour>/oslab-mysql
docker push <mostafaashour>/oslab-backend
docker push <mostafaashour>/oslab-frontend
```

### Running with Docker Compose
```bash
docker compose up

# Start in detached mode
docker compose up -d

# Stop all services
docker compose down

# Stop and remove volumes
docker compose down -v
```

---

## Implementation Details

### Task 1: Database Containerization
- Used official `mysql:8.0` base image
- Configured MySQL with root password and database name via environment variables
- Copied `init.sql` to `/docker-entrypoint-initdb.d/` for automatic schema initialization
- The users table is created and populated with initial data on first startup

### Task 2: Backend Containerization
- Used `node:18-alpine` for a lightweight image
- Installed all required npm packages: express, mysql2, cors, body-parser, csv-writer
- Exposed port 3000 for API access
- Configured environment variables for database connection (DB_HOST, DB_USER, DB_PASSWORD, DB_NAME)
- Created `/app/data` directory for CSV file exports

### Task 3: Frontend Containerization
- Used `node:18-alpine` base image
- Configured Vite dev server to bind to `0.0.0.0` for external access
- Exposed port 5173 for browser access
- Frontend communicates with backend at `http://localhost:3000`

### Task 4: Docker Registry
- Tagged all images with Docker Hub username convention
- Logged into Docker Hub via CLI
- Successfully pushed all three images to public repository

### Bonus: Docker Compose & Volumes

#### Networking
- Created custom bridge network `app_network` for inter-service communication
- Services communicate using service names as hostnames (e.g., `db`, `backend`)

#### Persistence
- **Named Volume (`db_data`)**: Stores MySQL data at `/var/lib/mysql`, ensuring data survives container deletion
- **Bind Mount (`./downloads`)**: Maps local `downloads` folder to `/app/data` in backend container

#### Features Implemented
1. **Service Dependencies**: Backend waits for database health check, frontend waits for backend
2. **Health Checks**: Database has mysqladmin ping health check
3. **CSV Export**: Clicking "Save to CSV" saves file to local `./downloads` folder on host machine

---

## Testing & Verification

### Functionality Tests
1. Database initialization successful with schema and default user
2. Backend connects to database and serves API on port 3000
3. Frontend accessible at `http://localhost:5173`
4. Can add new users through the web interface
5. CSV export creates file in local `./downloads` folder
6. Data persists after `docker compose down` and `docker compose up`

### Access Points
- Frontend: `http://localhost:5173`
- Backend API: `http://localhost:3000`
- Database: `localhost:3306` (from host)

---

## Challenges & Solutions

### Challenge 1: Frontend Network Access
**Problem:** Vite dev server only bound to localhost inside container  
**Solution:** Added `--host 0.0.0.0` flag to npm dev command

### Challenge 2: Backend-Database Connection
**Problem:** Backend couldn't resolve database hostname  
**Solution:** Used Docker Compose networking with service name `db` as hostname

### Challenge 3: CSV File Access
**Problem:** Exported CSV only existed inside container  
**Solution:** Implemented bind mount mapping `./downloads` to `/app/data`

---

## Conclusion

This lab successfully demonstrated OS-level virtualization concepts including:
- Process isolation through containerization
- Inter-container networking using Docker networks
- Data persistence using volumes and bind mounts
- Multi-container orchestration with Docker Compose
