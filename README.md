# GHG App Deployment

A Docker-based deployment configuration for the Greenhouse Gas (GHG) Application, featuring automated container updates with Watchtower.

## Overview

This repository contains the production deployment configuration for the GHG Application (ghgapp), a Nuxt.js-based web application for greenhouse gas tracking and analysis. The deployment uses Docker Compose to orchestrate the application container along with Watchtower for automatic updates.

## Architecture

- **ghgapp**: The main Nuxt.js application container running on port 3000
- **watchtower**: Automated container update service that monitors and updates the ghgapp container

## Prerequisites

Before deploying, ensure you have the following installed:

- Docker (version 20.10 or higher)
- Docker Compose (version 2.0 or higher)
- Access credentials to Azure Container Registry (ihadlab-f2bnbba0fgceaff9.azurecr.io)

## Setup

### 1. Docker Login

First, authenticate with the Azure Container Registry:

```bash
docker login ihadlab-f2bnbba0fgceaff9.azurecr.io
```

You will be prompted for your username and password. Contact your system administrator if you don't have these credentials.

### 2. Environment Configuration

Update the `prod.env` file with your database credentials:

```bash
NUXT_DATABASE_PASSWORD=your_secure_password
```

**Important**: Never commit sensitive credentials to version control. Consider using environment-specific files or secrets management tools for production deployments.

## Deployment

### Starting the Application

To start all services:

```bash
docker-compose up -d
```

This command will:
1. Pull the latest ghgapp image from Azure Container Registry
2. Start the ghgapp container on port 3000
3. Start the Watchtower service for automatic updates

### Stopping the Application

To stop all services:

```bash
docker-compose down
```

### Viewing Logs

To view application logs:

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f ghgapp
docker-compose logs -f watchtower
```

## Configuration

### Application Settings

The ghgapp service is configured with the following settings:

- **Port**: 3000 (mapped to host port 3000)
- **Workers**: 8 Nitro cluster workers for handling concurrent requests
- **Restart Policy**: `unless-stopped` - automatically restarts on failure
- **Environment**: Configured via `prod.env` file

### Watchtower Settings

Watchtower automatically monitors and updates containers with these settings:

- **Poll Interval**: 300 seconds (5 minutes)
- **Cleanup**: Automatically removes old images after updates
- **Monitored Containers**: ghgapp and watchtower itself
- **Restart Policy**: `unless-stopped`

## Accessing the Application

Once deployed, the application will be available at:

```
http://localhost:3000
```

Or replace `localhost` with your server's IP address or domain name.

## Maintenance

### Manual Updates

While Watchtower handles automatic updates, you can manually update the containers:

```bash
docker-compose pull
docker-compose up -d
```

### Checking Container Status

```bash
docker-compose ps
```

### Restarting Services

```bash
# Restart all services
docker-compose restart

# Restart specific service
docker-compose restart ghgapp
```

## Troubleshooting

### Container Won't Start

1. Check logs: `docker-compose logs ghgapp`
2. Verify Docker login: `docker login ihadlab-f2bnbba0fgceaff9.azurecr.io`
3. Ensure `prod.env` file exists and contains valid configuration

### Port Already in Use

If port 3000 is already in use, modify the `docker-compose.yaml` file to use a different port:

```yaml
ports:
  - "8080:3000"  # Change 8080 to your preferred port
```

### Database Connection Issues

1. Verify the `NUXT_DATABASE_PASSWORD` in `prod.env` is correct
2. Ensure the database server is accessible from the container
3. Check application logs for specific error messages

### Watchtower Not Updating

1. Check Watchtower logs: `docker-compose logs watchtower`
2. Verify Docker registry credentials are still valid
3. Ensure the `~/.docker/config.json` file is present and accessible

## Security Considerations

- Keep your Azure Container Registry credentials secure
- Regularly update the `NUXT_DATABASE_PASSWORD`
- Monitor Watchtower logs for unauthorized update attempts
- Use HTTPS in production with a reverse proxy (nginx, Traefik, etc.)
- Consider implementing rate limiting and firewall rules

## Contributing

This is a deployment configuration repository. For application-level changes, please refer to the main ghgapp repository.

## Support

For issues or questions:
1. Check the logs first using `docker-compose logs`
2. Review this documentation
3. Contact the development team or system administrator

## License

Please refer to the main application repository for license information.
