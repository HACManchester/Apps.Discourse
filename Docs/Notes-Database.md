# Database Notes

## Email Tokens

Based on this link

  * https://meta.discourse.org/t/getting-dev-instance-to-send-email/2507/20

When a user needs to confirm his email address is valid, a token is placed into the email_tokens table <br>
They then activate with http://localhost:5000/users/activate-account/:email_token


