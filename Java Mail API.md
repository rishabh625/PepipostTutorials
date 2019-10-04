# JavaMailAPI

### Introduction

JavaMail API provides a platform-independent and protocol-independent framework to build mail and messaging client applications using Java.

Java Mail API is generic and provides abstract classes containing objects that are being defined in the email system and act as an interface to email applications.

JavaMail API is generic for reading and sending emails. And require service providers to interact with mail servers running on transport protocol like:

1. Simple Mail Transfer Protocol(SMTP)
SMTP is internet standard for sending and receiving Email across IP networks it uses TCP with most popularly port 25 to send and receive. SSL can secure connections. JavaMail API has packages and a core classes;

    ```java 
     com.sun.mail.smtp
    ```
This acts as an SMTP protocol provider to connect any SMTP servers.

2. Internet Message Access Protocol(IMAP)
IMAP server listens mostly on port 143. And, IMAP over SSL uses the assigned port 993. Using IMAP protocol provider in JavaMail API:

    Package 
    ```java
     com.sun.mail.imap
    ```

3. Post Office Protocol (POP)
POP3 is an internet standard protocol used by local email clients to access email from remote server over TCP/IP.POP3 server listens on port 110. Following is the POP3 protocol provider in JavaMail API:

   ```java 
   com.sun.mail.pop3
   ```

## Installing JavaMail API

