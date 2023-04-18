## Fetch X509 certifiate from a remote TLS server
This example fetches the TLS certificate used to protect the site https://www.example.com (port 443) and prints the expiry date for the certicate (as well as the valid from date).

```
echo QUIT | timeout 3 openssl s_client -showcerts -servername www.example.com -connect www.example.com:443 | openssl x509 -dates
```

## Fetch X509 certifiate from a remote TLS server (with awk)
This example fetches the TLS certificate used to protect the site https://www.example.com (port 443) without printing the expiry date. This migt be slightly
faster than the above example given that awk is rather than the openssl x509 parser in the second step.

```
echo QUIT | timeout 3 openssl s_client -connect www.example.com:443 -showcerts 2>/dev/null | awk '/BEGIN CERTIFICATE/,/END CERTIFICATE/ {print $0}'
```
