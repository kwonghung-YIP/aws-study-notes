An example to create a pair of nginx EC2 instance that services static content with application loadbalancer upfront.

1. Create the following security groups under EC2 panel:
   * SSH-Remote (only allow ssh from my public IP)
   * HTTPS-ALB (allow in-bound HTTP/S 80,443 ports traffic from any IPs)
   * HTTPS-behind-ALB (allow in-bound HTTP/S 80,443 ports traffic only from HTTPS-ALB security group)

1. Create the AMI image
   1. Create a new EC2 instance **nginx-seed** from the "AWS Linux 2023 AMI" image.
   1. Install the nginx and enable it as systemctl service.
   1. Stop the EC2 instnace and create a new AMI image from it.

1. Create a pair of EC2 intances from **nginx-seed** image

1. Create a new target group

1. Create a new Application Load Balancer

1. Testing
