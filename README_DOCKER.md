# Docker Usage Guide for My Quasar Express App

This guide explains how to build and run the application using Docker and Docker Compose.

## Prerequisites

- [Docker](https://www.docker.com/products/docker-desktop) installed and running.
- `docker-compose` (included with Docker Desktop).

## Quick Start

The easiest way to run the entire stack (Frontend + Backend + Database if configured) is using Docker Compose.

1.  **Build and Run**:
    ```powershell
    docker-compose up --build -d
    ```
    - `--build`: Forces a rebuild of images (important after code changes).
    - `-d`: Detached mode (runs in background).

2.  **View Logs**:
    ```powershell
    docker-compose logs -f
    ```

3.  **Stop Services**:
    ```powershell
    docker-compose down
    ```

## Manual Build (Optional)

If you need to build generic images individually:

**Backend**:
```powershell
cd backend
docker build -t my-app-backend .
```

**Frontend**:
```powershell
cd frontend
docker build -t my-app-frontend .
```

## Important Notes

### Backend
- **Environment Variables**: ensure `.env` file exists in `backend/` or variables are passed via `docker-compose.yml`.
- **Database**: The current setup expects a database connection (e.g., PostgreSQL). Ensure your `DATABASE_URL` is correct.
- **Port**: Runs on port `3000` internally.

### Frontend
- **API URL**: The frontend communicates with the backend via `http://backend:3000` (internal Docker DNS) or exposed localhost port depending on configuration.
- **Port**: Serves on port `80` internally, mapped to `8080` on host (can be changed in `docker-compose.yml`).

### Troubleshooting
- If `npm ci` fails during build, try deleting `package-lock.json` and running `npm install` locally to regenerate it, then rebuild.
- If database connection fails, check `docker-compose logs backend`.
