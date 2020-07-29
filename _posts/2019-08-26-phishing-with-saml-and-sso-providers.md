---
title: Phishing with SAML and SSO Providers
author: jp
excerpt: TEST
type: post
date: 2019-08-26T21:42:40+00:00
url: /2019/08/26/phishing-with-saml-and-sso-providers/
categories:
  - phishing
  - red team

---
<img src="/images/2019/08/saml-sso.png">

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

  1. The user gets an email from noreply@okta.com with a message stating that they need to update their MFA token or something to that effect. This message is almost entirely attacker-controlled using Okta's Email Template Customization.
  2. The link they are asked to follow goes to <attacker controlled subdomain>.okta.com (Our example uses my personal Okta account but this could be changed to fit your operation)
  3. Once they click the link, it redirects immediately to our phishing site.
  4. In our case, we used EvilGinx which creates a transparent proxy of our target's actual Okta login page which allows us to capture the login credentials as well as the session token.

## So how does this work?

To get an email to come from noreply@okta.co, we can use our Okta trial to customize the email templates for new users, this lets us determine what we want our targets to see. Below, is a new Okta user email, modified to fit our phishing needs.

<p align="center">
  <img src="/images/2019/08/add-translauon-default-language-engllsh-ten-em-1.png" />
</p>

Now that we have our custom email template, we have two options. Place our phishing link directly in the email which will be analyzed by most email gateways, or use our other option; SAML.

What we need to do here is set up a custom SAML page to point to our phishing site. Evilsite.com will be replaced with our phishing site.

<img src="/images/2019/08/edit-identity-provider-name-protocol-t-authen-1.png">

Once we enter these details, we are given an Assertion URL, this URL will point to Okta but will redirect immediately to our phishing site we just specified.

<img src="/images/2019/08/add-identlty-provlder-name-test-idp-id-saml-me-1.png">

Now we need to copy the Assertion Consumer Service URL and go back to the email template we initially setup. Paste the Assertion link into the link for the Refresh MFA Token link.

Now to send our phishing email, we need to add our phishing targets as members of our Okta organization.

<img src="/images/2019/08/add-person-first-name-last-name-username-prima-1.png">

This doesn&#8217;t send the email, we need to click on the newly created user and select &#8216;Activate&#8217;

<img src="/images/2019/08/activate-person-are-pu-sure-you-want-to-activate-1.png"> 

Email sent and in the inbox.

<img src="/images/2019/08/primary-okta-social-okta-mfa-device-expiration-1.png"> 

<img src="/images/2019/08/okta-mfa-device-expiration-okta-lessnoreplyokta-com-1.png"> 

Once we click on the link, we are faced with this:

<img src="/images/2019/08/https-jardanpotti-okta-com-sso-sam12-00aq3csxlcc-1.png"> 

Which then redirects to our phishing site.

<img src="/images/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-2.png"> 

Since this is a transparent proxy, this allows our victims to sign in and Evilginx captures the credentials and session token.

<img src="/images/2019/08/https-okta-mytargetorg-com-login-login-htm-okta-4.png">

If 2FA is present, the proxy will handle this seamlessly and the user will then be logged in &#8211; note the URL is still our phishing site.

<img src="/images/2019/08/https-okta-mytargetorg-com-app-userhome-okta-w-1.png">
Our EvilGinx proxy now has the valid session token, which allows us to bypass MFA as well as the credentials to our phished user.

<img src="/images/2019/08/id-phishlet-okta-username-password-tokens-ca-1.png">
## Duo

Update: Duo pushed a fix to make this method more difficult to pull off. The fix went into production on September 26th, 2019.

To do this with Duo, we need to get the Basic Duo plan which costs $30 per month. Once we have that, we are able to customize our Duo new user activation email templates.

Customize the **Activation Emai**l template from your Duo admin portal to include your Duo sign-in link, and append _/saml/login?next=/?&email=<victim email address or username>_

<img src="/images/2019/08/word-image-3.png">

  1. Now, go to **Administrators -> Add Administrators** and add your victim as an Administrator to your account. Don’t worry, they will not know that password and will not be able to access the login page once we configure SAML 😊
  2. Go to **Administrators -> Admin Login Settings** and scroll down to **SAML Identity Provider Settings.**
  3. Choose Custom Identity Provider 
      1. **Entity or Issuer ID** can be anything
      2. **Assertion consumer service URL or single sign-on URL** should be your phishing site. I would recommend EvilGinx for this part.
      3. **Certificate** can be a randomly generated certificate. 
  4. Now go to **Users -> Add User** and add your victim as a user.
  5. Send Enrollment email.

<img src="/images/2019/08/word-image-5.png">
Clicking the link brings you here with the username prefilled:

Update: This is what Duo changed. The username no longer prefills. This would force the user to enter their username here. This wouldn&#8217;t exactly make this attack impossible, but it would raise the level of user interaction required. 

<img src="/images/2019/08/word-image-7.png">
And once you select **Continue to Identity Provider**, you are redirected to the phishing site!

### In conclusion

Since many services are moving into the Enterprise space and adding support for SAML, we will likely see many services that allow us to leverage SAML to redirect to our phishing sites. As usual with phishing, it typically comes down to user awareness.

### What can services that allow SAML and Custom IDP’s do to fix this?

  1. Add a warning before redirecting to ask the end-user if they trust the URL of the custom Identity Provider.
  2. Prevent admins from customizing emails sent from SSO providers.
  3. Make sure users only trust the URL! That is typically the only authoritative place to look before entering your credentials anywhere.

### SAML Open Redirects:

While testing this, I found that Google, GitLab, Duo, and Okta all allow you to use SAML for open redirects. It's likely that nearly all services that allow you to integrate SAML with a custom Identity Provider will be similar.
