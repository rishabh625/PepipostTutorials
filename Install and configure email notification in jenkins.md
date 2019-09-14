

# How To Install and Configure and Send Email Notifications in Jenkins



### Introduction

Jenkins is an open source tool used to perform Continuous Integration and Continuous Development by automating build/test/deployments.Using Jenkins you can easily automate the complete process of build to deploy across different environments(Dev/SIT/UAT/Production) by creating different Jobs or pipeline.
When you create this jobs there must be some way to get the team and yourself notified about build /test /deploy status.This is where Email Notifications can be used, Jenkins provide plugins to send Emails.You just need to install and configure the plugin correctly.

We will help you with this! :)

You can configure email notifications in your jobs in two ways:
1) Default Email Notifier(Email-Notification)
2) Extended Email Notification(Editable E-mail Notificatiom)

In Extended Email Notifications you can set triggers(eg build is unstable,or before build),specify email subject,content,and recipients. 

## Prerequisites

This tutorial is based on jenkins version 2.194 installed on localhost and  using http port 8071

## Step 1 — Installing Email Extension Plugin.

1) Open Jenkins using using the following URL: http://localhost:8080/ on any browser(in my case port is 8071)
2) Click on Manage Jenkins

    ![manage jenkins_img](https://i.imgur.com/kioMbcD.png)

3) Click on Manage Plugins.

   ![manage_plugins_img](https://i.imgur.com/OvhurfE.png)

4) Select Email Extension and 	
Email Extension Template Plugin and click install without restart

   ![installation_img](https://i.imgur.com/NzEdcZ7.png)

#### Once the plugin is installed lets configure it with smtp servers

   ![configure_img](https://i.imgur.com/OvhurfE.png)


## Step 2 — Configure Email Notifications

1) Click on Manage Jenkins and then Configure system

   ![configure_system_img](https://i.imgur.com/qoGEqQd.png)

2) Scroll below till E-mail Notification and click on advanced. 
Setup it as shown in below screenshot and save it.
   
   ![configure_system_img](https://i.imgur.com/A1Slumx.png)

You can test configurations by entering recipient email id and clicking on test configuration.If all is good it will show Email sent successfully,
You may get error while testing configurations, below is possible errors and solution to it.

  ![test_configuration_img](https://i.imgur.com/bV4SeuC.png)

In step 4 we will configure Extended Email Notification where in we can configure email content and set triggers.

## Step 3 — Configuring Email-Notification in our jobs or pipeline
1) Go to your jenkins home page and click on created job(for me its gradlerun)

    ![configure_email_notification](https://i.imgur.com/MSNVFok.png)

2) Click on Configure 
 
    ![configure_email_notification](https://i.imgur.com/OvhurfE.png)

3) Once your job configuration opens scroll down to add post-build action and select Email Notifications 

    ![configure_jekins_job](https://i.imgur.com/pwYWHhC.png)

4) Enter recipients and check Send e-mail for every unstable build and save it.

    ![configure_jekins_job](https://i.imgur.com/o4i1v59.png)

Here you go! Run your job and you start recieving email.

## Optional Steps

## Step 4 —  Configure Extended Email Notification

From step 2 scroll below till Extended E-mail Notification as setup it as shown in screenshot you can set your own triggers also, every option or setting in self explanatory , configure it as per your requirement.

   ![configure_extended_email](https://i.imgur.com/tzxdg5E.png)

   ![configure_extended_email](https://i.imgur.com/KJVk88j.png)

   ![configure_extended_email](https://i.imgur.com/n4Cl9l2.png)

That's it you have successfully configured email settings, Lets set this up in our pipeline or jobs.

## Step 5  — Configure Extended Email Notification in jenkins job 

From step 3 in add post-build action and select Editable Email Notifications. 
    
   ![configure_extended_email_injob](https://i.imgur.com/AhuJ5u8.png)

Once it is added in build action save it.
    
   ![configure_extended_email_injob](https://i.imgur.com/IflE7lb.png)

Now you will recieve custom email for each of your job actions. 

## Problems that you might face while sending test email

### Error 1

Unable to connect to smtp server

``` java 
java.net.ConnectException: Connection timed out: connect
	at java.net.DualStackPlainSocketImpl.waitForConnect(Native Method)
        Caused: javax.mail.MessagingException: Could not connect to SMTP host: smtp.pepipost.com, port: 25;
```

Solution : Try telnetting smtp server on different port 587,2525,25 etc. if that works change smtp port in your configuration.

### Error 2

If using port 587 SSL must be turned on 

```java
com.sun.mail.smtp.SMTPSendFailedException: 530 5.7.0 Must issue a STARTTLS command first. x8sm31507336pfn.106 - gsmtp
```
Solution : Turn on SSL from configurations.

### Error 3

If your jenkins is running on http port you may get below error.

```java 
javax.net.ssl.SSLException: Unrecognized SSL message, plaintext connection?
	at sun.security.ssl.InputRecord.handleUnknownRecord(Unknown Source)
```
Solution : Either host jenkins or https or change port to 465,

### Error 4
If you are using gmail as smtp server you may below error

```java 
javax.mail.AuthenticationFailedException: 535-5.7.8 Username and Password not accepted. Learn more at
535 5.7.8  https://support.google.com/mail/?p=BadCredentials r2sm51378882pfq.60 - gsmtp
```
Solution : If you have entered wrong password or username correct it.And in gmail allow less secure apps to login or install jenkins to use https.

## Conclusion
Now you can successfully configure email notifications for your build/test/deploy pipelines.
