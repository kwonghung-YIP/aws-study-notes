An example to create a pair of nginx EC2 instance that services static content with application loadbalancer upfront.

1. Create the following security groups under EC2 panel:
   * allow-all-out (no in-bound traffic, allow all out-bound traffic)
   * SSH-Remote (allow in-bound ssh from my public IP, no out-bound traffic)
   * HTTP/S-ALB (allow in-bound HTTP/S (80,443) ports traffic from any IPs. allow all out-bound traffic)
   * HTTP/S-behind-ALB (allow in-bound HTTP/S 80,443 ports traffic from HTTPS-ALB, no out-bound traffic)
   * nginx-shared-EFS (allow in-bound NFS traffic from nginx-shared-mount, allow all out-bound traffic)
   * nginx-shared-mount (no in-bound traffic, allow NFS out-bound traffic for any IPv4)

1. Create the AMI image
   1. Create a new EC2 instance **nginx-seed** from the "AWS Linux 2023 AMI" image.
   1. Install the nginx and enable it as systemctl service, add following script in User Data.
      ```bash
      #!/bin/bash
      sudo yum update -y
      sudo yum install -y nginx
      sudo systemctl enable nginx
      sudo systemctl start nginx
      ```
   1. Stop the EC2 instnace and create a new AMI image from it.

1. Create EFS drive
   1. 

1. Create a pair of EC2 intances from **nginx-seed** image
   1. Create following files in efs shared drive
      ```bash
      sudo mkdir -p /mnt/efs/fs1/site-content/site
      echo "<h1>Shared content in EFS drive!</h1>" | sudo tee /mnt/efs/fs1/site-content/site/index.html"
      sudo mkdir -p /mnt/efs/fs1/site-content/conf
      cat <<EOF | sudo tee /mnt/efs/fs1/conf/efs-shared.conf
      location /site {
         root /usr/share/nginx/site-content;
      }
      EOF
      ```
   
   1. Create the static content
      ```bash
      sudo mkdir -p /usr/share/nginx/html/test
      echo "Hello world from `hostname`!" | sudo tee /usr/share/nginx/test/test.txt
      sudo ln -s /mnt/efs/fs1/site-content /usr/share/nginx/site-content
      sudo ln -s /mnt/efs/fs1/conf/efs-shared.conf /etc/nginx/default.d/efs-shared.conf
      sudo systemctl restart nginx
      ```

   1. Test the nginx content
      ```bash
      # default nginx welcome page
      curl http://localhost
      # page with the local hostname
      curl http://localhost/test/test.txt
      # shared page in the efs drive 
      curl http://localhost/site/ 
      ```

1. Create a new target group

1. Create a new Application Load Balancer

1. Testing
