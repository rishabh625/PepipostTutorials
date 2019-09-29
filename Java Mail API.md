# JavaMailAPI


### Introduction

JavaMail API provides a platform-independent and protocol-independent framework to build mail and messaging client applications using Java.
Java Mail API is generic and provide abstract classes containing objects that is being defined in email system and act as interface to email applications.

JavaMail API is generic for reading and sending emails.And require service providers to interact with mail servers running on transport protocol like.

1) Simple Mail Transfer Protocol(SMTP)

   SMTP is internet standard for sending and recieving Email across IP networks it uses TCP with most popularly port 25 to send and recieve. Connections can be secured by SSL.

    JavaMail API has packages and core classes
    ```java 
     com.sun.mail.smtp
    ```
    acting as SMTP protocol provider to connect SMTP servers

2)  Internet Message Access Protocol(IMAP)

    IMAP server listens mostly on port 143. IMAP over SSL is assigned to port 993.

    Package 
    ```java
     com.sun.mail.imap
    ```
     is IMAP protocol provider in JavaMail API 

3) Post Office Protocol (POP)

   POP3 is an internet standard protocol used by local email clients to 
   access email from remote server over TCP/IP.POP3 server listens on port 
   110
   ```java 
   com.sun.mail.pop3
   ```
    is POP3 protocol provider in JavaMail API

## Installing JavaMail API

To use JavaMail API you would require to add two jars in your CLASSPATH.
1) [mail.jar](http://www.java2s.com/Code/Jar/m/Downloadmailjar.htm)
2) [activation.jar](http://www.java2s.com/Code/Jar/a/Downloadactivationjar.htm).


If You are using Maven Create Maven Project [Reading this](https://pepipost.com/tutorials/send-email-in-java-using-gmail-smtp/)

```
Note : JavaBeans Activation Framework (JAF) extension that provides the (activation.jar)javax.activation package is already present in Java SE 6 and later versions.
```

## Send Emails

To Send Email you will need an SMTP server

- You can install smtp server on your machine.

  Following this tutorials.
  - [How To Install Postfix CentOS 7](https://pepipost.com/tutorials/install-centos-postfix/)
  - [Installing Postfix as a Send-Only SMTP Server on Debian 10 (2019)](https://pepipost.com/tutorials/install-postfix-as-send-only-smtp-server/)

- Or use smtp service providers like Pepipost.

### Sample code to send email using SMTP.

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
Lets break down above code 3 parts for better understanding.

1) Getting The Mail Session.

   The session is the context of how you are going to interact with the 
   mail host.Helpful in debugging output from the mail host, timeouts, and 
   managing authentication mechanisms.

   Session class provides two methods to get session object.
    
      1) getDefaultInstance() : Return default session.
         
         overloaded as  below
         ```java
           public static Session getDefaultInstance(Properties props)
           public static Session getDefaultInstance(Properties props,Authenticator auth
         ```
      2) getInstance() : Returns new session.
         ```java
          public static Session getInstance(Properties props)
          public static Session getInstance(Properties props,Authenticator auth)
         ```

     Authenticator class represents an object that knows how to obtain 
  authentication for a network connection.
  One must register the Authenticator with the Session when you create session object.



2) Creating Message Content.

   To create the message, need to pass session object in MimeMessage class 
   constructor
   ```java
    MimeMessage message=new MimeMessage(session);
   ```
     
    message object will be used to store information. 
    like from address,to address,subject and 
    message body

   InternetAddress represents An RFC 822 address.

3) Transporting Message over selected protocol. 
   
   Transport class is used for message transport mechanism. This class 
    uses the SMTP protocol to send a message.

That's it you have sent email over SMTP using java Mail API.

### Errors while Sending Mail using Java Mail API

1) Build Error
   ```java
    javax.mail.internet.MimeMessage cannot be resolved.
   ```
    Solution: This occurs if javax.mail.internet.MimeMessage is missing in 
          your classpath.Add it or if using maven add dependecies in 
          pom.xml.

