---
layout: post
title: Setting up Keycloak on AWS
---

When trying to set up a testing environment of [Keycloak](https://www.keycloak.org/) on AWS, I bounced between a bunch of different documentation, blog posts and guides setting it up. It was fairly frustrating as all of the guides I found either didn't cover all of what I wanted to do, were slightly outdated or didn't work in some minor way, or simply lead me down the wrong rabbit holes. I decided to document what worked for me in the end.

This guide will cover setting up a _testing_ environment of Keycloak on an AWS EC2 instance, and setting up HTTPS for it. The Keycloak installation described here will not yield a production ready environment, however the instructions for setting up HTTPS should be fairly similar.

## EC2

**Installing Keycloak**

### Launch an EC2 instance

Launch an EC2 instance to run the Keycloak server. I will use the `Ubuntu Server 18.04 LTS (HVM), SSD Volume Type` of the free `t2.micro` type.

Make sure the **Security Group** allows you to SSH into it, and opens the ports that Keycloak listens to[^security_groups]:

| Type            | Protocol | Port Range | Source          | Description         |
| :-------------- | :------- | :--------- | :-------------- | :------------------ |
| SSH             | TCP      | 22         | 0.0.0.0/0       | Terminal access     |
| Custom TCP Rule | TCP      | 8080       | 0.0.0.0/0, ::/0 | Keycloak HTTP       |
| Custom TCP Rule | TCP      | 8443       | 0.0.0.0/0, ::/0 | Keycloak SSL        |
| HTTPS           | TCP      | 443        | 0.0.0.0/0, ::/0 | Load Balancer HTTPS |

[^security_groups]: _Note that you **may** have a separate Security Group for the Load Balancer which only has HTTPS exposed, and you may restrict access to the Keycloak access, but for this testing environment that doesn't matter._

Create or choose a key pair that you have access to, you will need the `.pem` file to SSH into the instance.

### Download and install Keycloak on the machine

Go to the **EC2 Console** and wait until your instance reports that its `Instance State` is **Running** and the `Status Checks` are complete, note the **Private IP** in its Description-tab.

SSH into your newly created machine.

_Instructions for this pop up when selecting your instance and clicking `Connect`, if you get an error about permissions of the .pem file, make sure to run `chmod 400 keypair.pem`._

```bash
# Connect to the EC2 instance
ssh -i "keypair.pem" ubuntu@ec2-xx-xxx-xxx-xxx.region-x.compute.amazonaws.com

# Install Java
sudo apt update
sudo apt install default-jre
java -version

# Download and extract Keycloak
# https://www.keycloak.org/downloads.html
wget https://downloads.jboss.org/keycloak/8.0.1/keycloak-8.0.1.tar.gz
tar -zxvf keycloak-8.0.1.tar.gz
cd keycloak-8.0.1/

# Create the admin user
./bin/add-user-keycloak.sh -r master -u <username> -p <password>

# Find the Private IP of your instance in the EC2 Console, then
# start the Keycloak server
./bin/standalone.sh -b XXX.XX.XX.XXX
```

This will start the server in your current terminal session.

**You can stop the server by pressing <kbd>Ctrl</kbd> + <kbd>C</kbd> or closing the terminal session.**

To let it keep running once you close your SSH connection, press <kbd>Ctrl</kbd> + <kbd>Z</kbd> _(which pauses the process)_, then run `bg` _(which resumes it in the background)_ and `disown` _(which detaches it from your job list)_.

> Now you should be able to access Keycloak over **HTTP**. Get the **Public DNS (IPv4)** address from the EC2 Console, and enter it in your browser, followed by `:8080`, and make sure it works.
>
> http://ec2-xx-xxx-xxx-xxx.region-x.compute.amazonaws.com:8080
>
> This means Keycloak is running correctly, but if you try to enter the Administration Console, it will simply give you an error saying **HTTPS required**. This is what we will set up next.

## Route 53

**Setting up a domain name**

The next thing we will do is to set up a domain for our Keycloak instance.[^domain_name]

[^domain_name]: _Many guides I was trying to follow suggest using [Let's Encrypt](https://letsencrypt.org/) to get a certificate directly on the EC2 instance. However, at the time of writing (2020-01-30), the EC2 domains are blacklisted by Let's Encrypt. This is for good reason, since they can belong to you one day, and someone else the next._

Go to the **Route 53 Console**, and if you haven't already - **buy a domain name**. This might take a while to register.

## ACM

**Getting a certificate for HTTPS**

Go to **Amazon Certificate Manager** and follow the instructions to request a public certificate for your domain. Since we use Amazon's own Route 53 to manage our domain, we can let it verify the domain by automatically creating a `CNAME` record for us.

## Elastic Load Balancing

**Mapping our certificate to our Keycloak server**

A certificate managed by ACM cannot be directly assigned to an EC2 instance, it has to be assigned to a Load Balancer (ELB).

Go back to the EC2 Console and go to **Load Balancers** in the left hand side menu.

Create an **Application Load Balancer**,

---

Set it to be `internet-facing`.

Select **HTTPS** as the Load Balancer Protocol, and make sure to select the same VPC as your EC2 instance, and select all availability zones shown.

---

Select the certificate from ACM.

---

Select the same Security Group as we created before.[^security_groups]

---

Create a new **target group**.

Set the Target type to `Instance` and the protocol to `HTTPS` - for Health checks too.

---

Register your instance as a Target for the Load Balancer, and **make sure to set the Port to `8443`** to redirect incoming HTTPS requests to the secure Keycloak port.

> You should now be able to test the Load Balancer by going to the address shown in the **DNS name** of the Load Balancer:
>
> https://myloadbalancer-xxxxxxxxxx.region-x.elb.amazonaws.com
>
> Your Keycloak instance should now allow you to log in as admin with the secure connection, but your browser will tell you that the website is insecure.

## Route 53

**Routing traffic to your Load Balancer**

Now we are ready to assign our proper domain name to our secure Load Balancer.

Go back to the Route 53 Console and enter the **Hosted zone** of your domain name.

Click **Create Record Set** and enter the _subdomain_ you wish to dedicate to Keycloak as the **Name**. The **Type** should be an A-record.

Choose **Yes** in the Alias-section, and select your Load Balancer from the dropdown menu.

Click **Create**.

> Give the domain some time to propagate, but after a little while you should be able to access Keycloak securely, over HTTPS, without any warnings at the proper domain!

# Sources

- [Keycloak Documentation](https://www.keycloak.org/documentation.html)
- [AWS Route 53 to ELB Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-ec2-instance.html)
- [ACM Public Certificate Documentation](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html)
- [ELB HTTPS Listener Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html)
- [VPC Security Groups Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)
- [Troubleshooting Unreachable EC2 Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-console.html)
