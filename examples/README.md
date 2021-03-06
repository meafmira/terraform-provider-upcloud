# Examples for terraform-provider-upcloud

This is a full example which shows how you can set up your own UpCloud instance by using Terraform.

## Initializing local environment

First install this provider and initialize Terraform in this directory.

```
$ git clone https://github.com/vtorhonen/terraform-upcloud-provider
$ cd terraform-upcloud-provider/examples
$ go install github.com/vtorhonen/terraform-upcloud-provider
$ terraform init

Initializing provider plugins...

Terraform has been successfully initialized!
```

## Configuring the plan

Set up your credentials to `01_server.tf`. Modify `login` block accordingly and set up your own SSH keys.
In this example you can also remove `keys` parameter, since UpCloud will auto generate a password for you
and deliver it via SMS.

Plan your changes.

```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + upcloud_server.test
      id:                   <computed>
      cpu:                  "2"
      hostname:             "my-awesome-hostname"
      ipv4:                 "true"
      ipv4_address:         <computed>
      ipv4_address_private: <computed>
      ipv6:                 "true"
      ipv6_address:         <computed>
      login.#:                           "1"
      login.868081814.create_password:   "true"
      login.868081814.keys.#:            "1"
      login.868081814.keys.0:            "ssh-rsa <snip>"
      login.868081814.password_delivery: "sms"
      login.868081814.user:              "tf"
      mem:                  "2048"
      os_disk_size:         "20"
      os_disk_tier:         "maxiops"
      os_disk_uuid:         <computed>
      private_networking:   "true"
      template:             "CentOS 7.0"
      title:                <computed>
      zone:                 "fi-hel1"

Plan: 1 to add, 0 to change, 0 to destroy.
```

## Applying the plan

Apply the plan by running `terraform apply`.

```
$ terraform apply
upcloud_server.test: Creating...
  cpu:                               "" => "2"
  hostname:                          "" => "my-awesome-hostname"
  ipv4:                              "" => "true"
  ipv4_address:                      "" => "<computed>"
  ipv4_address_private:              "" => "<computed>"
  ipv6:                              "" => "true"
  ipv6_address:                      "" => "<computed>"
  login.#:                           "" => "1"
  login.868081814.create_password:   "" => "true"
  login.868081814.keys.#:            "" => "1"
  login.868081814.keys.0:            "" => "<snip>"
  login.868081814.password_delivery: "" => "sms"
  login.868081814.user:              "" => "tf"
  mem:                               "" => "2048"
  os_disk_size:                      "" => "20"
  os_disk_tier:                      "" => "maxiops"
  os_disk_uuid:                      "" => "<computed>"
  private_networking:                "" => "true"
  template:                          "" => "CentOS 7.0"
  title:                             "" => "<computed>"
  zone:                              "" => "fi-hel1"
upcloud_server.test: Still creating... (10s elapsed)
upcloud_server.test: Still creating... (20s elapsed)
upcloud_server.test: Still creating... (30s elapsed)
upcloud_server.test: Still creating... (40s elapsed)
upcloud_server.test: Creation complete after 46s (ID: <snip>)

Outputs:

ip = <SOME IP ADDRESS>
```

You can then log in to the server by running `ssh tf@<SOME IP ADDRESS>`. Check your SMS messages if you didn't specify any SSH keys.

## Modifying the plan

Next, increase memory config from 2 GB to 4 GB by changing resource
config to `mem = 4096`. Then run `terraform plan` again.


```
$ terraform plan
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  ~ upcloud_server.test
      mem: "2048" => "4096"
```

Then `terraform apply`.

```
$ terraform apply
upcloud_server.test: Refreshing state... (ID: <snip>)
upcloud_server.test: Modifying... (ID: <snip>)
  mem: "2048" => "4096"
upcloud_server.test: Still modifying... (ID: <snip>, 10s elapsed)
upcloud_server.test: Still modifying... (ID: <snip>, 20s elapsed)
upcloud_server.test: Still modifying... (ID: <snip>, 30s elapsed)
upcloud_server.test: Still modifying... (ID: <snip>, 40s elapsed)
upcloud_server.test: Modifications complete after 40s (ID: <snip>)

Outputs:

ip = <SOME IP ADDRESS>
```

Again, log in to the server and verify that memory has been increased.


## Cleaning up the environment

You can then destroy the instance by running `terraform destroy`. NOTE: You will lose all data.

```
$ terraform destroy
Terraform will perform the following actions:

  - upcloud_server.test

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

upcloud_server.test: Destroying... (ID: <snip>)
upcloud_server.test: Still destroying... (ID: <snip>, 10s elapsed)
upcloud_server.test: Destruction complete after 10s

Destroy complete! Resources: 1 destroyed.
```

You can then verify that `terraform.tfstate` does not include the test server anymore.
Also, you can log in to UpCloud control panel and see that the instance has been deleted.
