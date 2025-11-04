# AWS ECS Deploy GitHub Action

This repository contains a GitHub Action for deploying Docker containers to Amazon ECS (Elastic Container Service).

## Features

- Deploys Docker images to ECS services
- Supports updating task definitions
- Integrates with GitHub Actions workflows

## Usage

Add the following step to your GitHub Actions workflow:

```yaml
- name: Deploy to ECS
    uses: brandlive/events/action-aws-ecs-deploy@v1
    with:
        task-definition: .aws/my-task-def.json
        container-name: my-container-name
        region: us-west-2
        cluster: my-ecs-cluster
        service: my-ecs-service
        image: my-docker-image:latest
        role: IAM deployment role
```

## Inputs

| Name                   | Description                        | Required |
|------------------------|------------------------------------|----------|
| `aws-access-key-id`    | AWS access key ID                  | Yes      |
| `aws-secret-access-key`| AWS secret access key              | Yes      |
| `cluster`              | ECS cluster name                   | Yes      |
| `service`              | ECS service name                   | Yes      |
| `image`                | Docker image to deploy             | Yes      |

## License

MIT