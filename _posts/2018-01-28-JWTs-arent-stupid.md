---
layout: post
authors:
    - mjones
date: 2018-01-28 3:00:00 -0500
title: JWTs Aren't Stupid, but Your Monolith Might Be
images:
 - image_path: /images/SSOCrossDomain.png
   title:
   weight: 29
---

When the schedule came out for CodeMash 2018, I knew I wanted to attend Randall Degges's talk "JWTs Suck (and are stupid)". As a developer in a shop that uses JWTs for user session tokens, I have to admit it stoked my impostor syndrome. But it's always best to learn about flaws in your design as early as you can. So on Friday at 9:45, I sat down and got ready to take a beating.

Randall's talk was a provocative conversation-starter. [Here](https://speakerdeck.com/rdegges/jwts-suck-and-are-stupid) are the slides; I'd recommend skimming through them before going any further. I'm assuming you have a high-level understanding of JWTs and session cookies, which the slides should give you. Before I critique the talk, I have to give Randall credit for raising some hard truths about JWTs that developers have a tendency to overlook:

* JWTs should be stored in cookies, not local storage. This protects against cross-site scripting.
* No sensitive data, including contact information, should ever go into the payload of an un-encrypted JWT. On the other hand, authorization info, like group or role information, is fair game and will save you trips to the database.
* JWTs are hard to revoke individually. Revocation lists defeat the purpose of JWTs by forcing you to access persistent storage on every request. And swapping out a cert or a shared secret in production logs out all your users, not just the "bad" ones.

I also agree with Randall's recommended use case for JWTs as very short-lived federated ID assertions. Used this way, JWTs could serve as a drop-in replacement for a framework like SAML.

Despite all that common ground, Randall didn't convince me that I should abandon the JWT. He dismisses JWTs as a case of overhyped "new hotness". After all, they've only been around since 2012. What he doesn't say is that around the same time, another big idea was gaining momentum in the software community: [microservices](http://2012.33degree.org/talk/show/67). This timing was not a coincidence. Controlling user sessions via persistent storage in a microservice architecture would be "[grossly inefficient](https://nordicapis.com/how-to-control-user-identity-within-microservices/)". Think about it: on a client request, you should expect several service-to-service calls. Are each of those services supposed to check the session table? This is an unacceptable amount of overhead.

By leaving out  microservices, Randall basically misses the point of JWTs. Where he touches on efficiency and scaling, he focuses on the sheer size of the token. He admits that we're only talking about tens of bytes' worth of difference.

Randall overplays his hand throughout this talk. He paints certain situations as clear wins for database sessions that are more like stalemates. Take for example the case of authentication into multiple domains by submitting one login form. In slide 43, we're shown a common setup. The domain "login.product.com" handles authentication, and "dashboard.product.com" represents the product itself.

{% for image in page.images %}
<img src="{{ image.image_path }}"/>
{% endfor %}

 He's being sarcastic with that slide title, but I agree with it at face value. Randall envisions JWTs as very short-lived. He gives dashboard.product.com the responsibility of creating a database session ID and sending it back to the client. To implement single sign-on across multiple domains in this setup, you could have each application talk to the same session database.Wouldn't it be just as easy, if not slightly easier, to have each application share a secret and use the JWT you have to make anyway?

To be fair, sometimes a monolith is the right tool for the job. If that's the situation you're in, then by all means use your favorite web framework and the database session tokens that come with it. Are you using microservices? Use JWTs. Are you already using JWTs? Keep them, but consider adding a revocation list if revoking individual sessions is a must-have. Above all, if you use JWTs, make sure you follow Randall's JWT best practices.

Stay safe out there.
