# Deploy a Single Server

AWS has datacenters all over the world, grouped into regions. An *AWS region* is a separate geographic area, such as useast-2 (Ohio), eu-west-1 (Ireland), and ap-southeast-2 (Sydney). Within each region, there are multiple isolated datacenters known as Availability Zones (AZs), such as us-east-2a, useast-2b, and so on

For each type of provider, there are many different kinds of resources that you can create, such as servers, databases, and load balancers. The
general syntax for creating a resource in Terraform is:

```hcl
resource "<PROVIDER>_<TYPE>" "<NAME>" {
    [CONFIG ...]
}
```

- `PROVIDER`: the name of provider (e.g., aws)
- `TYPE`: type of resource to create in that provider (e.g. instance)
- `NAME`: an identifier you can use throughput the Terraform code to refer to this resource (e.g. my_instance)
- `CONFIG`: consists of one or more *arguments* that are specific to that resource.

### ami

The Amazon Machine Image (AMI) to run on the EC2 Instance. You can find free and paid AMIs in the AWS Marketplace or
create your own using tools such as Packer (see “Server Templating Tools” for a discussion of machine images and server
templating). The preceding code example sets the ami parameter to the ID of an Ubuntu 18.04 AMI in us-east-2. This AMI is free to use.

### instance_type

The type of EC2 Instance to run. Each type of EC2 Instance provides a different amount of CPU, memory, disk space, and
networking capacity. The EC2 Instance Types page lists all the available options. The preceding example uses t2.micro, which
has one virtual CPU, 1 GB of memory, and is part of the AWS free tier.

### `terraform` binary

The terraform binary contains the basic functionality for Terraform, but it does not come with the code for any of the providers
(e.g., the AWS provider, Azure provider, GCP provider, etc.), so when you’re first starting to use Terraform, you need to run `terraform
init` to tell Terraform to scan the code, figure out which providers you’re using, and download the code for them. By default, the provider
code will be downloaded into a `.terraform` folder, which is Terraform’s scratch directory (you may want to add it to `.gitignore`). 

### `terraform plan`

The `plan` command lets you see what Terraform will do before actually making any changes. This is a great way to sanity check your
code before unleashing it onto the world. The output of the `plan` command is similar to the output of the `diff` command that is part of
Unix, Linux, and git: anything with a plus sign (+) will be created, anything with a minus sign (–) will be deleted, and anything with a
tilde sign (~) will be modified in place. 

### `terraform apply`

The `apply` command shows you the same plan output and asks you to confirm whether you actually want to proceed with this plan. So, while plan is available as a separate command, it’s mainly useful for quick sanity checks and during code reviews 

```bash
aws_instance.example: Refreshing state...
(...)
Terraform will perform the following actions:
# aws_instance.example will be updated in-place
    ~ resource "aws_instance" "example" {
        ami = "ami0c55b159cbfafe1f0"
        availability_zone = "us-east-2b"
        instance_state = "running"
        (...)
        + tags = {
            + "Name" = "terraform-example"
        }
        (...)
    }
Plan: 0 to add, 1 to change, 0 to destroy.
```

Terraform keeps track of all the resources it already created for this set of configuration files, so it knows your EC2 Instance already exists
(notice Terraform says Refreshing state… when you run the apply command), and it can show you a diff between what’s
currently deployed and what’s in your Terraform code (this is one of the advantages of using a declarative language over a procedural one)