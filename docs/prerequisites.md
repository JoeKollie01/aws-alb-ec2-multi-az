prerequistes
============
# Prerequisites

## AWS requirements
- An AWS account
- Permissions to create:
  - VPC resources (or permission to use the default VPC)
  - EC2 instances
  - Security groups
  - Application Load Balancer + Target Group

## Region and AZs
Pick one AWS region (example: us-east-1).
You will use **two Availability Zones** in that region.

## Networking approach
Use the **default VPC** to keep things simple:
- Two public subnets in different AZs (default VPC usually already has this)

## Ports used
- HTTP: 80
- SSH: 22 (optional, only if you want to log in)

## Local tools (optional)
- SSH client (if you plan to SSH into instances)
