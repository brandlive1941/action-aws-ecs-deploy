# action-aws-ecs-deploy

Configures AWS, renders Task Definition, and deploys to ECS for a single service/region combination. This action supports both updating existing services and creating new ones with automatic configuration retrieval from AWS Systems Manager (SSM) Parameter Store.

## Usage

### Simple Deployment (Targeting Existing Service)

```yaml
- name: Deploy to ECS
  uses: brandlive1941/action-aws-ecs-deploy@v1
  with:
    task-definition: .aws/sandbox-us-west-2-translation-service.json
    container-name: sandbox-translation-service-container
    region: us-west-2
    cluster: platform-sandbox-us-west-2
    service: translation-service
    image: 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-image:latest
    role: arn:aws:iam::123456789012:role/github-ecs-deploy-role
```

### Full Deployment (Create Service if missing)

When `create_service` is set to `true`, the action will attempt to fetch subnets, security groups, and target group ARNs from SSM Parameter Store using the provided `environment` and `container-name`.

```yaml
- name: Deploy to ECS
  uses: brandlive1941/action-aws-ecs-deploy@v1
  with:
    task-definition: .aws/sandbox-us-west-2-translation-service.json
    container-name: sandbox-translation-service-container
    environment: sandbox
    region: us-west-2
    cluster: platform-sandbox-us-west-2
    service: translation-service
    image: 123456789012.dkr.ecr.us-west-2.amazonaws.com/my-image:latest
    role: arn:aws:iam::123456789012:role/github-ecs-deploy-role
    create_service: true
    desired_count: 2
```

## Inputs

| Name | Description | Required | Default |
| :--- | :--- | :--- | :--- |
| `task-definition` | The task definition file path | **Yes** | - |
| `container-name` | The name of the container in the task definition to update | **Yes** | - |
| `image` | The ECR image URL | **Yes** | - |
| `region` | The AWS region to deploy to | **Yes** | - |
| `cluster` | The name of the ECS cluster to deploy to | **Yes** | - |
| `service` | The name of the service to deploy | **Yes** | - |
| `role` | The IAM Role ARN to assume for deployment | **Yes** | - |
| `environment` | The environment name (e.g., sandbox, dev, qa). Required for fetching configuration from SSM. | No | - |
| `container_port` | The port on the container to associate with the load balancer | No | `80` |
| `create_service` | Set to `true` to create the service if it does not exist | No | `false` |
| `launch_type` | The launch type to use for the service (used only when creating) | No | `FARGATE` |
| `desired_count` | The desired count for the service (used only when creating) | No | `0` |
| `subnets` | Fallback subnets to use for the service (if SSM fetch fails) | No | `[]` |
| `security_groups` | Fallback security groups to use for the service (if SSM fetch fails) | No | `[]` |
| `target_group` | Fallback target group ARN to use for the service (if SSM fetch fails) | No | - |

## Automatic Configuration Retrieval

When `create_service: true` and `environment` is provided, the action fetches the following SSM parameters:

- Subnets: `/${region}/SUBNET_A`, `/${region}/SUBNET_B`, `/${region}/SUBNET_C`
- Security Group: `/${environment}/${container-name}/SECURITY_GROUP`
- Target Group ARN: `/${environment}/${container-name}/TG_ARN`

If these parameters do not exist, it will fall back to the provided `subnets`, `security_groups`, and `target_group` inputs.

## License

MIT