# TLS/SSL setup in the nginx server

# 1. Create your private key with openssl
openssl genrsa -des3 -out <your-private-key> 2048

# 2. Sign your private key into domain.csr 
openssl req -key <your-private-key > -new -out <your-domain-signing-request>

# 3. Sign your private key into domain.csr as input and certificate as output
openssl x509 -signkey <your-private-key> -in <your-domain-signing-request> -req -days 365 -out <your-certificate>

# 4. The private is encrypted. Needs to be decrypted using password
openssl rsa -in <your-private-key> -out <your-unencrypted-private-key> -passin pass:<your-password>

# 5. Create secrets which includes your certificate and private key for nginx TLS/SSL configuration
# Encrypted key file and certificates must be present in current directory

kubectl create secret tls <secret-name> --key="<your-unencrypted-key>" --cert="<your-certificate>"

# 6. Extracting the yaml manifest of secrets
kubectl get secret <secret-name> -o yaml > secret.yml

# 7. Create configmap for web server configuration
kubectl create configmap nginx-conf --from-file=default.conf

# 8. Extracting the yaml manifests of configmaps
kubectl get configmaps <config-name> -o yaml > config.yaml