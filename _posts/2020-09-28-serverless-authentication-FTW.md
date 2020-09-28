---
title: Serverless Authentication FTW
author: jp
type: post
date: 2020-09-28T20:37:56+00:00
url: /2020/09/28/serverless-authentication-FTW/
categories:
  - aws
---

# Serverless Authentication FTW
Many applications you find on GitHub that can be used for one off tasks, or for simple automation don’t have built in authentication. Typically, I just run it on localhost and port forward, or just run the application locally. This can be a pain and doesn’t scale very well.

With AWS Application Load Balancing, and AWS Cognito, we can control access to applications using Cognito’s built in user directory services, and AWS’s Application Load Balancing conditional forwarding. 

This works like this:


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601310296880_image.png)


Before we begin, I will assume you have the follow prerequisites. 

    1. A VPC configured
    2. 2 Subnets in different Zones
    3. An Application you want to protect
    4. A domain name

To begin lets navigate to AWS Cognito, and add our User Pools.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600982784985_image.png)


From there, select **Create A User Pool.**

I will name it RT-Metrics, since that is the name of the application I am going to be using.

Then select, Review Defaults. You can customize your settings here, the only setting I changed was under to prevent users from being able to sign up themselves.

Before moving on, choose **Add an App Client**. Once again, I will name this the name of my application. The check **Enable Username Based Authentication**.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600983057804_image.png)



Now choose **Create app client → Return to pool details → Create Pool**.

Congrats! You have just completed your first steps to doing this thing!

Now, go to the left hand side and select **App client settings**.

![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600983148426_image.png)


For the next part you need to perform a couple changes:

1. Select **Cognito User Pool for Enabled Identity Providers**
2. Set the **Callback URL** to https://<yourdomain>/oauth2/idpresponse 
3. Select **Authorization code grant** for the **Allowed OAuth Flows**
4. Select **email** and **openid** for the **Allowed OAuth Scopes**
![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600983413290_image.png)


Now select **Choose Domain Name**. You can use the AWS provided one, or choose your own. In this case, I am going to go with AWS provided domain for ease of setup. 

![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600983613313_image.png)


One last thing needs to be done to wrap up the Cognito setup, users. To create a user, head to the left hand side and select **Users and Groups → Create User**. And complete the user details. 


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600983810022_image.png)


Now we need to configure the AWS Load Balancer.

First, go to the EC2 page, and on the bottom of the left hand side, you should be able to choose **Load Balancers**. 


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600983980810_image.png)


After you select **Create Load Balancer,** you are given a couple options, choose the first one; **Application Load Balancer.**

Give your Load Balancer a name, and add new Listener on port 443. You also need to choose at least two of the subnets. Remember, these subnets need to be in different availability zones.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600984142966_image.png)


Select **Next: Configure Security Settings**.

If you already have a cert, you will need to upload it here, or choose it from Amazon’s Certificate Manager. Since I don’t yet, lets go through the motions.

Select Request a new certificate from ACM.

![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600984284009_image.png)


From the Certificate Manager page, add the names for the domain/s you own. I chose to add a wildcard domain so that I don’t have to do this again for other apps I configure. 


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600984427073_image.png)


Choose your validation, I chose email but if you don’t have access to one of those email addresses, you can go with DNS, that option will require you to add a cname record to validate ownership.

Once you get the email, you can follow the link to approve the certificate issuance.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600990067529_image.png)


Once that is done, back in ACM, you should have a valid certificate.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600990168039_image.png)


Back on the Load Balancer page, select your new certificate. You may need to click the green refresh button next to the cert dropdown. 

![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600990246700_image.png)


Click **Next: Configure Security Groups**. If you already have one you’d like to use, go ahead and select it. I am gong to create a new one and add HTTP and HTTPS traffic from anywhere.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1600990329603_image.png)


Select **Next: Configure Routing.** In my case, I will be serving up an EC2 Instance, so that is what we want to choose here. And if you have SSL on the instance, set that here as well. 

![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601046868729_image.png)


On the next page, choose your instance, and then select **Add to registered**.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601046993498_image.png)


Next, review your options and if it all looks good, hit **Create**.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601047040111_image.png)


The last thing we need to do, is to configure our load balancer to use Cognito for authentication. On the load balancer tab, select your newly created load balancer, select **listeners**, and then select the listener you want to configure. 

If you added HTTP and HTTPS listeners, you will need to configure both. In my case, I configured the HTTP listener to forward to port 443. 

Select **HTTP: 80** and choose View/edit rules.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601047243125_image.png)


From this page, click the edit option near the top, then click the edit option on the rule itself. Delete the existing THEN rule and add a new one for redirecting to SSL.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601047373887_image.png)


Press update and go back to the load balancer page.

Following the same steps as before, edit the **HTTPS listener**.

When choosing a new **THEN** action, select **Authenticate**. You newly created Cognito configuration should be available to choose from this page.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601047490084_image.png)


Add one more action to forward to our target.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601047533519_image.png)



And select **Update**. 

We need to now update our DNS settings. Since we opted to use the AWS provided DNS name for authentication, we only need to add one CNAME record to the domain name we want our application to be reachable at.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601047858570_image.png)



![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601048044990_image.png)


Now hit your domain and see if you get redirected to auth.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601048092806_image.png)

![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601091341531_image.png)


Now that we know our authentication is working, we need to make sure that our application can’t be reached via the direct IP address.

We need to set a Security Group on that instance and only allow traffic from the load balancer.


![](https://paper-attachments.dropbox.com/s_6A8AD8E9B8CF9D48F3B9EDEC041237C695C2CBEE8AEE9F15FB740839B77F8950_1601048519686_image.png)


And you’re done! You should have an application behind authentication using only Cognito and an AWS Load Balancer!

