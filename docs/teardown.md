# Teardown (Avoid Charges)

Delete resources in this order:

1. **Load Balancer**
   - EC2 > Load Balancers > select `web-alb` > Delete

2. **Target Group**
   - EC2 > Target Groups > select `web-tg` > Delete

3. **EC2 Instances**
   - EC2 > Instances > select `web-1` and `web-2` > Terminate

4. **Security Groups** (optional)
   - Delete `alb-sg` and `web-sg` if you created dedicated ones and they are unused
