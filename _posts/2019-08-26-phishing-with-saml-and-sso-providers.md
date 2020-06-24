---
title: Phishing with SAML and SSO Providers
author: jp
type: post
date: 2019-08-26T21:42:40+00:00
url: /2019/08/26/phishing-with-saml-and-sso-providers/
categories:
  - phishing
  - red team

---
<img src="https://i1.wp.com/jordanpotti.com/wp-content/uploads/2019/08/saml-sso.png>

**Phishing is getting harder.** Email firewalls, user training and a host of endpoint controls are making phishing something that many Red Teams no longer perform. Instead, many Red Teams are using something called Assume Breach or White Carding, this allows the Red Team to skip certain portions of the initial access steps so that they can focus on other areas of the assessment. Spending two weeks on phishing could be valuable time lost inside the environment.

This is not always the case though, some organizations want to see the most realistic attack path and want their Red Teams to start with phishing. This can be frustrating since it can take weeks to set up the infrastructure and come up with realistic phishes. Even once we have all the pieces in place, it only takes one report of our phish by a savvy user to the SOC and entire parts of our phishing infrastructure can be burnt. Needless to say, phishing is expensive.

I was recently faced with needing to perform an engagement beginning with a phish, and I was up against a technology stack I wasn’t very familiar with. The customer had a solid email firewall and all links in inbound emails would be analyzed every time our target clicked the link. This removed the option of buying a previously trusted domain and relying on domain categorization.

In order for our phish to be successful, we needed:

  1. A trusted domain to send the email from.
  2. A link to a trusted site (Which would guide the victim to our phishing site) in the email.
  3. The initial landing page couldn’t look like a phish due to the customers&#8217; email and proxy controls.

This dilemma leads to the following research..

Combining SSO Providers with the ability to specify custom SAML Identity Providers, we can send phishing email’s from an SSO Provider to our victim, containing only valid links to SSO Provider which then redirects to our phishing site with minimal to no user interaction.

**This technique relies on two important things:**

  1. In most cases, custom Identity Providers with SAML introduce an open redirect.
  2. Many SSO providers such as Duo and Okta, allow administrators to customize emails sent from the SSO platform, typically coming from a no-reply@<duo/okta>.com/.

## The phishing flow looks like this:

  1. The user gets an email from noreply@okta.com with a message stating that they need to update their MFA token or something to that effect. This message is almost entirely attacker-controlled using Okta&#8217;s Email Template Customization.
  2. The link they are asked to follow goes to <attacker controlled subdomain>.okta.com (Our example uses my personal Okta account but this could be changed to fit your operation)
  3. Once they click the link, it redirects immediately to our phishing site.
  4. In our case, we used EvilGinx which creates a transparent proxy of our target&#8217;s actual Okta login page which allows us to capture the login credentials as well as the session token.

## So how does this work?

To get an email to come from noreply@okta.co, we can use our Okta trial to customize the email templates for new users, this lets us determine what we want our targets to see. Below, is a new Okta user email, modified to fit our phishing needs.

<p align="center">
  <img src="/images/2019/08/add-translauon-default-language-engllsh-ten-em-1.png" />
</p>

Now that we have our custom email template, we have two options. Place our phishing link directly in the email which will be analyzed by most email gateways, or use our other option; SAML.

What we need to do here is set up a custom SAML page to point to our phishing site. Evilsite.com will be replaced with our phishing site.

<img src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/edit-identity-provider-name-protocol-t-authen-1.png>

Once we enter these details, we are given an Assertion URL, this URL will point to Okta but will redirect immediately to our phishing site we just specified.

<img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/add-identlty-provlder-name-test-idp-id-saml-me-1.png>

Now we need to copy the Assertion Consumer Service URL and go back to the email template we initially setup. Paste the Assertion link into the link for the Refresh MFA Token link.

Now to send our phishing email, we need to add our phishing targets as members of our Okta organization.

<img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/add-person-first-name-last-name-username-prima-1.png>

This doesn&#8217;t send the email, we need to click on the newly created user and select &#8216;Activate&#8217;

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/activate-person-are-pu-sure-you-want-to-activate-1.png?resize=507%2C190" alt="Activate Person 
Are pu sure you want to activate Jordan Potti? 
An actlvatlon emall wlll be sent to thls person Informlng them that thelr account Is now actlve_ You can 
edlt the content of these emalls on the customlze emall page 
Cancel " class="wp-image-566" width="507" height="190" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/activate-person-are-pu-sure-you-want-to-activate-1.png?w=687 687w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/activate-person-are-pu-sure-you-want-to-activate-1.png?resize=300%2C112 300w" sizes="(max-width: 507px) 100vw, 507px" data-recalc-dims="1" /><figcaption>Activate Link for victim</figcaption></figure>
</div>

