# Deploying-a-flask-or-Dash-application-on-AWS-using-Elastic-Beanstalk
**Steps to deploy a flask application on AWS**:

I am writing this post because none of the steps outlined in other forums provide a comfortable and detailed guide about deploying a ```flask``` or a ```dash``` application on AWS. In all cases, I ended up finding my own ways to resolve the issues I came across. Apart from providing the steps to  successfully host the flask application on AWS, this post also covers the following areas:
  1. Where does the hosted application reside on the AWS Instance.
  2. How to update changes to flask application code or any other data file related to the flask applicaion by connecting to the AWS Instance using SSH without having to re-deploy the application every time a code change is made.
  3. How to debug for errors on Elastic Beanstalk

Hosting a ```flask``` or a ```Dash``` app is pretty much the same on AWS. I am illustrating the steps w.r.to a sample ```flask``` application. The steps to host a ```Dash``` application is exactly the same as ```flask```

Before proceeding to the next steps, the mandatory requirement is to make sure that your ```flask``` application is working locally in your local python environment without any errors. Make sure you have a separate local python environment for your flask application. After doing so, follow the below steps:

# Step 1:
  Make sure the flask application file and the supporting files are put inside a separate local directory(in my example, I have placed all relevant files under a folder named "application_folder"). Rename your main flask script to ```application.py``` (This is very important, since AWS ElasticBeanstalk only considers to host flask or dash scripts named as ```application.py```). Also, have only the packages that are needed for your flask application in a separate python environment. Remove unnecessary packages that your application doesn't use just in case to avoid any conflicts during package installation 

# Step 2: (Proceed after completing Step 1)
  Activate the local python environment and export all the packages installed in that environment to a file called ```requirements.txt``` - (Make sure the file name matches exactly). After activating the environment, You can do this on a Mac or Linux system using the following command on terminal,

   ```
   pip freeze > requirements.txt
   ```
This command will create a ```requirements.txt``` file containing all the packages that are installed in your local environment.

# Step 3: (Proceed after completing Step 2)
  Now, we are all set to move on to AWS configurations. Firstly, we need a key pair in order to install AWS-EB CLI(Command Line Interface) which is a command line utility for interacting with ElasticBeanstalk and hosting our application. Follow these steps in order to create your own key pair using your AWS account and to download it offline and encrypt it. [Creating Key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)


# Step 4:  (Proceed after completing Step 3)
  In order to host an application using AWS Elastic Beanstalk(EBS), we need to install the AWS EB-CLI which is the command line utility to host the application on AWS. I do not wish to go in detail about these steps because all the steps have been outlined clearly on AWS documentation available here: [EB-CLI installation](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install-advanced.html).
  
Now that we have the EB-CLI installed, please check if the command ```eb``` is working in the terminal of your system before moving on to the next step. 

# Step 5:
Now, let’s go to the process of hosting our project with AWS ElasticBeanstalk. Remember that I have placed the flask application file(```application.py```), the ```requirements.txt``` file and all other supporting files under the directory named ```application_folder```.
## To create an EBS environment and deploy the Flask application:
  1. Navigate to the project directory on command line i.e. "application_folder"
  2. Initialize your EB CLI repository with the eb init command:
```
          eb init -p python-3.6 flask-app --region us-east-2  #Make sure to change your desired Python version for your application here
```
   3. Run eb init again to configure the default keypair so that you can connect to the EC2 instance running your application with SSH:
```
          eb init
```
   You will be prompted to select the already existing key pair that you downloaded in Step 3. Select the key pair and finish this step.
   
   4. Create an environment and deploy your application to it with eb create:
```
          eb create flask-env
```
   Now if you had followed all the steps properly, the application will get hosted in sometime. You can view the status in the command line itself. 


# Step 6:
In order to find the URL on which the EBS hosted the application for us, you have to login into your AWS account, navigate to the "Elastic Beanstalk" service and select the environment that we just created by navigating to the application name. 
The EBS dashboard after a successfull hosting would look like this:

![EBS Console after hosting](/images/AWS_scrn1.png)

Now click on the URL and it should take you to the hosted URL.


Now that I've outlined all the steps to successfully host a flask or dash application, what if you get any errors. What if you get any errors while uploading your application or what if you are not able to access your application on the URL even after a successful hosting. Down below, I will outline the steps on how to debug the errors and to identify whats the error if anything has gone wrong

Before we go into debugging, there is one point to note. While hosting an application using AWS Elastic Beanstalk, a new EC2 instance of type ```t2.micro``` automatically gets provisioned for your application that you host using the ElasticBeanstalk service. You can check this by navigating to EC2 inside your AWS Console.

