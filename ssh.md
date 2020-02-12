# How To Setup an AWS Host as a Proxy Server Using Dynamic Port Forwarding

## Server Setup
1. Sign up for free tier AWS account
2. Click on "Services" and select "EC2"
3. Make sure you're in the region you want to launch your host in. Select the region from the dropdown list beside your username in the top navigation bar
4. Click on "Launch Instance"
5. Choose first AMI in list (Amazon Linux 2 AMI (HVM), SSD Volume Type)
6. Choose `t2.micro` as type since it's eligible for free tier
7. Click "Next" for instance details, storage, and tags
8. For security group configuration, choose "My IP" for source so that only you can ssh into this host. This can be changed later in Security Groups from EC2 dashboard:
	1. Select the security group created from EC2 launch wizard (probably named something like `launch-wizard-1``)
	2. Click on the Inbound tab
	3. Click on Edit to add/modify rules for inbound connections
	4. Add rule for each client IP you have
9. Launch host and download key pair. You can name it anything you want, like testkeypair.pem
10. Find your host's public DNS by going to Instances, then Description. It should look something like ec2-54-180-124-101.ap-northeast-2.compute.amazonaws.com

## Client Setup:
1. Download testkeypair.pem
2. Move it into `.ssh`: 
	```
	mv [download directory]/testkeypair.pem ~/.ssh
	```
3. Change directory to .ssh: 
	```
	cd ~/.ssh
	```
4. Change permissions for testkeypair.pem so only root user can read it. Otherwise, adding the key pair will throw error:
	```
	chmod 400 testkeypair.pem
	```
5. Add key pair: 
	```
	ssh-add testkeypair.pem
	```
6. Test ssh connection. If it doesn't work, call Saining:
	```
	ssh ec2-user@ec2-54-180-124-101.ap-northeast-2.compute.amazonaws.com
	```
7. Enable dynamic forwarding for port `1080`. `-C` specifies compression, `-D` is dynamic port forwarding:
	```
	ssh -C -D 1080 ec2-user@ec2-54-180-124-101.ap-northeast-2.compute.amazonaws.com
	```
8. Configure your browser to use proxy:
	1. Open Firefox
	2. Go to Preferences -> Network Settings and click Settings
	3. Select Manual proxy configuration 
	4. For SOCKS Host, enter `127.0.0.1`, and `1080` for Port
9. You should be able to access websites through the proxy now. Test connection by going to Google and see that it's in Korean (I picked South Korea as region for host)
