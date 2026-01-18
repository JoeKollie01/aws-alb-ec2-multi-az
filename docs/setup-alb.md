# Setup EC2 Instances (Apache)

You will create 2 EC2 instances in 2 different AZs and install Apache.

## Step 1: Launch Instance #1
1. Go to **EC2 > Instances > Launch instances**
2. Name: `web-1`
3. AMI: **Amazon Linux 2023** (or Amazon Linux 2)
4. Instance type: `t2.micro` (free tier eligible if applicable)
5. Key pair: choose one (optional if you won't SSH) - default value "Proceed without a key pair
6. Network settings:
   - VPC: default VPC
   - Subnet: pick **AZ A** (example: us-east-1a)
   - Auto-assign public IP: **Enabled**
7. Security group (create new):
   - Name: `web-sg`
   - Inbound rules:
     - HTTP (80) from **0.0.0.0/0** (temporary, for easy testing)
     - SSH (22) from **your IP only** (optional)
8. Launch instance

## Step 2: Launch Instance #2
Repeat the same steps:
- Name: `web-2`
- Subnet: pick **AZ B** (example: us-east-1b)
- Use the same security group `web-sg`

## Step 3: Install Apache on both instances
### Option A (recommended): Use User Data during launch
If you didn’t add it earlier, you can still do it by creating new instances with user data.

User data script (Amazon Linux):
```bash
##!/bin/bash
set -euxo pipefail

yum update -y
yum install -y httpd

systemctl enable httpd
systemctl start httpd

INSTANCE_ID="$(curl -s http://169.254.169.254/latest/meta-data/instance-id || true)"
AZ="$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone || true)"

cat > /var/www/html/index.html <<'HTML'
<!doctype html>
<html>
  <head><title>It works</title></head>
  <body>
    <h1>Apache is running</h1>
  </body>
</html>
HTML

if [ -n "${INSTANCE_ID}" ] || [ -n "${AZ}" ]; then
  echo "<p>Instance: ${INSTANCE_ID}</p><p>AZ: ${AZ}</p>" >> /var/www/html/index.html
fi

## Step 4: Create the Target Group (HTTP : 80)

1. Go to **EC2 → Target Groups**
2. Click **Create target group**
3. Set **Target type** to **Instances**
4. Enter **Target group name**: `web-tg`
5. Set **Protocol** to **HTTP** and **Port** to **80**
6. Select the **VPC** where your EC2 instances are running
7. Under **Health checks**:
   - **Protocol**: HTTP
   - **Path**: `/`
8. Click **Next**
9. Under **Register targets**:
   - Select both EC2 instances
   - Confirm **Port** is `80`
   - Click **Include as pending below**
10. Click **Create target group**
11. Open the target group → **Targets** tab → confirm both instances show **Healthy**

---

## Step 5: Create the Application Load Balancer (ALB)

1. Go to **EC2 → Load Balancers**
2. Click **Create load balancer**
3. Choose **Application Load Balancer**
4. Enter **Load balancer name**: `web-alb`
5. Set:
   - **Scheme**: Internet-facing
   - **IP address type**: IPv4
6. Under **Network mapping**:
   - Select the same **VPC** as your EC2 instances
   - Select **two subnets in two different Availability Zones**
7. Under **Security groups**:
   - Create/select `alb-sg`
   - Ensure inbound rule exists: **HTTP (80)** from `0.0.0.0/0`
8. Under **Listeners and routing**:
   - Listener: **HTTP : 80**
   - Default action: **Forward to** target group `web-tg`
9. Click **Create load balancer**
10. Test:
   - Open the ALB and copy its **DNS name**
   - Visit: `http://<alb-dns-name>`
   - Refresh a few times to see traffic hit different instances (if your page shows instance/AZ)
