
# Testing

## Confirm load balancing
- Open the ALB DNS name in your browser
- Refresh 10+ times
- The page should alternate between instances (different instance IDs / AZs)

## Simulate a failure
1. Stop `web-2` instance (or stop Apache on it)
2. Wait a minute or two
3. Target group should mark it **unhealthy**
4. ALB should keep serving traffic from the healthy instance

## Re-enable
- Start the instance again (or restart Apache)
- Wait for health checks to pass
- It should become healthy again

