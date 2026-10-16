# Shell Scripting Projects

A collection of practical Bash scripts for learning automation in cloud provisioning and application deployment. The repository currently includes examples for creating an AWS EC2 instance and deploying a containerized Django application.

## Projects

| File | Purpose | Main technologies |
| --- | --- | --- |
| [`project1.sh`](project1.sh) | Creates an EC2 instance, tags it, and waits until it reaches the `running` state. | Bash, AWS CLI, Amazon EC2 |
| [`project2`](project2) | Clones a Django notes application, installs Docker/Nginx tooling, and starts the application with Docker Compose. | Bash, Git, Docker, Docker Compose, Nginx |

## Requirements

### General

- Linux environment with Bash
- `sudo` access for package installation and system configuration
- Internet connectivity
- Git, where required by `project2`

### For `project1.sh`

- AWS CLI v2
- An AWS account with permission to call `ec2:RunInstances` and `ec2:DescribeInstances`
- An AWS region configured for the AWS CLI, for example:

  ```bash
  aws configure
  ```

- A valid AMI ID, EC2 instance type, key pair name, subnet ID, and security group ID for the selected region

### For `project2`

- A Debian/Ubuntu-style system with `apt-get`
- Docker
- Docker Compose
- Permission to use Docker

## Usage

Clone the repository and make the scripts executable:

```bash
git clone https://github.com/PrachiSukreSS/Shell_Scripting.git
cd Shell_Scripting
chmod +x project1.sh project2
```

### Create an EC2 instance

Before running the script, edit the configuration values in `project1.sh`:

```bash
AMI_ID="ami-xxxxxxxxxxxxxxxxx"
INSTANCE_TYPE="t2.micro"
KEY_NAME="your-key-pair"
SUBNET_ID="subnet-xxxxxxxxxxxxxxxxx"
SECURITY_GROUP_IDS="sg-xxxxxxxxxxxxxxxxx"
INSTANCE_NAME="Shell-Script-EC2-Demo"
```

Then run:

```bash
./project1.sh
```

The script calls `aws ec2 run-instances`, extracts the new instance ID, and polls EC2 every 10 seconds until the instance state is `running`.

> **Cost warning:** Running EC2 resources can incur AWS charges. Stop or terminate resources when they are no longer needed.

### Deploy the Django application

Run the deployment script from a suitable working directory:

```bash
./project2
```

The script is designed to:

1. Clone `django-notes-app` if the directory does not already exist.
2. Install Docker, Nginx, and Docker Compose using `apt-get`.
3. Adjust ownership of `/var/run/docker.sock`.
4. Build a Docker image named `notes-app`.
5. Start the application in detached mode with Docker Compose.

The deployment script assumes that the cloned application contains a working `Dockerfile` and Docker Compose configuration.

## Script structure

Both scripts demonstrate reusable Bash functions and explicit error handling:

- `project1.sh`
  - `check_awscli` verifies whether AWS CLI is available.
  - `install_awscli` downloads and installs AWS CLI v2.
  - `wait_for_instance` polls the EC2 instance state.
  - `create_ec2_instance` provisions and tags the instance.
  - `main` supplies the configuration and starts the workflow.

- `project2`
  - `code_clone` retrieves the Django application source.
  - `install_requirements` installs deployment dependencies.
  - `required_restarts` performs Docker socket configuration.
  - `deploy` builds and launches the application.

## Important considerations

These are educational scripts and should be reviewed before use in a production environment:

- `project1.sh` currently contains placeholder values for AWS configuration. Replace them before execution.
- AWS CLI installation is hard-coded for Linux x86_64 and requires `curl`, `unzip`, `sudo`, and `apt-get`.
- The AWS workflow does not set a timeout for instance startup and does not clean up the instance if a later step fails.
- `project2` installs packages and changes Docker socket ownership with elevated privileges. Review these commands carefully before running them.
- Adding a user to the Docker socket or changing its ownership can grant root-equivalent access to local users.
- The deployment workflow downloads source code from an external repository. Review the source, Dockerfile, and Compose configuration before building images.
- The scripts do not currently include automated tests, linting, secret management, or rollback handling.

## Troubleshooting

### AWS CLI or credentials errors

Verify installation, credentials, and the active region:

```bash
aws --version
aws sts get-caller-identity
aws configure get region
```

Ensure that the AMI, subnet, key pair, and security group all belong to the same AWS region.

### Docker permission errors

Confirm that Docker is installed and running:

```bash
docker --version
sudo systemctl status docker
```

Log out and back in if your user was added to the Docker group, or use the system's documented Docker permission procedure.

### Application deployment errors

Run the Docker build and Compose commands manually from the application directory to inspect their output:

```bash
docker build -t notes-app .
docker compose up -d
```

Depending on the installed Docker version, the command may be `docker-compose up -d` instead of `docker compose up -d`.

## Learning objectives

This repository provides practice with:

- Bash functions and script entry points
- Strict-mode scripting with `set -euo pipefail`
- Command checks and exit-status handling
- AWS CLI automation
- Polling asynchronous cloud resource state
- Linux package installation
- Docker-based application deployment
- Basic deployment failure handling

## License

No license file is currently included. Add a license if you intend to share or reuse this project under specific terms.
