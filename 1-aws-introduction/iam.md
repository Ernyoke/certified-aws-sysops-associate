# IAM: Identity Access & Management Introduction

- An IAM user account as an entity that represents a person or a service
- An IAM policy is a document that defines the permissions which are applied to users, groups and roles
    - Inline policies: policies which can be attached directly to the user
- IAM Groups are a collective of users which have the same policies applied to them. Policies applied to the group are inherently applied to the users which are par of the group. This makes easier to manage and apply policies for multiple users
- An IAM Role is an identity that can be assumed by a service

## Authentication Methods

- Access Keys: 
    - Consists of an Access key ID and a secret key
    - User for programmatic access tot he API
- IAM User accounts and passwords:
    - Used for authenticating to the AWS Management Console
- Signing Certificate:
    - X.%09 certificate used for accessing certain AWS services
