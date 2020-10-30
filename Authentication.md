# Authentication

### JWT
### OAuth2.0

1. What is JWT ?
    - JWT is a standard for user authentication
    - JWT is stateless, include 3 parts: Header.Payload.Signature
      + Header: encryption algorithm, token type
      + Payload: expiration time, user data (avoid storing sensitive data like password)...
      + Signature: encoded header + encoded payload + secret key 
    ![pic_1](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/IMG_20201030_095936.jpg) 
       ![pic_1](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/IMG_20201030_095925.jpg)
    - Secret key: in case of jwt is decoded, signature is still secured by using secret key. 
    
2. Store token ? 
    - Tokens don't need to store at Server, just store at Client.
    - Highly recommendation is that store jwt in Cookie and set flag HttpOnly to avoid XSS attacks.
    
3. Access Token vs Refresh Token
  + Access token is short life-span token uses for authenticating requests.
    - if exp time is long => jwt is easier to be damaged by hacker
    - if exp time is short => user must re-login more time
    => Solution: Refresh Token
  + Refresh token is long life-span token uses for generating new access token and refresh token periodically.
  
4. Work flow to Authentication via JWT
