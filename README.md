Project Description
My goal was to analyze the OWASP Zed Attack Proxy Root CA certificate to extract a hidden flag. To achieve this, I studied the certificate’s structure, examined its fields, and tested different encoding and hashing methods of key data to understand how the system forms and verifies the flag.

Work Done
1. Certificate Analysis
I obtained the certificate in .cer format and viewed its contents using:

openssl x509 -in owasp_zap_root_ca.cer -noout -text
From the output, I saw that the localityName (L) field contained the string 3580e22cbd191. This looked like a key piece of information for extracting the flag.

2. Converting the Certificate to DER and Parsing ASN.1
To study the structure more deeply, I extracted the content between BEGIN CERTIFICATE and END CERTIFICATE and saved it as cert.b64:

awk 'BEGIN{ok=0} /BEGIN CERTIFICATE/{ok=1; next} /END CERTIFICATE/{ok=0} ok' owasp_zap_root_ca.cer > cert.b64
base64 -d cert.b64 > cert.der
Then, I parsed the DER format using:

openssl asn1parse -in cert.der -inform DER
This allowed me to understand how the certificate fields were organized and confirmed the presence of the target string in the localityName field.

3. Checking String Encodings and Hashes
I checked various encodings and hashes of the string 3580e22cbd191:

echo -n "3580e22cbd191" | base64
# MzU4MGUyMmNiZDE5MQo=

echo -n "3580e22cbd191" | md5sum
# 0280761316e1f999fc0852c965eab89c

echo -n "3580e22cbd191" | sha1sum
# e6fa01b1342bb239c52f06c49a06e2918a1a2e3e

echo -n "3580e22cbd191" | sha256sum
# f14d616554fac0c0ec9c4b72b79ac288ec2198fc799ccb5dbba2388ee92550a9
However, manually guessing the flag didn’t work because the system itself should provide the flag automatically upon correct interaction.
