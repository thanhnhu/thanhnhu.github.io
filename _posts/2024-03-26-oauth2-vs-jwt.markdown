---
layout: post
title:  "OAuth2 vs JWT"
date:   2024-03-26 08:40:00 +0700
categories: programming
permalink: /oauth2-vs-jwt/
---
`OAuth 2.0` is a powerful and secure framework that allows different applications to securely interact with each other on behalf of users without sharing sensitive credentials.
The entities involved in OAuth are the User, the Server, and the Identity Provider (IDP).

What can an OAuth Token do?

When you use OAuth, you get an OAuth token that represents your identity and permissions. This token can do a few important things:

1. `Single Sign-On (SSO)`: With an OAuth token, you can log into multiple services or apps using just one login, making life easier and safer.
2. `Authorization Across Systems`: The OAuth token allows you to share your authorization or access rights across various systems, so you don't have to log in separately everywhere.
3. `Accessing User Profile`: Apps with an OAuth token can access certain parts of your user profile that you allow, but they won't see everything.
   
Remember, OAuth 2.0 is all about keeping you and your data safe while making your online experiences seamless and hassle-free across different applications and services.

#### Here's how OAuth works (a quick breakdown):
`The Password Grant`: The Password flow in OAuth is where you exchange a username/password for an Access Token (usually a JWT). You then use the Access Token in the HTTP Authorization header to identify yourself with your API service. This is what most people do when building SPAs with Angular / React, as well as mobile apps.

`The Client Credentials Grant`: The Client Credentials flow is where you exchange an API key (just like basic auth) for an Access Token. You then use the Access Token in the HTTP Authorization header to identify yourself with your API service. This is what people do when building server side apps with OAuth.

`The Implicit Grant`: This flow is what you see when you log into some place like Facebook. You click a button, are redirected to some other site to authenticate / accept permissions, and finally you're returned back to the main site with an Acccess Token that you use to identify yourself. This is NOT ideal for API services.

`The Authorization Code Grant`: This flow is exactly like the implicit flow, except you get back an authorization code that you then EXCHANGE for an Access Token that you use to identify yourself. This is NOT ideal for API services. It's slightly more secure.

#### JWT
JWT is an access token formatted as JSON which is issued by an authorization (phân quyền/cấp quyền/cho phép #authentication:xác thực) server. Need to decrypt with a secret key to get info.
Standard Restful is stateless, so should use JWT for this.

Imagine you have a special box called a JWT. Inside this box, there are three parts: a header, a payload, and a signature.

The `header` is like the label on the outside of the box. It tells us what type of box it is and how it's secured. It's usually written in a format called JSON, which is just a way to organize information using curly braces { } and colons : .

The `payload` is like the actual message or information you want to send. It could be your name, age, or any other data you want to share. It's also written in JSON format, so it's easy to understand and work with.

Now, the `signature` is what makes the JWT secure. It's like a special seal that only the sender knows how to create. The signature is created using a secret code, kind of like a password. This signature ensures that nobody can tamper with the contents of the JWT without the sender knowing about it.

When you want to send the JWT to a server, you put the header, payload, and signature inside the box. Then you send it over to the server. The server can easily read the header and payload to understand who you are and what you want to do.

#### Why do we need Refresh Tokens?
If we are using an `access token` for a long time, there is a chance a hacker can steal our token and misuse it. Hence it is not very safe to use the access token for a long period.

`Refresh tokens` are the kind of tokens that can be used to get new access tokens. When the access tokens expire, we can use refresh tokens to get a new access token from the authentication controller. Or   `Refresh token` is the way that lets a client application get a new access token for a user without prompt/ask the user to log in again.  
The access token will have less expiry time and Refresh will have long expiry time.  
The client (FrontEnd) should store refresh token in an httponly cookie and access token in local storage.