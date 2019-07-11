With this tutorial we'll try to make two `EC2` instances share the same `EFS` to launch a simple web server.<br>

We can skip Part 1 if you have a User and Group already provisioned via `IAM`.<br>

## Part 1 : create a User and a Group using IAM

- We log into your `AWS` management console using `$ https://console.aws.amazon.com.`<br>

I'm using `MFA` to secure my root account access coupled with `Google Authenticator` on my `Android` smartphone.<br>

We can bypass this step and login normally to `AWS` Management Console.<br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-1.jpg](https://i.postimg.cc/L5F2KQwp/isaac-arnault-AWS-1.jpg)](https://postimg.cc/nj26q2nR)

</p>
</details>

- We go to Services > IAM > Users > Add user<br>

<li>User name : user-1</li><br>

<li>Access type : Programmatic access</li><br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-16.png](https://i.postimg.cc/Mpdv5JTN/isaac-arnault-AWS-16.png)](https://postimg.cc/fVSzWFYf)

</p>
</details>

<b> Next : Permissions > Create group</b><br>

<li>Group name : Developers</li><br>

<b>Administrator Access > Create group</b><br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-17.png](https://i.postimg.cc/cJC65ktH/isaac-arnault-AWS-17.png)](https://postimg.cc/Ty8RK99M)

</p>
</details>

<b>Next : Tags</b><br>

<li>Key: dev-1 | Value: name of the developer</li><br>

<b>Create user</b><br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-18.png](https://i.postimg.cc/sXpzn5mx/isaac-arnault-AWS-18.png)](https://postimg.cc/hzPNvzHR)

</p>
</details>

<b>Download .csv</b> (you gonna use these credentials later on this tutorial)<br>

- We write down our Access key ID and Secret access key > close the window<br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-28.png](https://i.postimg.cc/WzPg3281/isaac-arnault-AWS-28.png)](https://postimg.cc/FdD7CXwM)

</p>
</details>

- Now in Groups we should have one group named Developers which should list <b>user-1</b>.

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-20.png](https://i.postimg.cc/TPfZch1q/isaac-arnault-AWS-20.png)](https://postimg.cc/dhNHss8L)

</p>
</details>

<hr>

## Part 2 : create an Elastic File System (EFS)

Sercices > Storage > EFS<br>

<b>Configure file system access</b><br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-37.png](https://i.postimg.cc/bJXpNRMP/isaac-arnault-AWS-37.png)](https://postimg.cc/06CLV7PX)

</p>
</details>

<b>Configure optional settings</b><br>

Optional - We'll enable encryption of data at rest for this tutorial.<br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-38.png](https://i.postimg.cc/ZYx44pLk/isaac-arnault-AWS-38.png)](https://postimg.cc/kDGkyVZw)X)

</p>
</details>

<b>Review and create</b><br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-39.png](https://i.postimg.cc/65HKGKSY/isaac-arnault-AWS-39.png)](https://postimg.cc/4mhqrDX9)

</p>
</details>

<hr>

## Part 3 : deploy 2 EC2 instances using a custom boot strap script<br>

Services > EC2<br>

- In "Create Instance" section, click on "Launch Instance"<br>

We gonna choose 2 instances <br>

- We welect Amazon Linux 2 AMI (HVM), SSD Volume Type<br>

- Instance type: choose t2.micro (Free tier eligible). Instance comes with 1vCPU and 1 GiB (memory).<br>

<b>Next: Configure instance details</b>

We choose to deploy 2 instances and we provision the<b> Advanced details </b> section with the following script:<br>

<details>
<summary>🔵 See script</summary>
  
<p>
  
'''
#!/bin/bash<br>
yum update -y<br>
yum install httpd -y<br>
service httpd start<br>
chkconfig httpd on<br>
yum install amazon-efs-utils -y

'''
</p>
</details>

- We leave all fields as they're by default, we just Enable termination protection.<br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-40.png](https://i.postimg.cc/zD06xjjf/isaac-arnault-AWS-40.png)](https://postimg.cc/QVKftcvL)

</p>
</details>

<b>Next : Add Storage</b><br>

- We leave all fields as they're by default.<br>
 
<b>Next : Configure Security Group</b><br>

- We create a new security group > Security group name: dev-group > Description : Developers Security Group > Review and launch > Launch > Create New Key Pair > Key Pair Name : EC2KP > Download Key Pair.

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-21.png](https://i.postimg.cc/XYWd37JH/isaac-arnault-AWS-21.png)](https://postimg.cc/PP6PQH1Y)

</p>
</details>

<b>Launch Instances > View Instances</b><br>

- We rename both instances respectively to "EC2 - EFS - Instance 1" and "EC2 - EFS - Instance 2".<br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-42.png](https://i.postimg.cc/597KBt4Y/isaac-arnault-AWS-42.png)](https://postimg.cc/z3hk58R8)

</p>
</details>

- At this point of the tutorial, we should have one Elastic File System (EFS), two running EC2 instances, a User and a Group created via IAM.

<hr>

## Part 4 : use the Command Line Interface to connect to both EC2 instances

We should remember that we've downloaded an EC2KP.pem file earlier. We will now move that file to a newly created directory.<br>

Ctrl + Alt + T to open a new CLI window<br>

`$ cd Desktop > $ mkdir SSH` - This to create an SSH directory to store our Key Pair (credentials).<br>

`$ cd Downloads` > `$ sudo mv /home/zaki/Downloads/EC2KP.pem /home/zaki/Desktop>SSH`<br>

- Go to your SSH directory and check that the file persists there : `$ cd Desktop/SSH` > ls<br>

- We change the permissions to .pem file, ie: `$ chmod 400 EC2KP.pem`.<br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-23.png](https://i.postimg.cc/4xbCDphh/isaac-arnault-AWS-23.png)](https://postimg.cc/zyBPWb7J)

</p>
</details>

- We will now connect to both EC2 instances using our `CLI` : we open two distinct windows<br>

- Use : `$ ssh ec2-user@your-ipv4-public-address -i EC2KP.pem`.<br>

- Type "yes" when prompted by the `CLI`<br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-24.png](https://i.postimg.cc/jj5X0d3V/isaac-arnault-AWS-24.png)](https://postimg.cc/qNPn20dQ)

</p>
</details>

- Go in root mode : `$ sudo su` and use `$ aws s3 ls`. The last command should return "Unable to locate credentials. We can configure credentials by running "aws configure".<br>

To use your provided credentials use : `$ aws configure` <br>

Remember that we wrote down our `Access Key ID` and `Secret access key` when creating our EC2 Instances. We use the provided credentials.<br>

- We connect to both `EC2` instances using the following command:

`$ ssh ec2-user@your-ipv4-address -i EC2KP.pem`

- We provide Access Key ID > AWS Secret Access Key > Default region name (use the Availability Zone of our EC2 instance, ie : us-east-1) > default output format : we can use "text" or "json". In this tutorial we use "json".<br>

<details>
<summary>🔴 See output</summary>
<p>  
  
[![isaac-arnault-AWS-43.png](https://i.postimg.cc/25SHNgmJ/isaac-arnault-AWS-43.png)](https://postimg.cc/qNWc8bNX)

</p>
</details>

<hr>
<b>Important</b><br>
If buckets do not show up, we can go to Users > Security credentials > Create a new access key. Or we can create a new EC2 instance and restart the procedure in our `AWS` CLI.<br>

When you Create access key, you'll have to download a file "access.Keys.csv".<br>
<br>

## Part 5 : Mount the EFS on both EC2 instances<br>

On <b>EC2 - EFS - Instance 1</b> SSH, use :

`$ ssh ec2-user@your-ipv4-address -i EC2KP.pem`<br>

`$ sudo su`<br>

`$ cd /var/www/html`<br>

`$ mount -t efs -o tls fs-ID:/ /var/www/html`<br>

We gonna create a single web page in order to check later on if it appears on the other EC2 instance SSH.<br>

`$ cd html`

`echo "<html><h1>Hello World</h1></html>" > index.html`<br>

To verify that the web page was correctly created we can perform a simple `$ ls` or we can connect to our EC2 - EFS - Instance 1 `IPv4 Public IP` in our browser.<br>

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-44.png](https://i.postimg.cc/j22XGfb7/isaac-arnault-AWS-44.png)](https://postimg.cc/8f8Mvjsp)

</p>
</details>

To ckeck if <b>EC2 - EFS - Instance 2</b> is sharing the same `EFS` as <b>EC2 - EFS - Instance 1</b>, we perform the following commands on our <b>EC2 - EFS - Instance 2</b> SSH:

`$ ssh ec2-user@your-ipv4-address -i EC2KP.pem`<br>

`$ sudo su`<br>

`$ cd /var/www/html`<br>

`$ mount -t efs -o tls fs-ID:/ /var/www/html`<br>

Note that we did not create an <b>index.html</b> file. Perform a simple `ls` and check if the <b>index.html</b> created in <b>EC2 - EFS - Instance 1</b> appears.<br>

If the file appears, it means that both EC2 instances share the same `EFS`. To make sure everything went fine, we can perform in our <b>EC2 - EFS - Instance 2</b> SSH:<br>

`$ echo "This tutorial works" > testfile.txt`

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-46.png](https://i.postimg.cc/P5XmcZQr/isaac-arnault-AWS-46.png)](https://postimg.cc/0b4MKz4L)

</p>
</details>

You can also use <b>EC2 - EFS - Instance 1</b> and <b>EC2 - EFS - Instance 2</b> `IPv4 Public IP` in our web browser. Both queries should append a unique index.html file and retrieve the same web page.

<details>
<summary>🔴 See output</summary>
<p>  

[![isaac-arnault-AWS-46.png](https://i.postimg.cc/xC2jxGL6/isaac-arnault-AWS-46.png)](https://postimg.cc/94Ljz789)

</p>
</details>

<hr>

That's all for now guys. I hoped you enjoyed this gist. Feel free to fork it and to spread a word about it. Thanks.