To use JavaMail API, you would require to add two jars in your CLASSPATH:
1. [mail.jar](http://www.java2s.com/Code/Jar/m/Downloadmailjar.htm)
2. [activation.jar](http://www.java2s.com/Code/Jar/a/Downloadactivationjar.htm).

If you are using Maven, Create a Maven Project refering [this](https://pepipost.com/tutorials/send-email-in-java-using-gmail-smtp/) link.

```
Note: JavaBeans Activation Framework (JAF) extension that provides the (activation.jar) javax.activation package is already present in Java SE 6 and later versions.
```


## Send Emails

To Send Email, you will need an SMTP server. In case you don't have a SMTP server, you can install one or just use some SMTP service providers like Pepipost.

Here, are few helpful tutorials in case you want to install the SMTP server on your machine:
  - [How To Install Postfix CentOS 7](https://pepipost.com/tutorials/install-centos-postfix/)
  - [Installing Postfix as a Send-Only SMTP Server on Debian 10 (2019)](https://pepipost.com/tutorials/install-postfix-as-send-only-smtp-server/)


### Sample Code to Send Email Using SMTP

```java
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

        // Mention the Recipient's email address
        String to = "fromaddress@gmail.com";

        // Mention the Sender's email address
        String from = "toaddress@gmail.com";

        // Mention the SMTP server address. Below Gmail's SMTP server is being used to send email
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

Let's break down the above code into three parts, for better understanding:

1. Getting The Mail Session.
The session is the context of how you are going to interact with the mail host. Helpful in debugging output from the mail host, timeouts, and managing authentication mechanisms. Session class provides two methods to get the session object:

 1.  getDefaultInstance() : Return default session.
         
         overloaded as  below
         ```java
           public static Session getDefaultInstance(Properties props)
           public static Session getDefaultInstance(Properties props,Authenticator auth
         ```

 2. getInstance() : Returns new session.
         ```java
          public static Session getInstance(Properties props)
          public static Session getInstance(Properties props,Authenticator auth)
         ```

 Authenticator class represents an object that knows how to obtain authentication for a network connection. One must register the Authenticator with the Session when you create a session object.



2. Creating Message Content

   To create the message, need to pass session object in MimeMessage class constructor:
   
   ```java
    MimeMessage message=new MimeMessage(session);
   ```
   
   The message object will be used to store information. 
    like from address, to address, subject and 
    message body. `InternetAddress` represents An RFC 822 address.


3. Transporting Message over the selected protocol
Transport class is being used for message transport mechanism. This class uses the SMTP protocol to send a message. That's it you have sent an email over SMTP using Java Mail API.

### Errors while Sending Mail Using Java Mail API

**Error 1:** Build Error
   ```java
    javax.mail.internet.MimeMessage cannot be resolved.
   ```
**Solution:** This occurs if javax.mail.internet.MimeMessage is missing in your classpath. Add it or if using maven add dependencies in pom.xml.



**Error 2:** ``` SendFailedException.getValidUnsentAddresses() ``` not returning value as expected.

**Solution:** You need to enable  property `mail.smtp.reportsuccess` and `mail.smtp.sendpartial `.

**Error 3:** Unable to give multiple CC's

**Solution:** Use the below code:

   ```java
    Address[] cc = new Address[] {InternetAddress.parse("abc@abc.com"),
                               InternetAddress.parse("abc@def.com"), 
                               InternetAddress.parse("ghi@abc.com")};
    message.addRecipients(Message.RecipientType.CC, cc);
   ```
**Error 4:** Email sent through SMTP which contains German characters (umlauts) 
    content is not displaying correctly.
    
**Solution:** You must set proper encoding for message subject,body etc.
    as follow `setContent(content, "UTF-8")`. ` mail.mime.charset` by default uses the Java charset, as specified in the file.encoding. file.encoding property is specified when JVM starts up however you can set it up while running your java program as follow:
	
	``` java -Dfile.encoding=UTF-8 … com.x.Main ```
 
##### By the time you reach here you would have explored much of JavaMail API 

> **One Fact:**
Oracle Java JDK and JVM is paid, but JavaMail API still is free and opensource as it is published under  Common Development and Distribution License (CDDL) v1.1 and GNU General Public License (GPL) v2 with Classpath Exception.
JavaMail API is widely used in Android, Spring Applications. 

Let's See Some example of sending email using various properties of JavaMail API.

### Send an email through SMTP without authentication.

Sample Code:

```java
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

        // Mention the Recipient's email address
        String to = "fromaddress@gmail.com";

        // Mention the Sender's email address
        String from = "toaddress@gmail.com";

        // Get system properties
        Properties properties = System.getProperties();

        // Setup mail server
        properties.put("mail.smtp.host", "smtp.host.com");
        properties.put("mail.smtp.port", "465");

        // Get the Session object and pass username and password
        Session session = Session.getInstance(properties);

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
For the above code to work there should be no authentication at your SMTP server.

But in real life SMTP servers uses authentication like TLS or SSL.
Let's jump to an example of setting TLS or SSL.

### Send email using TLS and SSL authentication

To establish an SSL/TLS connection, applications using JavaMail API (client) must be able to verify that the security certificate presented by the server is trusted by the client's  application. Trusted certificates are maintained in a Java Keystore file on the client.

1. Using TLS
You need to set mail.smtp.starttls.enable to true.
By default 587 ports default to TLS.
Here is sample code:

```
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

        // Mention the Recipient's email addess
        String to = "toaddress@gmail.com";

        // Mention the Sender's email address
        String from = "fromaddress@gmail.com";

        // Mention the SMTP server address. Below Gmail's SMTP server is being used to send email
        String host = "smtp.gmail.com";

        // Get system properties
        Properties properties = System.getProperties();

        // Setup mail server
        properties.put("mail.smtp.host", host);
        properties.put("mail.smtp.port", "587");
        properties.put("mail.smtp.starttls.enable", "true");
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


2) Using SSL
In earlier releases, it was necessary to explicitly set a socket factory property to enable the use of SSL. From Java Mail API 1.5 it is no longer needed, as the support for SSL is in-built.

JavaMail API has a special SSL socket factory that can simplify dealing with servers with self-signed certificates.

`class com.sun.mail.util.MailSSLSocketFactory` can be used as a simple socket factory to allow trusting all or some hosts.

`MailSSLSocketFactory` avoids the need for managing keystore, or configure your own TrustManager and add a certificate in keystore.

Sample Code:

```java
package com.sendemail;

import java.util.Properties;

import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import com.sun.mail.util.MailSSLSocketFactory;


public class SendMail {

    public static void main(String[] args) {

        // Mention the Recipient's email address
        String to = "toaddress@gmail.com";

        // Mention the Sender's email address
        String from = "fromaddress@gmail.com";

        // Mention the SMTP server address. Below Gmail's SMTP server is being used to send email
        String host = "smtp.gmail.com";
        
        MailSSLSocketFactory sf = new MailSSLSocketFactory();
    sf.setTrustAllHosts(true);
    // or
    // sf.setTrustedHosts(new String[] { "my-server" });  

        // Get system properties
        Properties properties = System.getProperties();

        // Setup mail server
        properties.put("mail.smtp.host", host);
        properties.put("mail.smtp.port", "587");
        properties.put("mail.smtp.starttls.enable", "true");
        properties.put("mail.smtp.auth", "true");
        props.put("mail.smtp.ssl.socketFactory", sf);
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
### Sending Inline Image in Email

Here is sample code :
```java
package com.sendemail;

import java.util.Properties;
import javax.activation.DataHandler;
import javax.activation.DataSource;
import javax.activation.FileDataSource;
import javax.mail.BodyPart;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeBodyPart;
import javax.mail.internet.MimeMessage;
import javax.mail.internet.MimeMultipart;

public class SendInlineImagesInEmail {
   public static void main(String[] args) {
      // Mention the Recipient's email address
      String to = "recipientemail@gmail.com";

      // Mention the Sender's email address
      String from = "fromemail@gmail.com";
      final String username = "rishabhmishra131";//change accordingly
      final String password = "******";//change accordingly

      // Mention the SMTP server address. Below Pepipost's SMTP server is being used to send email
      String host = "smtp.pepipost.com";

      Properties props = new Properties();
      props.put("mail.smtp.auth", "true");
      props.put("mail.smtp.starttls.enable", "true");
      props.put("mail.smtp.host", host);
      props.put("mail.smtp.port", "587");

      Session session = Session.getInstance(props,
         new javax.mail.Authenticator() {
            protected PasswordAuthentication getPasswordAuthentication() {
               return new PasswordAuthentication(username, password);
            }
         });

      try {

         // Create a default MimeMessage object.
         Message message = new MimeMessage(session);

         // Set From: header field of the header.
         message.setFrom(new InternetAddress(from));

         // Set To: header field of the header.
         message.setRecipients(Message.RecipientType.TO,
            InternetAddress.parse(to));

         // Set Subject: header field
         message.setSubject("Testing Subject");

         // This mail will have two parts, BODY and embedded image
         MimeMultipart multipart = new MimeMultipart("related");

         // first part (the html)
         BodyPart messageBodyPart = new MimeBodyPart();
         String htmlText = "<H1>Hello</H1><img src=\"cid:image-id\">";
         messageBodyPart.setContent(htmlText, "text/html");
         // add it
         multipart.addBodyPart(messageBodyPart);

         // second part (the image)
         messageBodyPart = new MimeBodyPart();
         DataSource fds = new FileDataSource(
            "/home/rishabh.mishra/sampleimage.png");

         messageBodyPart.setDataHandler(new DataHandler(fds));
         messageBodyPart.setHeader("Content-ID", "<image-id>");

         // add image to the multipart
         multipart.addBodyPart(messageBodyPart);

         // put everything together
         message.setContent(multipart);
         // Send message
         Transport.send(message);

         System.out.println("Sent message successfully....");

      } catch (MessagingException e) {
         throw new RuntimeException(e);
      }
   }
}

```

Here in this example image file has been used as an attachment. So, you have to set the Content-ID header for the image file and then use the same Content-ID in the email message body with `<img src='cid:image-id'>`.

## Conclusion
Hope this tutorial helped to learn the details of Java Mail APIs. Various use case scenarios, like sending email using SMTP, exploring the authentication mechanism, sending inline images and using TLS/SSL were covered as a part of this tutorial.
