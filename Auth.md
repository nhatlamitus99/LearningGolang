# Auth

### JWT
### OAuth2.0

1. What is JWT ?
    - JWT is a standard for user authentication and also information exchange.
    - JWT is stateless, include 3 parts: Header.Payload.Signature
      + Header: encryption algorithm, token type
      + Payload: expiration time, user data (avoid storing sensitive data like password)...
      + Signature: encoded header + encoded payload + secret key 
    ![pic_1](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/IMG_20201030_095936.jpg) 
       ![pic_2](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/IMG_20201030_095925.jpg)
    - Secret key: in case of jwt is decoded, signature is still secured by using secret key. 
    
2. Store token
    - Tokens don't need to store at Server, just store at Client.
    - Highly recommendation is that store jwt in Cookie and set flag HttpOnly to avoid XSS attacks.
    
3. Access Token vs Refresh Token
      + Access token is short life-span token uses for authenticating requests.
        - if exp time is long => jwt is easier to be damaged by hacker
        - if exp time is short => user must re-login more time
        => Solution: Refresh Token
      + Refresh token is long life-span token uses for generating new access token and refresh token periodically.

4. Work flow to authen by JWT

      ![pic_3](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-30-11-08-53-22.jpg)
      
5. Work flow to refresh an expired access token
    
      ![pic-4](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-30-17-54-31-64.jpg)

6. What is OAuth ?
    - OAuth2.0 is an authorization framework that permit third-party app to access protected resources from resource server.
    - 4 roles in oauth2.0:
        + Client (third-party app): where need resource
        + Resource Owner : user
        + Authorization Server : where validate delegation and return token to client
        + Resource Server : where store resource needing to share
    - 4 types of grant in oauth2.0:
        + Authorization Code Grant
        + Implicit Grant
        + Resource Owner Password Grant
        + Client Credentials Grant
        
      
7. Authorization Code Grant:

      ![pic_5](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-30-15-48-56-35.jpg)
      
8. Resource Owner Password Grant:

      ![pic_6](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/oauth-2.0-resource-owner-password%20credentials-grant-process.png)
      
    
