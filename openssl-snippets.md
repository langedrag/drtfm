## Fetch the certifiate from a remote TLS server
This example fetches the TLS certificate used to protect the site https://www.example.com (port 443) and prints the expiry date for the certicate (as well as the valid from date).

```
echo "" | openssl s_client -showcerts -servername www.example.com -connect www.example.com:443 | openssl x509 -dates
```
