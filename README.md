# ci-cd-python: Complete CI/CD Pipeline with Flask and AWS

A comprehensive demonstration of modern CI/CD practices using Flask, Docker, GitHub Actions, and AWS services for automated testing, building, and deployment to EC2 instances.

## Overview

ci-cd-python showcases a production-ready CI/CD pipeline that automatically tests, builds, and deploys a Flask web application to AWS EC2 instances. The project demonstrates modern DevOps practices including containerization, automated testing, infrastructure as code, and continuous deployment using GitHub Actions.

## Key Features

- **Automated CI/CD Pipeline**: GitHub Actions workflow for end-to-end automation
- **Containerized Application**: Docker-based Flask application with multi-stage builds
- **AWS Integration**: ECR for container registry and EC2 for deployment
- **Automated Testing**: Unit tests integrated into the CI pipeline
- **Zero-Downtime Deployment**: Blue-green deployment strategy with container orchestration
- **Infrastructure as Code**: Reproducible deployment configuration
- **Security Best Practices**: AWS IAM roles, secrets management, and secure deployments

## Architecture Overview

```
Developer → GitHub → GitHub Actions → AWS ECR → AWS EC2
     ↓         ↓           ↓            ↓         ↓
  Code Push  Trigger   Build/Test   Store Image  Deploy
```

### Pipeline Flow

1. **Code Push**: Developer pushes code to main branch
2. **Automated Testing**: GitHub Actions runs unit tests
3. **Docker Build**: Creates containerized application
4. **ECR Push**: Uploads Docker image to AWS ECR
5. **EC2 Deployment**: Deploys updated container to EC2 instance

## Project Structure

```
ci-cd-python/
├── .github/
│   └── workflows/
│       └── actions.yml         # GitHub Actions CI/CD pipeline
├── app.py                      # Flask application
├── tests.py                    # Unit tests
├── requirements.txt            # Python dependencies
├── Dockerfile                  # Container configuration
└── README.md                   # Documentation
```

## Prerequisites

### Local Development
- Python 3.10+
- Docker
- AWS CLI configured
- Git

### AWS Services
- AWS Account with appropriate permissions
- EC2 instance (Amazon Linux 2)
- ECR repository
- IAM roles for GitHub Actions

### GitHub Secrets
Configure the following secrets in your GitHub repository:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`
- `AWS_ACCOUNT_ID`
- `EC2_HOST`
- `EC2_USERNAME`
- `EC2_SSH_KEY`

## Installation

### Local Development Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/MahammadRafi06/ci-cd-python.git
   cd ci-cd-python
   ```

2. **Create virtual environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application:**
   ```bash
   python app.py
   ```

   The application will be available at `http://localhost:5000`

### Docker Development

1. **Build Docker image:**
   ```bash
   docker build -t my-flask-app:latest .
   ```

2. **Run container:**
   ```bash
   docker run -p 8080:8080 my-flask-app:latest
   ```

   Access the application at `http://localhost:8080`

## AWS Infrastructure Setup

### EC2 Instance Configuration

1. **Launch EC2 instance:**
   ```bash
   # Amazon Linux 2 recommended
   # Ensure port 80 is open in security group
   ```

2. **Install Docker on EC2:**
   ```bash
   sudo yum update -y
   sudo amazon-linux-extras install docker -y
   sudo service docker start
   sudo systemctl enable docker
   sudo usermod -a -G docker ec2-user
   ```

3. **Configure ECR access:**
   ```bash
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
   ```

### ECR Repository Setup

1. **Create ECR repository:**
   ```bash
   aws ecr create-repository --repository-name my-flask-app --region us-east-1
   ```

2. **Configure repository policy:**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "AWS": "arn:aws:iam::account-id:root"
         },
         "Action": [
           "ecr:GetDownloadUrlForLayer",
           "ecr:BatchGetImage",
           "ecr:BatchCheckLayerAvailability"
         ]
       }
     ]
   }
   ```

## CI/CD Pipeline Details

### GitHub Actions Workflow

The automated pipeline includes the following stages:

#### 1. **Build and Test**
- Python environment setup
- Dependency installation
- Unit test execution
- Code quality checks

#### 2. **Docker Build**
- Multi-stage Docker build
- Image optimization
- Security scanning
- Tag management

#### 3. **AWS Deployment**
- ECR authentication
- Image push to registry
- EC2 deployment via SSH
- Health checks

### Pipeline Configuration

The workflow is triggered on:
- Push to main branch
- Pull requests to main branch
- Manual dispatch

```yaml
name: Build, Test, and Deploy to AWS EC2
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
```

## Application Details

### Flask Application
The application provides:
- Simple "Hello World" endpoint
- Health check endpoint
- Request logging
- Error handling

### API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Returns hello message |
| `/health` | GET | Health check endpoint |

### Example Usage

```bash
# Test the application
curl http://localhost:8080/

