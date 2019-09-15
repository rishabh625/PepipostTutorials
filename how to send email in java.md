

# How To Send Email in Java.



### Introduction

Sending Email is the most common and necessary requirement for most of the applications. Java provides Java Mail API a platform-independent and protocol-independent framework to build mail and messaging applications.

The JavaMail reference implementation is licensed under the Common Development and Distribution License (CDDL) v1.1 and GNU General Public License (GPL) v2 with Classpath Exception.  

In this guide, you will get detailed steps on how to setup JavaMail in your Java Project And implement JavaMail API to build and send emails on SMTP protocol.

## Prerequisites

Eclipse IDE

Java Runtime Environment

## Step 1 — Create new Maven Project 

Click on File > New > Project > Maven > Maven Project.

![new maven project](https://i.imgur.com/m7yMKFJ.png)

Check Create  a simple project(skip archetype selection) and click finish.

![archetype of maven](https://i.imgur.com/UCCklVj.png)

Give Artifact Id and group Id to your project.

![artifact and group id maven](https://i.imgur.com/I8I8Uas.png)

This will give default project structure as below

![project_structure](https://i.imgur.com/4mL0MRa.png)

A default pom.xml is created

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>SendEmail</groupId>
   <artifactId>SendEmail</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   <name>SendEmail</name>
   <description>This artiface will send emails using Java Mail API</description>
</project>

```
Add following dependencies in your pom.xml file.

```xml
<dependencies>
  	<dependency>
			<groupId>com.sun.mail</groupId>
			<artifactId>javax.mail</artifactId>
			<version>1.6.2</version>
		</dependency>
  </dependencies>

```
This will add javax.mail.jar 1.6.2 into your project resources.

Final pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>SendEmail</groupId>
  <artifactId>SendEmail</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>SendEmail</name>
  <description>This artiface will send emails using Java Mail API</description>
  <dependencies>
  	<dependency>
			<groupId>com.sun.mail</groupId>
			<artifactId>javax.mail</artifactId>
			<version>1.6.2</version>
		</dependency>
  </dependencies>
</project>
```


## Step 2 — Create Java Class
1) Right click on project and create new package and name it com.sendemail

![java package](https://i.imgur.com/yDPkth5.png)

2) Right click on package and create SendMail class
   check to create main method 

![java class](https://i.imgur.com/55VUyVC.png)

In our code we would require to import following packages and class.

1) import java.util.Properties;
   
   The Properties class represents a persistent set of properties. The Properties can be saved to a stream or loaded from a stream.

2) import javax.mail.Message;

   This class models an email message.
   To send a message,subclass of Message (e.g. MimeMessage) is instantiated, the attributes and content are filled in, and message is sent using the Transport.send method.

3) import javax.mail.MessagingException;

   This is base class for all exceptions thrown by the Messaging classes

4) import javax.mail.PasswordAuthentication;

    This class is simply a repository for a user name and a password.

5) import javax.mail.Session;
   
    Session class represents a mail session.

6) import javax.mail.Transport;
    
   This is abstract class that models a message transport.

7) import javax.mail.internet.InternetAddress;

   This class represents an Internet email address using the syntax of 
   RFC822

8)  import javax.mail.internet.MimeMessage;

    This class represents a MIME style email message. It implements the Message abstract class and the MimePart interface.

 Below is the full Java code to send emails using gmail SMTP server, with description of each line .

``` java
package com.sendemail;

import java.util.Properties;

import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

public class SendMail {

	public static void main(String[] args) {

		// Recipient's email ID needs to be mentioned.
		String to = "fromaddress@gmail.com";

		// Sender's email ID needs to be mentioned
		String from = "toaddress@gmail.com";

		// Assuming you are sending email from through gmails smtp
		String host = "smtp.gmail.com";

		// Get system properties
		Properties properties = System.getProperties();

		// Setup mail server
		properties.put("mail.smtp.host", host);
		properties.put("mail.smtp.port", "465");
		properties.put("mail.smtp.ssl.enable", "true");
		properties.put("mail.smtp.auth", "true");
		
		// Get the Session object.// and pass username and password
		Session session = Session.getInstance(properties, new javax.mail.Authenticator() {

			protected PasswordAuthentication getPasswordAuthentication() {

				return new PasswordAuthentication("fromaddress@gmail.com", "*******");

			}

		});
		
		// Used to debug SMTP issues
		session.setDebug(true);
		
		try {
			// Create a default MimeMessage object.
			MimeMessage message = new MimeMessage(session);

			// Set From: header field of the header.
			message.setFrom(new InternetAddress(from));

			// Set To: header field of the header.
			message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));

			// Set Subject: header field
			message.setSubject("This is the Subject Line!");

			// Now set the actual message
			message.setText("This is actual message");

			System.out.println("sending...");
			// Send message
			Transport.send(message);
			System.out.println("Sent message successfully....");
		} catch (MessagingException mex) {
			mex.printStackTrace();
		}

	}

}

```
If you want to send HTML content replace ``` 
message.setText("This is actual message")``` with below code

