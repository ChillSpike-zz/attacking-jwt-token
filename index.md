Attacking JWT tokens 

A signed JWT is known as a JSON Web Signature (JWS) - It a specification for transferring JWT between two parties.
JWT consists of three components, a header, a payload and the digital signature. These three components are Base64url encoded and separated by dots(.)- example below:
## eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c 
## Since JWT contains  3 parts, our attack surface could be targeted to each of these components
A sample JWT encoded payload would be 

The same JWT paylaod  when decoded would consist of 3 parts: Header,Payload and Signature.

<img src="jwt.io.PNG"></img>

### Header:
 The header usually consists of two attributes: the type of the token, i.e: JWT, and the signing algorithm used. Ex: HMAC SHA256 ,RSA , other supported signing algorithms as per the [RFC7518](https://tools.ietf.org/html/rfc7518#section-3)
 <img src="header.PNG"/>
 
#### Attack surface in Header component [1] :
Along with the supported algorithms , the specification also allows you to define the "alg:None". It was allowed to be used for situations where the integrity of the token was verified or trusted .There are few libraries  that treat such tokens (alg:none) as valid ,allowing an attacker to forge a JWT for any user by creating a JWT with an algorithm of none or None OR nonE. Modifying the signature usin this technique is easy as the attacker could set "alg":"None" and use an empty signature as below:
After changing alg to none, remove the signature from the JWT ===> (header + ‘.’ + payload + ‘.’) and submit it to the server.
** As per [Auth0](https://auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/)-- current implementation  has basic check to prevent this attack ,the token verification would ideally fail for using algorithm none. But it is still a good entry point to investigate .

 
#### Attack surface in Body component [2] :
Rather including the required information developers might often retreive the whole objects from database and put it into the body of JWT resulting in Information disclosure via  the decoded body. As an attacker you could check for password hashes, users address, credit card info or other sensitive data .Often developers do include links to other user's record or another identifier as well.
Certain scenarios requires the presence of  "JTI" field in the body, failure to include this could result in the request being replayed casuign a token replay attack.


#### Attack surface in signature component [3] :Information Disclosure:
The main element in the entire JWT payload is the "secret-key" that is used to sign and verify the integrity of the token.
The secret key used to sign a JWT should be a long random string, making it impossible to guess or crack, but this is not always the case.If you could crack the secret-key used by the signing algorithm to sign a JWT, then you are free to sign your own tokens and tamper the data withing the payload.The secret-key should only be accessible to the issuer and the consumer; it should not be accessible to anybody else.Some tools such as John the Ripper, JWT cracker or Hashcat could be used to brute force the secret-key if the key is not long enough.

