# devops-engineering-ci-public-platform-build-push-action

GitHub Action used to build Platform Specialised Images for a specific architecture (arm64 or amd64).

## Description

This action builds and pushes IQGeo platform specialized images (base, build, appserver, tools, devenv) for Platform 7.x. It handles:
- Checking out the utils-docker-platform repository
- Setting up Docker Buildx
- Authenticating with Azure Container Registry
- Building the image with appropriate build arguments per image type
- Pushing the built image to ACR

## Supported Image Types

- **base**: Platform base image with core IQGeo files
- **build**: Build image with Node.js and Python build tools
- **appserver**: Application server image with Apache and mod_wsgi
- **tools**: Tools image with JDK
- **devenv**: Development environment image with debugging tools

## Usage

```yaml
- name: Build and push platform image
  uses: IQGeo/devops-engineering-ci-public-platform-build-push-action@main
  with:
    version: '7.4.0'
    build_id: 'unique-build-id-123'
    platform: 'arm64'  # or 'amd64'
    image_type: 'base'  # or 'build', 'appserver', 'tools', 'devenv'
    dockerfile: 'dockerfile.base'
    acr: 'iqgeoproddev.azurecr.io'
    registry_username: ${{ secrets.REGISTRY_USERNAME }}
    registry_password: ${{ secrets.REGISTRY_PASSWORD }}
    engineering_prefix: 'devops_sandbox_engineering'
    gh_token: ${{ secrets.GH_TOKEN }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | Platform version to build (e.g., 7.4.0) | Yes | - |
| `build_id` | Unique build ID for this workflow run | Yes | - |
| `platform` | Architecture to build for (amd64, arm64) | Yes | - |
| `image_type` | Type of platform image (base, build, appserver, tools, devenv) | Yes | - |
| `dockerfile` | Name of the dockerfile (dockerfile.base, dockerfile.build, etc.) | Yes | - |
| `acr` | Azure container registry server name | Yes | - |
| `registry_username` | Azure container registry username | Yes | - |
| `registry_password` | Azure container registry password | Yes | - |
| `engineering_prefix` | Engineering prefix for ACR image path | No | `devops_sandbox_engineering` |
| `gh_token` | GitHub token to clone utils-docker-platform repo | Yes | - |
| `dev_tools_version` | Dev tools version (only used for devenv image) | No | - |

## Build Arguments by Image Type

### base
- `PLATFORM_VERSION`
- `CONTAINER_REGISTRY` (engineering/)

### build
- `PLATFORM_VERSION`
- `CONTAINER_REGISTRY` (with engineering_prefix)
- `PLATFORM_BASE`

### appserver
- `PLATFORM_VERSION`
- `CONTAINER_REGISTRY` (with engineering_prefix)

### tools
- `PLATFORM_VERSION`
- `CONTAINER_REGISTRY` (with engineering_prefix)

### devenv
- `PLATFORM_VERSION`
- `DEV_TOOLS_VERSION`
- `CONTAINER_REGISTRY` (with engineering_prefix)
- `PLATFORM_BASE`

## Image Naming

Images are tagged as:
```
{acr}/{engineering_prefix}/platform/platform-{image_type}:{build_id}_{arch_suffix}
```

Where `arch_suffix` is:
- `arm-64` for arm64 builds
- `amd-64` for amd64 builds

## Example Workflow

See [devops-engineering-ci-public-build-platform-specialised-image-workflow](https://github.com/IQGeo/devops-engineering-ci-public-build-platform-specialised-image-workflow) for a complete example of using this action to build all platform images with proper dependency chains.
