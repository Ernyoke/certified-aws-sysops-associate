# Encryption

## Encryption in Flight (SSL)

- Data is encrypted before sending and decrypted after it was received
- SSL certificates help with encryptions (HTTPS)
- Encryption in flight ensures no MITM attack can happen

## Server Side Encryption at Rest

- Data is encrypted after being received by the server
- Data is decrypted before being sent back to the client
- Data is encrypted with a key (data key)
- The encryption/decryption key must be managed somewhere and the server must have access to it

## Client Side Encryption

- Data is encrypted by the client, the server will never be decrypted by the server
- Data will be decrypted by a receiving client, the server should not be able to decrypt the data
- Client side encryption can leverage Envelope Encryption


## AWS KMS - Key Management Service

- Easy wy to control access to data, AWS will manage the encryption keys
- Fully integrated with IAM for authorization
- Seamlessly integrates with other services:
    - EBS: encrypt volumes
    - S3: server side encryption
    - Redshift: encryption of data
    - RDS: encryption of data
    - SSM: encryption of stored parameters
    - Etc.
- We can also use the CLI/SDK to perform client side encryption
- Anytime we need to share sensitive information, we have to use KMS
- The value in KMS is the CMK (customer-master key) used to encrypt data can never be retrieved by the user. The CMK can be rotated for extra security
- We should never store secrets in plaintext, especially in source code!
- Encrypted secrets can be stored in the code/env. variables
- KMS can only help encrypt up to 4KB of data per call
- If data > 4KB, we have to use Envelope Encryption
- To give access to KMS to someone:
    - We have to make sure the key policy allows the user
    - We have to make sure the IAM policy allows the API calls
- We are able to fully manage the keys and policies:
    - Creation policies
    - Rotation polices
    - Disable/Enable policies
- We can use CloudTrail to audit key usage
- There are 3 types of CMKs:
    - AWS Managed Service Default CMK: free
    - User Keys created in KMS: $1/month
    - User Keys imported (must be 256-but symmetric key): $1/moth
- KSM pricing for API calls: $0.003/10000 calls
- Encryption in AWS services:
    - Requires migration (using Snapshots/Backups):
        - EBS volumes
        - RDS databases
        - ElastiCache
        - EFS network file system
    - In-place encryption:
        - S3

## CloudHSM

- KMS => AWS manages the software for encryption
- CloudHSM => AWS provisions encryption hardware
- Dedicated Hardware (HSM = Hardware Security Module)
- We manage our own encryption keys entirely (not AWS)
- The CloudHSM hardware device is temper resistant
- Provides FIPS 140-2 Level 3 compliance
- CouldHSM clusters are spread across multi AZ (HA)
- Supports both symmetric and asymmetric encryption (SSL/TLS keys)
- No free tier available
- We use the CloudHSM Client software