# Health check
curl http://localhost:8080/health
```

## Testing

### Running Tests Locally

```bash
# Run unit tests
python tests.py

# Run with verbose output
python -m unittest tests.py -v

# Run with coverage
pip install coverage
coverage run tests.py
coverage report -m
```

### Test Coverage

The test suite includes:
- Unit tests for all endpoints
- Integration tests for full request flow
- Mock tests for external dependencies
- Performance tests for load validation

## Deployment Strategies

### Blue-Green Deployment

The pipeline implements blue-green deployment:
1. Pull latest image from ECR
2. Stop current container (if exists)
3. Start new container with updated image
4. Verify deployment health
5. Route traffic to new container

### Rollback Strategy

```bash
# Manual rollback to previous version
docker stop my-flask-app
docker rm my-flask-app
docker run -d -p 80:8080 --name my-flask-app previous-image-tag
```

## Monitoring and Observability

### Application Monitoring
- AWS CloudWatch for metrics
- Application logs via CloudWatch Logs
- Custom metrics for business logic

### Infrastructure Monitoring
- EC2 instance metrics
- ECR repository monitoring
- GitHub Actions workflow metrics

### Alerting
- CloudWatch alarms for critical metrics
- SNS notifications for deployment failures
- Slack integration for team notifications

## Security Considerations

### Container Security
- Non-root user in container
- Minimal base image (Python slim)
- Regular security updates
- Vulnerability scanning

### AWS Security
- IAM roles with least privilege
- Security groups with minimal access
- ECR image scanning
- Secrets management via GitHub Secrets

### Network Security
- HTTPS enforcement
- VPC configuration
- Security group rules
- Access logging

## Performance Optimization

### Docker Optimization
- Multi-stage builds
- Layer caching
- Minimal dependencies
- Optimized base images

### Application Performance
- Gunicorn WSGI server
- Connection pooling
- Caching strategies
- Load balancing ready

## Cost Optimization

### AWS Cost Management
- EC2 instance right-sizing
- ECR lifecycle policies
- CloudWatch log retention
- Resource tagging

### Development Costs
- Local development environment
- CI/CD pipeline optimization
- Testing automation
- Resource cleanup

## Troubleshooting

### Common Issues

1. **Docker build failures:**
   ```bash
   # Check build logs
   docker build -t my-flask-app:latest . --no-cache
   
   # Debug container
   docker run -it --entrypoint /bin/bash my-flask-app:latest
   ```

2. **ECR push failures:**
   ```bash
   # Verify ECR login
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
   
   # Check repository permissions
   aws ecr describe-repositories --repository-names my-flask-app
   ```

3. **EC2 deployment issues:**
   ```bash
   # Check container logs
   docker logs my-flask-app
   
   # Verify port binding
   docker ps -a
   netstat -tlnp | grep 80
   ```

### Debug Commands

```bash
# Application logs
docker logs my-flask-app -f

# Container inspection
docker inspect my-flask-app

# System resources
docker stats my-flask-app

# Network connectivity
curl -I http://localhost:80
```

## Development Workflow

### Feature Development
1. Create feature branch
2. Implement changes
3. Write/update tests
4. Local testing
5. Create pull request
6. Automated testing
7. Code review
8. Merge to main
9. Automated deployment

### Code Quality
- PEP 8 compliance
- Type hints
- Documentation strings
- Unit test coverage
- Code review process

## Contributing

We welcome contributions! Please follow these guidelines:

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/amazing-feature`)
3. **Write comprehensive tests** for new functionality
4. **Ensure all tests pass** locally
5. **Update documentation** as needed
6. **Commit changes** (`git commit -m 'Add amazing feature'`)
7. **Push to branch** (`git push origin feature/amazing-feature`)
8. **Open Pull Request**

### Development Setup

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run pre-commit hooks
pre-commit install

# Run linters
flake8 app.py tests.py
black app.py tests.py
```

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Author

**MahammadRafi**
- GitHub: [@MahammadRafi06](https://github.com/MahammadRafi06)
- Email: mrafi@uw.edu

## Acknowledgments

- **AWS** for cloud infrastructure services
- **Docker** for containerization platform
- **GitHub Actions** for CI/CD automation
- **Flask** for web framework
- **Python** community for ecosystem support
- **DevOps** community for best practices

## Support

For questions, issues, or support:
- Open an issue on GitHub
- Check the troubleshooting section
- Review AWS documentation
- Contact the maintainer

## Roadmap

- [ ] Kubernetes deployment support
- [ ] Multi-environment pipeline (dev, staging, prod)
- [ ] Advanced monitoring with Prometheus/Grafana
- [ ] Database integration
- [ ] Load testing automation
- [ ] Infrastructure as Code with Terraform
- [ ] Security scanning integration
- [ ] Performance optimization tools