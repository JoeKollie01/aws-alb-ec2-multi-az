# AWS ALB -> EC2 (Multi-AZ) Web Architecture (Console Setup)

This repo documents how to build a simple, highly available web setup on AWS using the AWS Console:

- Internet-facing **Application Load Balancer (ALB)**
- **Target Group** forwarding traffic on **port 80**
- Two **EC2 instances** running **Apache**, placed in **different Availability Zones**

## Architecture

![Architecture Diagram](diagrams/architecture.png)

## What this provides

- **High availability**: instances in multiple AZs
- **Load balancing**: traffic is distributed across healthy instances
- **Health checks**: ALB stops routing to unhealthy instances

## Quick start

1. Read prerequisites: `docs/prerequisites.md`
2. Launch EC2 instances + install Apache: `docs/setup-ec2.md`
3. Create target group + ALB: `docs/setup-alb.md`
4. Test in browser: `docs/testing.md`
5. Tear down resources when done: `docs/teardown.md`

## Expected outcome

You will open the ALB DNS name in your browser and see a page served by Apache.
Refreshing the page should alternate between instances (if each serves a slightly different page).
