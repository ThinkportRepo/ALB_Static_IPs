# Enabling ALB throught static IP addresses

The solution is based on https://aws.amazon.com/de/blogs/networking-and-content-delivery/using-static-ip-addresses-for-application-load-balancers/

# In short

ALB uses domain names to access. Sometimes you need exactly ips for different reasons. IPs for domain names are dynamic and managed by AWS. So the solution suggests to create a NLB in front of ALB with static IPs (EIP basically which are passed as parameters to the template) and forward traffic from it to ALB.
Since IPs of ALB can change (and target of NLB cannot be domain), there is Lambda, which runs every minute, checks if ALB got new IPs and assigns it as targets to NLB

# Precautions

So there can be possible downtime of the application:
Lambda has checked ALB. The IPs are still the same. Right after the check IPs have been changed. So until the next check (in 1 minute) application basically won't work.
We cannot invole Lambda more often that once a minute, so the only solution is an EC2 Instance with constantly running applcation.

ALB IPs can change quite frequently (there are described cases with twice a day)

# Implementation details

Cloudformation template also includes Network Loadbalancer.
ALB must already exist and be private.

Lambda zip in Cloudformation is taken from public repository from the article but in case of any problems, it's also add to this repo