2) ``` SendFailedException.getValidUnsentAddresses() ``` not returning value as expected.

   Solution : You need to enable  property ``` mail.smtp.reportsuccess ``` and 
  ``` mail.smtp.sendpartial ```.

3) Unable to give multiple CC's

   Solution : use below code
   ```java
    Address[] cc = new Address[] {InternetAddress.parse("abc@abc.com"),
                               InternetAddress.parse("abc@def.com"), 
                               InternetAddress.parse("ghi@abc.com")};
    message.addRecipients(Message.RecipientType.CC, cc);
   ```
4)  Email sent through SMTP which contains German characters (umlauts) 
    content is not displaying correctly.
    
    you must set proper encoding for message subject,body etc.
    as follow ``` setContent(content, "UTF-8") ```.

     ``` mail.mime.charset ``` by default uses the Java charset, as 
      specified in the file.encoding.

     file.encoding property is specified when JVM starts up
     however you can set it up while running your java program as follow
     ``` java -Dfile.encoding=UTF-8 … com.x.Main ```
 
##### By the time you reach here you would have explored much of JavaMail API 

``` 
Fact : Even through Oracle Java JDK and JVM is paid. JavaMail API still will be free as it is published under  Common Development and Distribution License (CDDL) v1.1 and GNU General Public License (GPL) v2 with Classpath Exception

JavaMail API is most widely used in Android,Spring Applications.
```  

Lets See Some example of sending email using various properties of JavaMail API

### Send email through smtp without authentication.

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

        // Recipient's email ID needs to be mentioned.
        String to = "fromaddress@gmail.com";

        // Sender's email ID needs to be mentioned
        String from = "toaddress@gmail.com";

        // Get system properties
        Properties properties = System.getProperties();

        // Setup mail server
        properties.put("mail.smtp.host", "smtp.host.com");
        properties.put("mail.smtp.port", "465");

        // Get the Session object.// and pass username and password
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
For above code to work there should be no authentication at your SMTP Server

But in real life SMTP servers uses authentication like TLS or SSL.
Lets jump to example of setting TLS or SSL.

### Send email using TLS and SSL authentication

To establish an SSL/TLS connection, application using JavaMail API(client) must be able
to verify that the security certificate presented by the server
is trusted by client application.Trusted certificates
are maintained in a Java keystore file on the client.

1) Using TLS
You just need to set mail.smtp.starttls.enable to true.
By default 587 ports defaults to TLS.
Here is sample code - 
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

   In earlier releases it was necessary to explicitly set a socket
factory property to enable use of SSL. From Java Mail API 1.5 it is no longer needed and SSL support is built in.

   JavaMail API has special SSL socket factory that can simplify
   dealing with servers with self-signed certificates.

   class com.sun.mail.util.MailSSLSocketFactory can be used as a
   simple socket factory to allow trusting all or some hosts.

   MailSSLSocketFactory avoids the need of managing 
   keystore, or configure your own TrustManager and and add certificate in 
   keystore.

Sample Code :
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

        // Recipient's email ID needs to be mentioned.
        String to = "toaddress@gmail.com";

        // Sender's email ID needs to be mentioned
        String from = "fromaddress@gmail.com";

        // Assuming you are sending email from through gmails smtp
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
      // Recipient's email ID needs to be mentioned.
      String to = "recipientemail@gmail.com";

      // Sender's email ID needs to be mentioned
      String from = "fromemail@gmail.com";
      final String username = "rishabhmishra131";//change accordingly
      final String password = "******";//change accordingly

      // Assuming you are sending email through Pepipost
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
Here we have attached the image file as attachment and then set the Content-ID header for image file and then use the same content id in the email message body with <img src='cid:image-id'>.

Conclusion :
That's it we have learned much of Java Mail API.Used Protocols.Sent Email Using SMTP and explored authentication mechanism, sending inline images, using TLS and SSL.