```java
    // Send the actual HTML message.
	   message.setContent(
              "<h1>This is actual message embedded in HTML tags</h1>",
             "text/html");
```


## Step 3 — Test Java Code 

Run Java Application and email will be sent to recipient. Your console will look like this.

![test result](https://i.imgur.com/w6rbbnn.png)

You Have successfully sent email using your Java Code.
Now let's send file attachment in your email.

## Optional Steps

## Step 4 — Send Email with Attachement.

To send email with attachment will have to import few more classes.

1) import java.io.File and 
import java.io.IOException

    File Class is an abstract representation of file and directory pathnames and IOException class is general class of exceptions produced by failed or interrupted I/O operations.

2) import javax.mail.internet.MimeMultipart
    
    This Class is an implementation of the abstract Multipart class that uses MIME conventions for data.

3) import javax.mail.internet.MimeBodyPart

    This class represents a MIME body part.
MimeBodyPart uses the InternetHeaders class to parse and store the headers of that body part.



Below is the java code to send attachment in email.

The code has two MIME Body part one carrying attachment and other carrying text for email, this are added in Multipart, then this multipart is used to set as content of message.

``` java 
Multipart multipart = new MimeMultipart();

			MimeBodyPart attachmentPart = new MimeBodyPart();
			
			MimeBodyPart textPart = new MimeBodyPart();

			try {

				File f =new File("H:\\fakepath\\file1.PNG");
				
				attachmentPart.attachFile(f);
				textPart.setText("This is text");
				multipart.addBodyPart(textPart);
				multipart.addBodyPart(attachmentPart);

			} catch (IOException e) {

				e.printStackTrace();

			}

			message.setContent(multipart);

```

At the End of the article Find Running Java Code to send attachments in Email. 


## Conclusion

Now You have implemented Java Mail API in your project and you can send emails using SMTP Servers in your Project.
#### Java Code to send attachment in Email
``` java
package com.sendemail;

import java.io.File;
import java.io.IOException;
import java.util.Properties;

import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.Multipart;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeBodyPart;
import javax.mail.internet.MimeMessage;
import javax.mail.internet.MimeMultipart;

public class SendMail {

	public static void main(String[] args) {

		// Recipient's email ID needs to be mentioned.
		String to = "toaddress@gmail.com";

		// Sender's email ID needs to be mentioned
		String from = "fromaddress@gmail.com";

		// Assuming you are sending email from through gmails smtp
		String host = "smtp.gmail.com";

		// Get system properties
		Properties properties = System.getProperties();

		// Setup mail server
		properties.put("mail.smtp.host", host);
		properties.put("mail.smtp.port", "465");
		properties.put("mail.smtp.ssl.enable", "true");
		properties.put("mail.smtp.auth", "true");

		// Get the Session object.// and pass 
		Session session = Session.getInstance(properties, new javax.mail.Authenticator() {

			protected PasswordAuthentication getPasswordAuthentication() {

				return new PasswordAuthentication("youremail@gmail.com", "*******");

			}

		});
		//session.setDebug(true);
		try {
			// Create a default MimeMessage object.
			MimeMessage message = new MimeMessage(session);

			// Set From: header field of the header.
			message.setFrom(new InternetAddress(from));

			// Set To: header field of the header.
			message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));

			// Set Subject: header field
			message.setSubject("This is the Subject Line!");
			
			
			Multipart multipart = new MimeMultipart();

			MimeBodyPart attachmentPart = new MimeBodyPart();
			
			MimeBodyPart textPart = new MimeBodyPart();

			try {

				File f =new File("H:\\pepipost_tutorials\\javaemail1.PNG");
				
				attachmentPart.attachFile(f);
				textPart.setText("This is text");
				multipart.addBodyPart(textPart);
				multipart.addBodyPart(attachmentPart);

			} catch (IOException e) {

				e.printStackTrace();

			}

			message.setContent(multipart);

			System.out.println("sending...");
			// Send message
			Transport.send(message);
			System.out.println("Sent message successfully....");
		} catch (MessagingException mex) {
			mex.printStackTrace();
		}

	}

}

```

