# Virtual Private Cloud (VPC) Core Concepts

- VPC is a logically isolated portion of the AWS cloud within a region
- Subnets are networks within a VPC. Subnets are created inside of an Availability Zone (AZ)
- Subnets can be public or private. Instanced in public subnet have their in public IP address
- Route Table: 
    - Solve data routing within a VPC or between a VPC and outside world
    - We can multiple route tables per VPC, each of them assigned to a subnet
- Internet gateway: used to connect with the outside world
    - 0.0.0.0/0 address is added to the route table, all traffic which does not have a private destination is routed through the internet gateway
- We can have multiple VPCs in a region
- Each VPC has a CIDR block assigned (example: `10.0.0.0/16`)
- Default VPC: by default an AWS account has a VPC created in each region
- For the default VPC we get a default subnet created in each AZ for each location (can be between 2 and 6 subnets)
- For the default subnets we get a default internet gateway => each default subnet is basically public

## Public and Private Services

- Public services have a public IP address, they can be reached from the internet
- Everything launched in a VPC gets a private IP address
- Private services don't have a public IP
- Private instances can connect to the internet through a NAT instance
- Private instances can also access other AWS services using a VPC end-point