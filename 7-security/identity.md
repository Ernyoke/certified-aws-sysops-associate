# Identity

## IAM + MFA

- MFA adds a new layer of security while accessing an AWS account
- AWS MFA accepts both virtual of hardware MFA device
- Virtual MFA devices: Google Authenticator, Authy
- MFA for root user can be configured from the IAM dashboard
- MFA can also be configured from CLI
- MFA can be configured for individual users from an account
- Credential Report:
    - A CSV report file on all the IAM users and credentials
    - Shows who have MFA enabled

## IAM PassRole Option

- When an EC2 instance is created for example, we can assign an role to it
- In order to be able to assign a role to an EC2 instance, we need `IAM:PassRole` role

## AWS STS - Security Token Service

- Allows to grant limited and temporary access to AWS resources
- Token is valid for up to one hour, must be refreshed afterwards
- STS allows for cross account access: allows user form one AWS account to access resources from another AWS account
- Allows for federation (Active Directory):
    - Provides a non-AWS user with temporary AWS access by linking users AD credentials
    - Uses SAML (Security Assertion Markup Language)
    - Allows Single Sign ONe (SSO) which enables users to log into AWS console without assigning IAM credentials directly
- Allows federation with third party providers and Cognito
    - Used mainly for web and mobile applications
    - Makes use of Facebook/Google/Amazon etc to federate them

## Cross Account Access

- We define an IAM role for another account to access
- We define which accounts can access this IAM role
- We use AWS STS to retrieve credentials and impersonate the IAM role we have to access (`AssumeRole API`)

## Identity Federation

- Federation lets users outside of AWS to assume temporary role for accessing AWS resources
- These users assume an identity provided access role
- Federation assumes a form of 3rd party authentication:
    - LDAP
    - Microsoft Active Directory (similar to SAML)
    - Single Sign On
    - Open ID
    - Cognito
- Using federation we don't need to create individual IAM users

## SAML Federation for Enterprises

- To integrate Active Directory / ADFS with AWS (ror SAML 2.0)
- Provides access to AWS console or CLI through temporary credentials
- There is no need to create an IAM user for each of our employees

## Custom Identity Broker Application for Enterprises

- Use only if identity provider is not compatible with SAML 2.0
- The identity broker must determine the appropriate IAM policy

## AWS Cognito - Federated Identity Pools for Public Applications

- Goal:
    - Provide direct access to AWS resources form the client side
- How:
    - Log in to federated identity provider or remain anonymous
    - Get temporary AWS credentials back from Federated Identity Pool
    - These credentials come with a predefined IAM policy stating their permissions
- Examples:
    - Provide temporary access to write to S3 bucket using Facebook login