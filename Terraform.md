# Terraform

## Commands

| Command              | What it does                                                                                                                                   |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| Terraform init       | initialize a working directory, dl the necessary provider plugins and modules, set up backend for storing infra state                          |
| Terraform plan       | create a plan = a set of changes to make the resources match the config => preview the changes Terraform will make to match your configuration |
| Terraform apply      | apply the planned changes (shows the delta then asks for confirmation)                                                                         |
| Terraform fmt        | format code (linting)                                                                                                                          |
| Terraform validate   | validate the syntax of the configuration and correct usage of attributes and values                                                            |
| Terraform show       | inspect the current state (see actual values associated with the resource group)                                                               |
| Terraform state      | review the information in the state file (terraform.tfstate)                                                                                   |
| Terraform state list | see a list of the resources created with Terraform (terraform.tfstate)                                                                         |

## Project structure and vocab

* Provider = plugin that Terraform uses to create and manage resources (ex: azurerm)
* Resource = component of the infrastructure -> has a type and a name

---

* Variables.ts -> store redundant values/avoid hard-coding in templates
* Outputs.ts -> choose which data are output when applying changes (can also be queried with terraform output) -> avoids useless outputs when much info in a configuration

---

  Assign values to root module variables:

* .tfvars (terraform apply -var-file="xxx.tfvars")
* command line - -var="name=value" on terraform plan/apply
* environment variables

---

Backend = where terraform stores its state -> .tfbackend = ref to the place where it's stored if remote
