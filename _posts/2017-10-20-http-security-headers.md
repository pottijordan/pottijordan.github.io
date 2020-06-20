---
title: HTTP Security Headers
author: jp
type: post
date: 2017-10-20T01:31:29+00:00
url: /2017/10/20/http-security-headers/
code:
  - "Content-Security-Policy: default-src 'self'"
categories:
  - Uncategorized
tags:
  - application security

---
HTTP security headers seem to be findings on nearly every assessment I have been doing lately. I decided to come up with some handy quick references for these headers in order to better understand them. HTTP Response headers are a way for a server and client to exchange information. In this case, these headers are enforced to ensure that the client is protected from common client side vulnerabilities.

#### **Content-Security-Policy**

Content-Security-Policy &#8211; CSP is a HTTP security header that allows the web application to specify the source for any file that must be loaded from a separate domain. Its main use is to prevent cross-site scripting (XSS) and other injection techniques that load malicious data from an external source. An example of a CSP that restricts the source for all scripts, images and fonts would look like this:

> <pre>Content-Security-Policy: default-src 'self'</pre>

If you were to add sources for other script, you could simply append the trusted domain. It even accepts wildcards for specifying subdomains.

> <pre>Content-Security-Policy: default-src 'self' *.jordanpotti.com</pre>

You can get more granular and specify image-src, media-src, child-src and a host of other restrictions. You can also use report-uri in order to POST reports of policy failures to a URI of your choice. Here is an example of that:

> <pre>Content-Security-Policy: default-src 'self' *.google.com ; report-uri <a href="https://errorcollector.jordanpotti.com/collection.php">https://errorcollector.jordanpotti.com/collection.php</a></pre>

Reporting these violations could give your developers diagnostic information as well as possibly alert you to a vulnerability or attempt to exploit a vulnerability.

Perhaps the most beneficial feature is CSP&#8217;s ability to prohibit inline JavaScript from executing. This prevents malicious code from being inserted into the page, the downside is that you now have to migrate all of you JavaScript to External Resources. Using External Resources is best practice so doing this increases your security as well as code cleanliness.

For more information on CSP, visit: <https://content-security-policy.com/>

#### **Subresource Integrity:**

Subresource Integrity &#8211; SRI is similar to CSP as it applies a layer of security to the scripts and other external data sources your application may use. SRI works by verifying the hash of an external data source before attempting to use it. This works by taking the hash of the external data and placing it in an &#8216;integrity&#8217; attribute inside a <script> or <link> element. If the external data source inside that element do not match the provided integrity hash value, the resource is not loaded.

> <pre>&lt;script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"&gt;&lt;/script&gt;</pre>

You can also use Content-Security-Policy to require that all scripts run have Subresource Integrity set in order to run. Applying this to our last CSP policy, it would look like this:

> <pre>Content-Security-Policy: default-src 'self' *.google.com ; report-uri <a href="https://errorcollector.jordanpotti.com/collection.php">https://errorcollector.jordanpotti.com/collection.php</a> ; require-sri-for script;</pre>

For more information on SRI, visit: <https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity>

#### **CroSs Origin Resource Sharing**

CORS is a way for applications to access resources not stored locally. CORS aims at scaling back the restrictions of the same-origin policy in order to allow the legitimate sharing of web resources.  In the above sample for SRI, we used crossorigin=&#8221;anonymous&#8221;, this specifies that this resource can be accessed without providing credentials.

The most basic CORS request are simple, they consist of the request to the resources from the client, and a response stating the resource it is requesting, the typical headers as well as a Origin header. The Origin header is similar to the Referrer header except that is has less information. For example, the Referrer header states the entire path and the Origin header only states the server name. When the server receives the CORS request, it verifies if the Origin is allowed. If it is allowed, Access-Control-Allow-Origin is set as a response header.

> <pre>Access-Control-Allow-Origin: <a href="https://jordanpotti.com">https://jordanpotti.com</a></pre>

When the browser receives the response, it verifies that server name sent back the browser in the new header matches the site the browser is visiting. Access-Control-Allow-Credentials also allows the passing of credentials through these requests in order to access to protected content. Some sites allow all by specifying a wild card &#8216;*&#8217; for allowed Origins. This is a very dangerous practice and can open the door to a multitude of attacks.

To read more on the security of CORS, read this: <http://blog.portswigger.net/2016/10/exploiting-cors-misconfigurations-for.html>

For more information on CORS, visit: <https://www.w3.org/TR/cors/>

#### **X-Frame-Options:**

X-Frame-Options has a very specific purpose. It is used to prevent malicious iframe&#8217;s from loading on a site. In regards to other HTTP headers, it is fairly simple with only three directives. DENY, SAMEORIGIN, an ALLOW-FROM &#8216;domain&#8217;. This is to enforce the source of <iframe>, <frame> and <object>. The most common I have seen is SAMEORIGIN which enforces the source to come from the sites own domain.

> <pre>X-Frame-Options: SAMEORIGIN or X-Frame-Options: ALLOW-FROM <a href="https://jordanpotti.com">https://jordanpotti.com</a></pre>

For more information on X-Frame-Options, visit: <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options>

#### **X-XSS-Protection:**

X-XSS-Protections is similar to X-Frame-Options in that it has a narrower scope than some other security headers. It has the ability to report violations, block a page from rendering is it detects a XSS attack as well as the ability to report only and not block. It has two main directives; 0 and 1. 0 disables XSS filtering and 1 enables it. If set to 1 with no other options, the browser will sanitize the page by removing the detected XSS portions. Adding mode=block will prevent the page from loading at all if XSS is detected.

> <pre>X-XSS-Protection: 1; mode=block</pre>

For more information on X-XSS-Protection, visit: <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection>

#### **X-Content-Type:**

X-Content-Type is a HTTP header that tells the server not to overwrite the response content-type. If the server specifies content as non-executable such as text/html, the browser will render it that way. It only has one option making it very easy to setup.

> <pre>X-Content-Type-Options: nosniff</pre>

For more information on X-Content-Type-Options, visit: <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options>

#### **HTTP Strict-Transport-Security**

HSTS is a way for a server to tell the browser to always upgrade a connection to HTTPS. If set, any pages on that domain that only work over HTTP will no longer work.

> <pre>Strict-Transport-Security: max-age=31536000; includeSubDomains; preload</pre>

This specifies how long the browser should respect this header, it covers all sub-domains, and it also is eligible for being added to a browsers HSTS list. This will enforce HTTPS on your site even after the HSTS header is removed.

Visit: <https://hstspreload.org/> to add your site to the HSTS hard-coded list.

For more information on HSTS, visit: <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security>

&nbsp;

To view how your site rates, check: <https://securityheaders.io/> . Scott Helme also has a pretty informative [post][1] on these headers as well so go check that out to learn more.

&nbsp;

&nbsp;

 [1]: https://scotthelme.co.uk/hardening-your-http-response-headers/