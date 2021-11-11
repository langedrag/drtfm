
Self-signed certificate with OpenSSL

The below OpenSSL one-liner will create a certificate for host1.mydomain.com with an rsa key of 2048 bit strength and a validity of 3650 days.

$ hostdn=host1.mydomain.com; \
openssl req -new -x509 \
-newkey rsa:2048 \
-days 3650 \
-sha256 -nodes \
-out $hostdn.crt \
-keyout $hostdn.key \
-config <(cat <<EOF
[req]\nx509_extensions = v3_req
distinguished_name = dn
prompt = no
[dn]
CN = $hostdn
[v3_req]
subjectAltName = @alt_names
[alt_names]
DNS.1 = $hostdn
EOF
)