Email sent and in the inbox.

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter"><img src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png?w=688" alt="Primary 
Okta 
Social 
Okta MFA Device Expiration - Target Organization - MFA Device Expiration Hi Jordan, Your MFA Device is expiring in 24 hours, follow this link to refresh your device to avoid being locked ou... 
3:10 PM " class="wp-image-569" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png?w=1585 1585w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png?resize=300%2C20 300w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png?resize=768%2C52 768w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png?resize=1024%2C70 1024w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png?w=1376 1376w" sizes="(max-width: 688px) 100vw, 688px" data-recalc-dims="1" /></figure>
</div>

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/okta-mfa-device-expiration-okta-lessnoreplyokta-com-1.png?resize=427%2C421" alt="Okta MFA Device Expiration 
Okta <noreply@okta.com> 
to me 
okta 
Hi Jordan, 
Inbox x 
Target Organization - MFA Device Expiration 
Your MFA Device is expiring in 24 hours, follow this link to refresh your device to 
avoid being locked out of your account. 
Refresh MFA Token 
This link expires in 7 days. 
If you experience difficulties accessing your account, you can send a help request to 
your system administrator using the link: targetorg.okta.com/help/login 
This is an automatically generated message from Okta_ Replies are not monitored or answered. 
Reply 
Forward " class="wp-image-571" width="427" height="421" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/okta-mfa-device-expiration-okta-lessnoreplyokta-com-1.png?w=730 730w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/okta-mfa-device-expiration-okta-lessnoreplyokta-com-1.png?resize=300%2C296 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/okta-mfa-device-expiration-okta-lessnoreplyokta-com-1.png?resize=50%2C50 50w" sizes="(max-width: 427px) 100vw, 427px" data-recalc-dims="1" /><figcaption>Email sent to victim from Okta</figcaption></figure>
</div>

Once we click on the link, we are faced with this:

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-jardanpotti-okta-com-sso-sam12-00aq3csxlcc-1.png?resize=433%2C226" alt="https://jardanpotti.okta.com/sso/sam12/00aq3csxlcc6NSYg0356 
o 
Signing In " class="wp-image-573" width="433" height="226" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-jardanpotti-okta-com-sso-sam12-00aq3csxlcc-1.png?w=1174 1174w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-jardanpotti-okta-com-sso-sam12-00aq3csxlcc-1.png?resize=300%2C157 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-jardanpotti-okta-com-sso-sam12-00aq3csxlcc-1.png?resize=768%2C402 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-jardanpotti-okta-com-sso-sam12-00aq3csxlcc-1.png?resize=1024%2C536 1024w" sizes="(max-width: 433px) 100vw, 433px" data-recalc-dims="1" /><figcaption>Loading page for redirect to phishing site</figcaption></figure>
</div>

Which then redirects to our phishing site.

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-2.png?resize=499%2C292" alt="https://okta.mytargetorg.com/login/login.htm 
okta 
Sign In 
usemame 
Password 
Remember me 
Sign In 
Need help signing in? " class="wp-image-576" width="499" height="292" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-2.png?w=1171 1171w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-2.png?resize=300%2C176 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-2.png?resize=768%2C450 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-2.png?resize=1024%2C600 1024w" sizes="(max-width: 499px) 100vw, 499px" data-recalc-dims="1" /><figcaption>Phishing Page (Evilginx)</figcaption></figure>
</div>

Since this is a transparent proxy, this allows our victims to sign in and Evilginx captures the credentials and session token.

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-4.png?resize=446%2C291" alt="https://okta.mytargetorg.com/login/login.htm 
okta 
Sign In 
admin 
Remember me 
Sign In 
Need help signing in? " class="wp-image-578" width="446" height="291" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-4.png?w=1004 1004w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-4.png?resize=300%2C197 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-4.png?resize=768%2C503 768w" sizes="(max-width: 446px) 100vw, 446px" data-recalc-dims="1" /><figcaption>Evilginx Phishing Pae</figcaption></figure>
</div>

If 2FA is present, the proxy will handle this seamlessly and the user will then be logged in &#8211; note the URL is still our phishing site.

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-app-userhome-okta-w-1.png?resize=514%2C160" alt="https://okta.mytargetorg.com/app/UserHome 
okta 
Work 
Sites 
G Suite Sites 
Google Accumts 
G Suite Google Account 
Q Launch App 
M Gmail 
G Suite Mail 
Home 
Drive 
G Suite Drive 
jordan • 
Calendar 
G Suite Calendar 
+ Add Apps 
Admin " class="wp-image-580" width="514" height="160" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-app-userhome-okta-w-1.png?w=1405 1405w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-app-userhome-okta-w-1.png?resize=300%2C94 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-app-userhome-okta-w-1.png?resize=768%2C240 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/https-okta-mytargetorg-com-app-userhome-okta-w-1.png?resize=1024%2C320 1024w" sizes="(max-width: 514px) 100vw, 514px" data-recalc-dims="1" /><figcaption>Succesful login to Okta through our Evilginx proxy</figcaption></figure>
</div>

