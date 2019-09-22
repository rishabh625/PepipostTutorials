
# How To Send Email in CodeIgniter (CI)


### Introduction

PHP is widely used in development of web applications and email is most important to it whether its signup, forgetten password , invoices or any other communications all are sent over the email.

Nowadays almost every PHP framework provides well managed and well developed email functionality.

CodeIgniter is no new to it and has a built-in email library to send Emails.
Sending email in CodeIgniter is much easier.Just you haved to configure SMTP properties in configuration file, load it, and start sending emails using SMTP.

## Prerequisites

- PHP (This tutorial is based 5.3.5).

  If you dont have PHP get it installed from [here](https://www.php.net/)

- Any Web Server like Nginx or Apache (For this tutorial have used Wamp Server)

  Install Wamp Server from [here](http://www.wampserver.com/en/)

  Install Nginx Server from [here](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/)

  Install Xampp from [here](https://www.apachefriends.org/download.html)

Make sure you have PHP-FPM installed,up and running.
- CodeIgniter 3.1.1

If you are new to CI get it installed on any of the Web Server from below

[on wamp server](https://www.apachefriends.org/download.html)

[on nginx](https://www.nginx.com/resources/wiki/start/topics/recipes/codeigniter/)

[Official documentation](https://codeigniter.com/user_guide/installation/index.html) for installing CI

OR 

you can simply clone it from [github](https://github.com/bcit-ci/CodeIgniter.git) and configure it on any of web server.

Once you install CI on running it will show welcome page like this

![CI welcome Page](https://i.imgur.com/wSRB9kE.png)


Now let's send Email.Here are the few steps to be followed.

## Step 1 — Configuring SMTP properties in CI

We must have one single place to manage our SMTP configurations. And Code Igniter doesn't have predefined config file so let's create one

#### Create a file email.php in the directory application/config

#### Add below code to email.php

``` php

<?php
defined('BASEPATH') OR exit('No direct script access allowed');
 
 
$config['protocol'] = 'smtp';
$config['smtp_host'] = 'smtp.pepipost.com';
$config['smtp_port'] = '587'; // 8025, 587 and 25 can also be used. Use Port 465 for SSL.
$config['smtp_user'] = 'smtpusername';
$config['smtp_pass'] = 'SMTP!Password';
$config['smtp_crypto'] = 'ssl';
$config['charset'] = 'utf-8';
$config['mailtype'] = 'html';
$config['newline'] = "rn";
?>

```

In this file we have defined 
- SMTP protocol to be used.
- Then SMTP User Name and Password

Note : IF you are using Gmail SMTP. Enable less secure app to login from gmail's setting.
- SMTP Port used for smtp mail
- Encryption method to be used ie. tls,ssl etc.
- Mail Type can be html or plain text.
- Charset can be  iso-8859-1 or utf-8
- New Line character to be \r\n.

## Step 2 — Create CodeIgniter Email Controller

- Create Controller file SendEmail.php in application/controllers
- Create function send Load Library email in it and configure it for.
   -  From Name
   -  From Email Address

      ```php
        $this->email->from($from, $fromName); 
      ```
   -  Recipient email address
      ```php
         $this->email->to($to);
      ```
   - Email Subject
     ```php
		$this->email->subject($subject);
     ```
   - Email Content
     ```php
		$this->email->message($message);
     ```
- Send Email using 
    ```php
       $this->email->send()
    ```
It will return true/false based email is sent or not.

- If sending email fails use below function call to debug failure Reason
```php 
   show_error($this->email->print_debugger());
```
#### Sample Code(SendEmail Controller) to send Email

```php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

class Sendemail extends CI_Controller {
	 
	function send()
	{
		$this->load->library('email');
		$fromName="TestName";
		$to='toaddress@domain.com';
		$subject='Test Mail Subject';
		$message='Test Content';
		$from ="fromaddress@domain.in";
		$this->email->from($from, $fromName);
		$this->email->to($to);
	 
		$this->email->subject($subject);
		$this->email->message($message);
		
		if($this->email->send())
		{
			echo "Mail Sent Successfully";
		}
		else
		{
			echo "Failed to send email";
			show_error($this->email->print_debugger());			 
                }
	}
	public function index()
	{
		$this->sendMail();
	}
}

```



## Step 3 — Send Test Email

Open http://localhost/sendemail/send in your browser 

You will see output as

```
 Mail Sent Successfully.
```

If you get any error scroll down to below section of possible errors that you might get.

## Optional Steps

## Step 4 — Send Email using stored Template 

1) Create Email Template.

   - Create a new directory email in application/views

   - Create a new file email_template.php in application/views/email

   - Add below code to application/views/email/email_template.php

   This file will used to dynamically generate content based on message and email id passed.

```html
<!DOCTYPE html>
<html>
   <head>
      <meta charset="utf-8" />
      <title>Codeigniter email template</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
   </head>
   <body>
      <div>
         Hi ,
	 Recieved your message 
		 {message}
	 We will contact you soon on {email} 
      </div>
    </body>
</html>
```

2) Create View File
   
   - Create a new file contactus.php in application/views/email

   - Add below code to application/views/email/contactus.php

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Contact Us</title>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>
        <div>
            <h3>Contact Form</h3>
            <form method="post" action="<?php echo base_url()/sendemail/send?>" enctype="multipart/form-data">
                <input type="email" id="email" name="email" placeholder="Enter your Email Address">
                <br><br>
                <textarea rows="6" id="message" name="message" placeholder="Type your message here"></textarea>
                <br><br>
                <input type="submit" value="Submit" />
            </form>
        </div>
    </body>
</html>
```

- Replace above created controller file with below code 
  To get message and to address from contact us form.

```php
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

class Sendemail extends CI_Controller {
	 
	function send()
	{
		$this->load->library('email');

                $to= $this->input->post('email');
                $contactmessage= $this->input->post('message');
                $data = array(
                 'to'=>$to,
                 'message'=>$contactmessage
                );
                $message = $this->load->view('email/email_template.php',$data,TRUE);
		$fromName="Your Company";
		$subject='Enquiry';
		$from ="fromaddress@domain.in";
		$this->email->from($from, $fromName);
		$this->email->to($to);
	 
		$this->email->subject($subject);
		$this->email->message($message);
		
		if($this->email->send())
		{
			echo "Mail Sent Successfully";
		}
		else
		{
			echo "Failed to send email";
			show_error($this->email->print_debugger());			 
                }
	}
	public function index()
	{
		$this->load->view('email/contactus');

	}
}
```
That's it open http://localhost/sendemail in your browser Enter Email address, message and submit.

You have successfully sent Email!

## Step 5 — Send attachment in Email.

Sending attachment in CI is very much easier.
Just add below code in above created controller and your email will be sent with attachment

```php
$this->email->attach('H:\path\file.PNG');
```

## Possible Errors

### Error 1
Unable to connect to smtp host

```php
A PHP Error was encountered

Severity: Warning

Message: fsockopen() [function.fsockopen]: unable to connect to smtp.pepipost.com:25 (A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond. )
Filename: libraries/Email.php
Line Number: 2069

```
This occurs 

1) When your SMTP Server is down.
2) Port you are using to send Email is blocked.

#### Solution : 
1) Contact your SMTP Service provider.
2) Telent SMTP host on port 25,587,465 or 2525 whichever responds use that to send emails.

### Error 2
This error occurs when you try to use SSL but
SSL is not configured

```php
A PHP Error was encountered

Severity: Warning

Message: stream_socket_enable_crypto() [streams.crypto]: this stream does not support SSL/crypto
Filename: libraries/Email.php
Line Number: 2097

```

#### Solution : 
1) open php.ini file  and remove ; from below line and restart web server 

   ;extension=php_openssl

2) Try changing port to 465 or 587.

3) If you dont want to use SSL rmove smtp_crypto property from config file.

## Conclusion

You have successfully sent email using CI. You can use this in your web application.Define your own templates and keep Sending Emails.

If you Face any issue Please mention it in comments ! We are there to help you!

