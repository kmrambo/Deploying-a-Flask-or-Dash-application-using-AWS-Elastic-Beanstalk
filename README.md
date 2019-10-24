# Deploying-a-flask-or-Dash-application-on-AWS-using-Elastic-Beanstalk
Steps to deploy a flask application on AWS

I am writing this post because none of the steps outlined in other forums provide a comfortable and detailed guide about deploying a flask or a dash application on AWS. In all cases, I ended up finding my own ways to resolve the issues I came across. Apart from providing the steps to  successfully host the flask application on AWS, this post also covers the following areas:
  1. Where does the hosted application reside on the AWS Instance.
  2. How to update changes to flask application code or any other data file related to the flask applicaion by connecting to the AWS Instance using SSH.
  3. How to debug for errors on Elastic Beanstalk

Hosting a flask or a Dash app is pretty much the same on AWS. I am illustrating the steps w.r.to a sample flask application. The steps to host a Dash application is exactly the same, except that the flask code has to be replaced by Dash code.

Before proceeding to the next steps, the mandatory thing is to make sure that your flask application is working locally without any errors. Make sure you have a separate local python environment for your flask application and the flask application should work correctly without any errors in that local environment. After doing so, follow the below steps:

# Step 1:
  Make sure the flask application file and the supporting files are put inside a separate local directory(in my example, I have placed all relevant files under a folder named "application_folder"). Rename your flask application to "application.py" (make sure you have the same file name too). Also, have only the packages that are needed for your flask application in a separate python environment. 

# Step 2:
  Activate the local python environment and export all the packages installed in that environment to a file called "requirements.txt" - (Make sure the file name matches exactly). After activating the environment, You can do this on a Mac system using the following command on terminal,

   ```
   pip freeze > requirements.txt
   ```
This command will create a "requirements.txt" file containing all the packages that are installed in your environment.

# Step 3:
  Now, we are all set to move on to AWS configurations. Firstly, we need a key pair in order to install AWS-EB CLI which is a command line utility for hosting our application. Follow these steps in order to create your own key pair using your AWS account and to download it offline and encrypt it. [Creating Key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)


# Step 4:  
  In order to host an application using AWS Elastic Beanstalk(EBS), we need to install the AWS EB-CLI which is the command line utility to host the application on AWS. I do not wish to go in detail about these steps because all the steps have been outlined clearly on AWS documentation available here: [EB-CLI installation](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install-advanced.html).
  
Now that we have the EB-CLI installed, please check if the command "eb" is working in the terminal of your system before moving on to the next step. 

# Step 5:
Now, let’s go to the process of uploading the project into the AWS instance. Remember that I have placed the flask application file(application.py), the "requirements.txt" file and all other supporting files under the directory named "application_folder".
  ## To create an EBS environment and deploy the Flask application:
      1. Navigate to the project directory on command line i.e. "application_folder"
      2. Initialize your EB CLI repository with the eb init command:
```
          eb init -p python-3.6 flask-app --region us-east-2
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
In order to find the URL on which the EBS hosted the application for us, you have to login into your AWS account, navigate to th "Elastic Beanstalk" service and select the environment that we just created by navigating the application name. 
The EBS dashboard after a successfull hosting would look like this:

![EBS Console after hosting](/images/AWS_scrn1.png)

Now click on the URL and it should take you to the hosted URL.


Now that I've outlined all the steps to successfully host a flask or dash application, what if you get any errors. What if you get any errors wile uploading your application or what if you are not able to access your application on the URL even after a successful hosting. Below, I will outline the steps on how to debug the errors and to identify whats the error if anything has gone wrong

I'm still updating the further steps. Watch out for more updates!
    