You should also learn how to ```ssh``` (secure shell - to connect to your ElasticBeanstalk provisioned EC2 instance) into this EC2 instance. For steps on how to ```ssh```, refer this [link](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)




# Debugging for Errors:
**Step 1:**
    First and foremost, you have to make sure that you did not receive any errors while hosting the application using the ```eb``` command. You should get a message saying ```Application hosted successfully``` once the hosting process is complete. If you get any errors at this step, check for the root cause of the error and rectify it here itself.
    
**Step 2:**
  Now, if the application is hosted successfully without errors but for some reason, while accessing the URL of your ElasticBeanstalk hosted application on the browser, you receive any server error or any other kind of error, the first thing you should want to look into is the server logs of your Elastic Beanstalk application. 
  
 To retrieve the server logs of your application, login into your AWS account, navigate to the "Elastic Beanstalk" service and select the environment that we just created by navigating to the application name. Now click on **Logs** and then **Request Logs** to retrieve the last 100 lines or the entire logs. A folder named ```BundleLogs-SomeRandomNumber``` would get downloaded to your system now which contains all the logs of the entire server. 
 
 Now the question is, where to find your application errors from this ```BundleLogs-SomeRandomNumber``` folder. There will be lots of directories and sub-directories inside this logs folder.
 
 You will find your application logs from your downloaded logs folder ```BundleLogs-SomeRandomNumber``` in this path:
 
 ```~/BundleLogs-SomeRandomNumber/var/log/httpd/error_log```
 
 Now open up ```errror_log``` and you will be able to trace back what is the cause for your flask UI not getting rendered when you load the URL in the browser. Any errors that had happened, will get logged into this file.
 
 **Some Common Causes for Errors**:
 1. Some packages like nltk require certain corpuses to be downloaded manually after installation of the package. These downloads cannot be done by using ```pip install package-name```. For situations like these, though ```nltk``` package would have already been installed while hosting the application from ```requirements.txt``` file, you will have to manually do a ```ssh``` into your server and download the necessary corpuses for your application.
 2. Your application might run into memory or CPU limitations on the EC2 instance if your application does too much of processing. This totally depends on your application. While hosting the application using AWS Elastic Beanstalk, a ```t2.micro``` instance is provisioned for your application. This is the default instance configuration and consists of very minimal memory and CPU. You can identify if your application is running into such issues by monitoring the CPU and memory utilization of your EC2 instance after connecting to your instance through ```ssh``` while your application is being accessed on the browser. [Here](https://www.binarytides.com/linux-command-check-memory-usage/) is how to check the Memory and CPU utilization after connecting to your EC2 instance through ```ssh```.
 3. In any case, if you have found out that your application is running into memory or CPU limits, you can change the configuration of the EC2 instance of your ElasticBeanstalk application after the application is hosted. You can do so by navigating to your application environment inside Elastic Beanstalk Console, and clicking on ```Configuration```. Over here, you will be able to upscale your EC2 instance from ```t2.micro``` to any configuration you want.
 
##Some Pro tips
**Under which path does your hosted application reside inside your EC2 instance?**
 This is the path where your application resides in your EC2 instance. i.e. the project directory ```/opt/python/bundle/2/app```. Which also means, your application will get rendered from this path only. Trust me, it took a lot of time for me to identify this :)
 
 
 **Application re-deployment or rolling out updates:**
 We might want to push new changes to our project in the future. In such cases, it is not necessary to host your updated project again from scratch. You can simply do a ```ssh``` into your instance, navigate to the project directory which is ```/opt/python/bundle/2/app``` and edit your project directory for the updates. If you have lots of updates, say for example with ```application.py``` or any supporting files, you can transfer the updated files directly from your local machine to your EC2 instance using ```ssh```. A google search will tell you how to transfer files between your local machine and EC2 instance.
 
**Installing new packages in the future:**
Okay, now that ElasticBeanstalk has hosted my application for me like a breeze. But where is the python virtual environment on the EC2 instance where ElasticBeanstalk has installed all my packages from ```requirements.txt```. You will need this environment information if for example, you make any new updates to your ```application.py``` which include installing new packages that were not present in ```requirements.txt``` at the time of hosting your application. For changes like these where you might want to install new packages in the future for your application on your EC2 instance, simple do a ```ssh``` into the instance, 
do then do a ```cd /opt/python/run/venv/bin``` (this is the path of the virtual environment where ElasticBeanstalk installs all your packages), activate the environment using ```source ./activate``` and then do ```pip install your-packagename```.

Finally, make sure to restart your ElasticBeanstalk environment if you make any changes to project code or install new packages for the changes to take effect. 

                                                    CHEERS!
