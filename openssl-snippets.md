#Fetch the certifiate from a remote TLS server and print out the expiry dates

```
echo "" | openssl s_client -showcerts -servername staging.grid.kognif.ai -connect staging.grid.kognif.ai:443 | openssl x509 -dates
```
