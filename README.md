# demicon_task

## Description Task Cloud Developer

In the D3 team at demicon, we work both at the customer site and internally as far as possible with
Terraform to manage AWS infrastructures. However, there are AWS services that cannot yet be
accessed via Terraform. Here, another option is to use CloudFormation.
One challenge that arises here is that the two tools do not have access to dynamically generated
resources from the other tool. Let's assume that a base infrastructure has been created with
Terraform and an AWS application load balancer has been provisioned here that stores a URL as an
output parameter in Terraform State. This url is required in Cloud Formation. CloudFormation Custom
Resources can be used to trigger Lambda functions and use the return values as inputs for other
cloud formation resources.

## Task:

Create a Lambda function in the programming language of your choice that allows to retrieve an
arbitrary terraform state file from S3 and return terraform outputs from the state file, such as the URL
of an AWS application load balancer in the above example. The Lambda function should be managed
using Terraform IaC. By default all outputs from the terraform state should be returned and optionally
the function should accept an input parameter to return a specific output from the terraform state
file.

## Solution:

As the task was only about implementing a lambda function with terraform, I assume the following prerequisites:
1. A S3 bucket with the name "demicon-s3" exists.
2. Terraform has configured its backend to use this bucket, and its status file "terraform.tfstate" exists on the root of the bucket mentioned.
3. In previous terraform run, it was configured to have some output.
4. As you know, if you want to use this function, you can use "trigger" or "function_url" ( POST method without encryption ).
5. I do not want to make it more complex!

### How to implement
1. Make sure aws and terrafrm installed and configured.
2. Add AWS S3 bucket with name "demicon-s3", put an example terraform status file "terraform.tfstate" on the root.
3. Pull the repository files in a new folder:
```bash
$ git clone https://github.com/alireza0/demicon_task
```
4. Unzip and run the code:
```bash
$ cd demicon_task
$ terraform init
$ terraform plan
$ terraform apply
```

### How to test
To use the lambda function you can trigger it by other component or call it via a URL. HTTPS direct request could be apply by enabling "function url" of lambda function. The the url of lambda cuold be called by browser, `curl` or API test softwares like **postman**. Result will be all `output` data.

```bash
$ curl -X POST https://<function_url>
```

To ask one `output` value from the function, you could send an unencrypted POST call with "param=\<requested key\>":
```bash
$ curl -H "Content-Type: application/json" -d "param=<key>" -X POST https://<function_url>
```
**Example**
```bash
$ curl -X POST https://sv4eceq7l3zwdkjkd7dlbjybi40vvqsi.lambda-url.eu-central-1.on.aws/
{"sample_url": {"value": "http://www.google.de", "type": "string"}, "subnet_cidr_blocks": {"value": ["172.31.1.0/24"], "type": ["tuple", ["string"]]}, "vpc_id": {"value": "vpc-b59d26df", "type": "string"}}

$ curl  -H "Content-Type: application/json" -d "param=sample_url" -X POST https://sv4eceq7l3zwdkjkd7dlbjybi40vvqsi.lambda-url.eu-central-1.on.aws
"http://www.google.de"
```

### Sample terraform state file
```json
{
"version": 4,
"terraform_version": "1.2.5",
"serial": 4,
"lineage": "b90e0e57-8d83-e453-1635-52b24c20ba8a",
"outputs": {
"sample_url": {
"value": "http://www.google.de",
"type": "string"
},
"subnet_cidr_blocks": {
"value": [
"172.31.1.0/24"
],
"type": [
"tuple",
[
"string"
]
]
},
"vpc_id": {
"value": "vpc-b59d26df",
"type": "string"
}
},
"resources": [
{
"mode": "data",
"type": "aws_subnet",
"name": "example",
"provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
"instances": [
{
"index_key": "subnet-0c1be6326c60a76c0",
"schema_version": 0,
"attributes": {
"arn": "arn:aws:ec2:eu-central-1:043552108237:subnet/subnet-0c1be6326c60a76c0",
"assign_ipv6_address_on_creation": false,
"availability_zone": "eu-central-1a",
"availability_zone_id": "euc1-az2",
"available_ip_address_count": 251,
"cidr_block": "172.31.1.0/24",
"customer_owned_ipv4_pool": "",
"default_for_az": false,
"enable_dns64": false,
"enable_resource_name_dns_a_record_on_launch": false,
"enable_resource_name_dns_aaaa_record_on_launch": false,
"filter": null,
"id": "subnet-0c1be6326c60a76c0",
"ipv6_cidr_block": "",
"ipv6_cidr_block_association_id": "",
"ipv6_native": false,
"map_customer_owned_ip_on_launch": false,
"map_public_ip_on_launch": false,
"outpost_arn": "",
"owner_id": "043552108237",
"private_dns_hostname_type_on_launch": "ip-name",
"state": "available",
"tags": {
"Name": "public subnet 1"
},
"vpc_id": "vpc-b59d26df"
},
"sensitive_attributes": []
}
]
},
{
"mode": "data",
"type": "aws_subnet_ids",
"name": "example",
"provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
"instances": [
{
"schema_version": 0,
"attributes": {
"filter": null,
"id": "vpc-b59d26df",
"ids": [
"subnet-0c1be6326c60a76c0"
],
"tags": null,
"vpc_id": "vpc-b59d26df"
},
"sensitive_attributes": []
}
]
},
{
"mode": "managed",
"type": "aws_subnet",
"name": "public_subnet_1",
"provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
"instances": [
{
"schema_version": 1,
"attributes": {
"arn": "arn:aws:ec2:eu-central-1:043552108237:subnet/subnet-0c1be6326c60a76c0",
"assign_ipv6_address_on_creation": false,
"availability_zone": "eu-central-1a",
"availability_zone_id": "euc1-az2",
"cidr_block": "172.31.1.0/24",
"customer_owned_ipv4_pool": "",
"enable_dns64": false,
"enable_resource_name_dns_a_record_on_launch": false,
"enable_resource_name_dns_aaaa_record_on_launch": false,
"id": "subnet-0c1be6326c60a76c0",
"ipv6_cidr_block": "",
"ipv6_cidr_block_association_id": "",
"ipv6_native": false,
"map_customer_owned_ip_on_launch": false,
"map_public_ip_on_launch": false,
"outpost_arn": "",
"owner_id": "043552108237",
"private_dns_hostname_type_on_launch": "ip-name",
"tags": {
"Name": "public subnet 1"
},
"tags_all": {
"Name": "public subnet 1"
},
"timeouts": null,
"vpc_id": "vpc-b59d26df"
},
"sensitive_attributes": [],
"private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjo2MDAwMDAwMDAwMDAsImRlbGV0ZSI6MTIwMDAwMDAwMDAwMH0sInNjaGVtYV92ZXJzaW9uIjoiMSJ9"
}
]
}
]
}
```
