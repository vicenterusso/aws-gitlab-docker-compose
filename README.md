# AWS GitLab Docker Compose Setup

Provides an overview and instructions for deploying GitLab and GitLab Runner using Docker Compose. This setup includes GitLab for hosting your repositories and GitLab Runner for running your CI/CD pipelines.

## Overview

The Docker Compose file contains two services:

1. **`web`**: This service runs GitLab Community Edition (CE) and is configured to use an external URL with SSL termination handled externally (e.g., by a load balancer).
2. **`gitlab-runner`**: This service runs GitLab Runner, which is used to execute your CI/CD jobs.

This setup is suitable for small to medium-sized teams that operates Gitlab behind an ELB load balancer.

## Prerequisites

- Docker and Docker Compose installed on your host machine.
- AWS Application Load Balancer configured to handle SSL termination.
- AWS Network Load Balancer configured to handle SSH traffic.
- Domain name (`gitlab.mydomain.com` for GitLab and `git.mydomain.com` for SSH access).

## Configuration

Before starting, ensure the following configurations are adjusted to fit your environment:

- **External URL**: Set to your GitLab instance's domain (`https://gitlab.mydomain.com`).
- **GitLab SSH Host**: Configured for SSH access (`git.mydomain.com`), ensure DNS is pointing to the same host.
- **Ports**: `8088` is mapped to GitLab's internal port `80`, and `2222` for GitLab SSH (make sure your external SSH setup points here if using a custom port).

## Deployment

To deploy GitLab and GitLab Runner:

1. **Clone the Repository** (if provided) or create a `docker-compose.yml` file with the content provided above.

2. **Start Services**:

   Run the following command in the directory where your `docker-compose.yml` is located:

   ```bash
   docker-compose up -d
   ```

3. **Verify Deployment**:

   After a few minutes, access GitLab by navigating to `https://gitlab.mydomain.com`. The first visit will prompt you to set up an admin password.

## Registering GitLab Runner

To register the GitLab Runner with your GitLab instance:

1. Obtain a **registration token** from GitLab:
   - Navigate to **Admin Area > Overview > Runners** in your GitLab instance.

2. Register the runner:

Execute the following command, replacing `YOUR_TOKEN_HERE` with your registration token:

```bash
docker exec -it gitlab-runner gitlab-runner register \
   --url "http://web" \
   --token "glrt-qn8zVF-q_eg51xnY8PxC" \
   --executor "docker" \
   --docker-image "docker:20.10.7"
```

Notie that the `--url` parameter is set to `http://web`, which is the name of the GitLab service in the Docker Compose file.

## Backup and Restore

Ensure regular backups, especially for production environments. Refer to GitLab's documentation for backup and restore procedures.

## Maintenance

Regularly update your Docker images to the latest versions to receive security updates and new features:

```bash
docker-compose pull
docker-compose up -d
```

## Troubleshooting

- For issues related to GitLab, check the logs using `docker logs gitlab-ce`.
- For runner-specific issues, use `docker logs gitlab-runner`.

## Support

For additional help, refer to the [GitLab official documentation](https://docs.gitlab.com/ee/) or post questions on [GitLab Forum](https://forum.gitlab.com/).

---

This README provides a basic starting point. Adjust the content to match your specific setup, security practices, and operational procedures.
