# Week 8 Comparison

## VM Deployment vs Cloud Run

In previous weeks, the Flask application was deployed manually to virtual machines using Docker and SSH. This required connecting to servers, building containers manually, and restarting services.

With Cloud Run, deployment is easier because Google automatically manages the infrastructure. The application scales automatically and HTTPS is already configured.

## CI/CD Improvements

The CI/CD pipeline makes deployments faster and more secure. GitHub Actions can automatically build and deploy containers without manual SSH access.

## Security Benefits

Cloud Run reduces server management and limits direct access to infrastructure. Artifact Registry securely stores container images and Terraform keeps infrastructure configuration consistent.

## Reflection

Using Terraform and Cloud Run simplified deployment compared to managing virtual machines manually. The process was faster, easier to scale, and more automated.
