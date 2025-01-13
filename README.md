# Nginx Playground

This project sets up a simple Nginx server using Docker. It proxies requests to a backend application running on your host machine at port 3000.

## Features
- Proxies requests from Nginx to a backend running on `http://host.docker.internal:3000`.
- Logs are stored in the `logs/` directory.
- Configurable through the `nginx-config/` directory.

## Prerequisites
- [Docker](https://www.docker.com/) installed on your machine.

## Getting Started

### 1. Clone the repository
```bash
git clone <repository-url>
cd <repository-folder>
```

### 2. Create the Nginx configuration
Create a configuration file in the `nginx-config/` directory. For example:
```bash
mkdir -p nginx-config
cat <<EOL > nginx-config/default.conf
server {
  listen 80;
  listen [::]:80;
  server_name example.com;

  location / {
    proxy_pass http://host.docker.internal:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade \$http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host \$host;
    proxy_cache_bypass \$http_upgrade;
  }
}
EOL
```

### 3. Run the Docker Compose setup
Start the Nginx container:
```bash
docker-compose up -d
```

### 4. Verify the setup
- Access your Nginx server in your browser or using a tool like `curl` at `http://localhost`.
- Ensure that the backend application is running on `http://host.docker.internal:3000`.

### 5. View Logs
Logs from Nginx are stored in the `logs/` directory. You can inspect them using:
```bash
tail -f logs/access.log
```

## Directory Structure
```
.
├── docker-compose.yml
├── logs/
│   ├── access.log
│   └── error.log
├── nginx-config/
│   └── default.conf
```

## Configuration Notes
- The `proxy_pass` directive in `nginx-config/default.conf` forwards requests to the backend application. Update `http://host.docker.internal:3000` as needed.
- Ensure that your backend application is accessible on the specified address and port.

## Restart Policy
The container is set to restart automatically using:
```yaml
restart: always
```
This ensures that the container restarts in case of crashes or reboots.

## Stopping the Setup
To stop the Nginx container, run:
```bash
docker-compose down
```

## Troubleshooting
- **Problem**: Cannot access `http://localhost`
  - Ensure the Nginx container is running: `docker ps`
  - Check the logs for errors: `tail -f logs/error.log`

- **Problem**: Backend is not reachable
  - Verify that the backend application is running on `http://host.docker.internal:3000`.
  - Update the `proxy_pass` directive in `nginx-config/default.conf` if needed.

## License
This project is licensed under the MIT License. See the LICENSE file for details.

