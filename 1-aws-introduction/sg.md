# Security Groups and Access Control Lists (NACLs)

- Security groups and access control lists are **Firewalls** in AWS VPCs.
- Security groups:
    - Security groups operate at the instance level
    - They can be applied to one or more instances
    - They filter traffic at the instance level
    - They evaluate all rules
    - They are stateful firewalls
- NACLs:
    - NACLs operate at subnet level
    - They filter traffic at subnet level
    - They process rules in order
    - They are stateless firewalls