Our EvilGinx proxy now has the valid session token, which allows us to bypass MFA as well as the credentials to our phished user.

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter"><img src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/id-phishlet-okta-username-password-tokens-ca-1.png?w=688" alt="id 
phishlet 
okta 
username 
password 
tokens 
captured 
Win64; 
remote 
time 
2819-87-89 21:19 
sessions 3 
id 
phishlet 
username . 
password 
tokens . 
landing url 
user -agent 
remote ip 
create time 
update time . 
okta 
captured 
. https : //mytargetorg.com/o...... 
. mozi11a/S.8 (Windows NT 18.8; 
x64) AppleuebKit/S37 .36 (KHTML, 
. 2819-87-89 21:04 
2819-87-89 21:19 
[ {&quot;path&quot; • &quot; , &quot;domain&quot; : &quot;jordanpotti . okta . com&quot;,&quot;expirationDate&quot; 
: 1594243257 , 
&quot;value&quot; : 
like Gecko) Chrome/7s.ß.377ß.1ßß safari/S37.36 
&quot; &quot;sid&quot; &quot;httpOn1y&quot; : true, &quot;hostOn1y&quot; : true}] " class="wp-image-582" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/id-phishlet-okta-username-password-tokens-ca-1.png?w=1252 1252w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/id-phishlet-okta-username-password-tokens-ca-1.png?resize=300%2C77 300w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/id-phishlet-okta-username-password-tokens-ca-1.png?resize=768%2C198 768w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/id-phishlet-okta-username-password-tokens-ca-1.png?resize=1024%2C264 1024w" sizes="(max-width: 688px) 100vw, 688px" data-recalc-dims="1" /><figcaption>Evilginx dashboard</figcaption></figure>
</div>

## Duo

Update: Duo pushed a fix to make this method more difficult to pull off. The fix went into production on September 26th, 2019.

To do this with Duo, we need to get the Basic Duo plan which costs $30 per month. Once we have that, we are able to customize our Duo new user activation email templates.

Customize the **Activation Emai**l template from your Duo admin portal to include your Duo sign-in link, and append _/saml/login?next=/?&email=<victim email address or username>_

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-3.png?resize=469%2C311" alt="" class="wp-image-584" width="469" height="311" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-3.png?w=1095 1095w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-3.png?resize=300%2C199 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-3.png?resize=768%2C511 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-3.png?resize=1024%2C681 1024w" sizes="(max-width: 469px) 100vw, 469px" data-recalc-dims="1" /><figcaption>New user email template</figcaption></figure>
</div>

  1. Now, go to **Administrators -> Add Administrators** and add your victim as an Administrator to your account. Don’t worry, they will not know that password and will not be able to access the login page once we configure SAML 😊
  2. Go to **Administrators -> Admin Login Settings** and scroll down to **SAML Identity Provider Settings.**
  3. Choose Custom Identity Provider 
      1. **Entity or Issuer ID** can be anything
      2. **Assertion consumer service URL or single sign-on URL** should be your phishing site. I would recommend EvilGinx for this part.
      3. **Certificate** can be a randomly generated certificate. 
  4. Now go to **Users -> Add User** and add your victim as a user.
  5. Send Enrollment email.

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-5.png?resize=469%2C232" alt="" class="wp-image-586" width="469" height="232" srcset="https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-5.png?w=1148 1148w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-5.png?resize=300%2C149 300w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-5.png?resize=768%2C381 768w, https://i0.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-5.png?resize=1024%2C508 1024w" sizes="(max-width: 469px) 100vw, 469px" data-recalc-dims="1" /><figcaption>Email from Duo in Victim inbox</figcaption></figure>
</div>

Clicking the link brings you here with the username prefilled:

Update: This is what Duo changed. The username no longer prefills. This would force the user to enter their username here. This wouldn&#8217;t exactly make this attack impossible, but it would raise the level of user interaction required. 

<div class="wp-block-image is-style-zoooom">
  <figure class="aligncenter is-resized"><img src="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-7.png?resize=434%2C410" alt="" class="wp-image-588" width="434" height="410" srcset="https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-7.png?w=606 606w, https://i2.wp.com/jordanpotti.com/wp-content/uploads/2019/08/word-image-7.png?resize=300%2C284 300w" sizes="(max-width: 434px) 100vw, 434px" data-recalc-dims="1" /><figcaption>Prefilled login at Admin Duo page</figcaption></figure>
</div>

And once you select **Continue to Identity Provider**, you are redirected to the phishing site!

### In conclusion

Since many services are moving into the Enterprise space and adding support for SAML, we will likely see many services that allow us to leverage SAML to redirect to our phishing sites. As usual with phishing, it typically comes down to user awareness.

### What can services that allow SAML and Custom IDP’s do to fix this?

  1. Add a warning before redirecting to ask the end-user if they trust the URL of the custom Identity Provider.
  2. Prevent admins from customizing emails sent from SSO providers.
  3. Make sure users only trust the URL! That is typically the only authoritative place to look before entering your credentials anywhere.

### SAML Open Redirects:

While testing this, I found that Google, GitLab, Duo, and Okta all allow you to use SAML for open redirects. It&#8217;s likely that nearly all services that allow you to integrate SAML with a custom Identity Provider will be similar.
