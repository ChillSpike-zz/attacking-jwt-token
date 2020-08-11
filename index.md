Attacking JWT tokens 


## Since JWT contains  3 parts, our attack surface could be targeted to each of these components
### Header:
 The header usually consists of two attributes: the type of the token, i.e: JWT, and the signing algorithm used. Ex: HMAC SHA256 ,RSA , other supported signing algorithms as per the [RFC7518](https://tools.ietf.org/html/rfc7518#section-3)

<img src="https://github.com/ChillSpike/attacking-jwt-token/blob/gh-pages/jwt.io.PNG">
