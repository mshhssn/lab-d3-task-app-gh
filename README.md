cat > README.md <<'EOF'
# lab-d3-task-app-gh

Small browser-based task app served by Nginx in Docker.

## Purpose

This repository documents the learning and implementation state of the Docker labs up to:

- containerizing a small static web app
- using GitHub as the source of truth
- using Docker Hub as the image registry
- validating images in CI before pushing
- publishing versioned releases with Git tags

## Contents

- static frontend app
- Dockerfile
- GitHub Actions workflow for build, test, and publish
- Docker Hub publishing setup

## Repositories

- GitHub repository: `lab-d3-task-app-gh`
- Docker Hub repository: `mshhssn/lab-d3-task-app-dh`

## Project Structure

```text
.
├── app/
│   ├── index.html
│   ├── style.css
│   └── app.js
├── .github/
│   └── workflows/
│       └── docker-publish.yml
├── Dockerfile
└── README.md
```

## Local Build and Run
To build and run the Docker image locally:

```bash
# Build the Docker image
docker build -t lab-d3-task-app:local .

# Run the Docker container
docker run -d --name lab-d3-task-app-local -p 8080:80 lab-d3-task-app:local

# Access the app in the browser
curl -I http://localhost:8080

# Stop and remove the container when done
docker stop lab-d3-task-app-local
docker rm lab-d3-task-app-local
```

## CI/CD Workflow
The GitHub Actions workflow defined in `.github/workflows/docker-publish.yml` automates the build, test, and publish process for the Docker image. It triggers on pushes to the `main` branch and on new tags, ensuring that only validated images are published to Docker Hub.

Current pipeline behavior:
- verfies required files exist
- builds the test Docker image
- starts a container from the test image
- checks if the container is running
- perform an HTTP check to ensure the app is responding
- if all checks pass, builds and pushes the image to Docker Hub

For Git tags liek `v1.0.0`, the workflow will also create a versioned release on GitHub and push the corresponding image tag to Docker Hub.
- builds the Docker image with the version tag
- pushes the image to Docker Hub with the version tag
- creates a GitHub release with the version tag

## Docker Hub Tags
Typically, the Docker Hub repository will have tags like:
- `latest` (automatically updated with the latest successful build from `main`)
- `v1.0.0`, `v1.1.0`, etc. (versioned tags corresponding to GitHub releases)
- sha256 digest tags for specific image versions

## Release Tagging
To create a new release, you can use Git tags. For example:
```bash
git checkout main
git pull origin main
git tag v1.0.0
git push origin v1.0.0
```
This will trigger the GitHub Actions workflow to build and publish the corresponding Docker image and create a GitHub release.

## Notes
- GitHub stores how the image is built and validated.
- Docker Hub stores the built image artifacts.
- The local image name can differ from the Docker Hub image name.
- This repository represents the project state up to the versioned image tagging stage.

```bash
# Example of building and tagging a local image
docker build -t lab-d3-task-app:local .
docker tag lab-d3-task-app:local mshhssn/lab-d3-task-app:latest
docker tag lab-d3-task-app:local mshhssn/lab-d3-task-app:v1.0.0
```