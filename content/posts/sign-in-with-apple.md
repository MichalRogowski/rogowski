---
title: "Why sign-in with apple may take you more than 5 minutes and how it works?"
date: 2021-06-05T23:33:38+02:00
draft: false
---
### &nbsp; 
I saw a lot of articles why Sign In With Apple (SIWA) is - great, easy etc. Only few of them were showing how difficult it may be and this is one of those. I will list all articles that I found about other problems at the end of the article. I described it from iOS perspective.
### &nbsp; 
BTW! If you like my articles please follow me on [Twitter](https://twitter.com/michrogowski)  Thanks!
### &nbsp; 
## Sign In With Apple 🍏 (SIWA) 
UML diagram below shows register / login flow
![SIWA-UML](https://thepracticaldev.s3.amazonaws.com/i/tnslve4034o1py47ha08.jpg)
1. **SIWA button register / login** - Most important part of request is scope. For test purposes I’ve added email and full name to scope. When pressing button for the first time user is able to decide, does he want to use real email, or “fake” which will forward all messages to and from user.
2. **ASAuthorizationAppleIDCredential** - Apple [Documentation](https://developer.apple.com/documentation/authenticationservices/asauthorizationappleidcredential)  describes it as *A credential that results from a successful Apple ID authentication.* You can also check all fields that are returned. Most important for us are:
	* `var authorizationCode: Data?` - *A short-lived token used by your app for proof of authorization when interacting with the app’s server counterpart.*
	* `var identityToken: Data?` - *A JSON Web Token (JWT) that securely communicates information about the user to your app.*
	* `var user: String` - *An identifier associated with the authenticated user.*
	* `var fullName: PersonNameComponents?` - *The user’s name.*
	* `var email: String?`- *The user’s email address.*
### &nbsp; 
Token authorisation code on the diagram above is `authorizationCode` from this response - we have to send it to our API Server. Now our server will be able to use it to get JWT as response from `Apple /token` servers.
### &nbsp; 
To decode JWT (identityToken) use [jwt.io](https://jwt.io) - all validation and debugging is done on the client side👌. My *register* JWT looks like
#### HEADER
```json
{
  "kid": "AIDOPK1",
  "alg": "RS256"
}
```
#### PAYLOAD
```json
{
  "iss": "https://appleid.apple.com",
  "aud": "com.myapp.app",
  "exp": 1571516448,
  "iat": 1571515848,
  "sub": "222222.11111111111111111111111111111111.3334",
  "c_hash": "9C1Y7-lPwAXGTrrAbciP1T",
  "email": "hap41ok9pl@privaterelay.appleid.com",
  "email_verified": "true",
  "is_private_email": "true",
  "auth_time": 1571515848
}
```
### &nbsp; 
Same JWT will be provided to API Server in step 5 but with different timestamps and *hash* key and value. *sub* is my user id associated with this application, *aud* is bundle of the application. *hash*, *kid* and *alg* are needed to confirm that this JWT was created by apple, and confirm it with Apple [public key](https://appleid.apple.com/auth/keys) it will be done in step 6 by API server. 
3. **Register / Login** - We are sending our `authorizationCode` as part of our request.
4. **/token** - thanks to `authorizationCode` API Server is able to send [/token](https://appleid.apple.com/auth/token) request with parameters:
``` json
{
	grant_type: "authorization_code",
	code: "this is authorizationCode",
	client_id: "bundle id same value as in “aud” in” JWT above",
	client_secret: "client secret generated for this app"
}
```
5. **TokenResponse** - response for */token* is
```json
{
    "access_token": "a5a45fnksdnfk83jflkasfksd98c29fdf1.1.pfsmo.bfd324145555r15",
    "token_type": "Bearer",
    "expires_in": 3600,
    "refresh_token": "r7999999999d0485b85a0d6124124140.1.pfsmo.YFFFFFsadazJYjzw",
    "id_token": "JWT token"
}
```
Put your `id_token` again to [jwt.io](https://jwt.io)  and decode - user values are same as in JWT from `ASAuthorizationAppleIDCredential`- things that are different are described above.  We can notice that there is no full name returned by the JWT, so we have to pass it from app. 
6. **Register / login user** - server needs to confirm with Apple [public key](https://appleid.apple.com/auth/keys)  that JWT was created by them, and not injected in any way. Now we can create our user we have verified *email*, *userID* so everything we need.
7. **Establish user session** - response returns our new user with accessToken and refreshToken.
### &nbsp; 
## 🆒 so where is the problem?
As you may noticed I described register path, with register JWT but how does login JWT looks like? 
#### PAYLOAD STEP 5)
```json
{
  "iss": "https://appleid.apple.com",
  "aud": "com.myapp.app",
  "exp": 1571516802,
  "iat": 1571516202,
  "sub": "222222.11111111111111111111111111111111.3334",
  "at_hash": "ArC1Y7-lPwAXGTrrAbciP1",
  "auth_time": 1571516173
}
```
### &nbsp; 
### *Ok so where is the email?*
This is our problem, there is no *email* key in login flow. That means your API Service not only have to implement Apple version of OpenID Connect - main difference for us is no [/userinfo](https://connect2id.com/products/server/docs/api/userinfo) endpoint. It’s not OpenID Connect compliant, you can check [here](https://bitbucket.org/openid/connect/src/default/How-Sign-in-with-Apple-differs-from-OpenID-Connect.md) how it differs. If you are using email to *authenticate* a user, you may have a problem. Email shouldn’t be mandatory in your system and not only because you may have problem with SIWA, but if you are supporting other identity providers like Facebook, how do you behave if someone has created an account with his/her phone number? 
### &nbsp; 
### Summary
Why Sign In With Apple may be problematic? This article was published at 20th of October 2019 - and for that day apple wasn’t OpenID Connect compliant.  Email was only returned once during registration flow, if your API Service needs *email* to authenticate user you got a problem. Is it bad? I don’t think so - email shouldn’t be mandatory, Facebook may not return email as well if someone has created account using phone number. If you have to migrate start it now because *Apple Requiring New Apps and App Updates to Be Built With iOS 13 SDK Starting in April 2020* so the clock is ticking. 
### &nbsp; 
List of helpful articles:
[What the Heck is Sign In with Apple? | Okta Developer](https://developer.okta.com/blog/2019/06/04/what-the-heck-is-sign-in-with-apple)
[So They’ve Signed in with Apple, Now What? — Curtis Herbert](https://blog.curtisherbert.com/so-theyve-signed-in-with-apple-now-what/)
[“Sign in with Apple” implementation hurdles - DEV Community 👩‍💻👨‍💻](https://dev.to/costerad/sign-in-with-apple-implementation-hurdles-761) by @costerad
[How to Add Sign in with Apple to iOS Apps - Getting Started Guide](https://auth0.com/blog/learn-how-to-implement-sign-in-with-apple-easily/?utm_source=dev&utm_medium=sc&utm_campaign=siwa_ga) by @auth0
### &nbsp; 
Thanks!