# URLS
  https://jhooq.com/terraform-depends-on/ 



# Terraform


# Core Componenets
  - Executable 
  - Configuration Files
  - Provider Plugins
  - State data

# Terraform Object Types
   
   - Providers
   - Resources 
   - Data sources

# Terraform Steps

  - Init
    Initialize the backend
    Intialize the provider plugins 
  - Plan
  - Apply
  - Destroy

# Variables and Outputs 
  - Input Variables
  - Local Values
  - Output Values

# Input Variables
  - Input variables serve as parameters for a Terraform module, allowing aspects of the module to be customized without altering the module’s own source code 
  - Allow modules to be shared between different configurations.

# Locals
  - A local value assigns a name to an expression, allowing it to be used multiple times within a module without repeating it.
# Terraform Data Types
  - Primitive
    String, number , boolean
  - Collection
    List, Set, map
  - Structural
    Tuple, object

# State
  - Map real world resources to your configuration
  - Keeps track of metadata
  - Improve performance for large infrastructure 
  - The state is stored in local file terraformtf.state 
  - It can also be stored remotely, which works better in a team environment.

 # Backends
  - A “backend” in Terraform determines how state is loaded and how an operation such as apply is executed.  
  - This abstraction enables non-local file state storage, remote execution, etc.

# Resources
  - Resources are the most important element in the Terraform language. Each resource block describes one or more infrastructure objects, such as virtual  
    networks, compute instances, or higher-level components such as DNS records.
  
# Modules
  - A module is a container for multiple resources that are used together. Modules can be used to create lightweight abstractions, so that you can describe your
    infrastructure in terms of its architecture, rather than directly in terms of physical objects.
  - Modules are like packages in Terraform for code reusability

  - Two types of Modules
    1. Local Modules 
    2. External Module

# Output

  - Output values are like the return values of a Terraform module, and have several uses:

    1. A child module can use outputs to expose a subset of its resource attributes to a parent module.
    2. A root module can use outputs to print certain values in the CLI output after running terraform apply.
    3. When using remote state, root module outputs can be accessed by other configurations via a terraform_remote_state data source.

# Data
  - Data sources allow data to be fetched or computed for use elsewhere in Terraform configuration. 
  - Use of data sources allows a Terraform configuration to make use of information defined outside of Terraform, or defined by another separate Terraform 
    configuration.

# Import
  - Terraform is able to import existing infrastructure. 
  - This allows you take resources you’ve created by some other means and bring it under Terraform management.

# Format and Validate Terraform code
  - Format code per HCL canonical standard
    terraform fmt

  - Validate code for syntax 
    terraform validate 

  - Validate code skip backend validation
    terraform validate -backend=false 

# Init
  - Initialize your Terraform working directory

  - initialize directory, pull down providers
    terraform init 

  - initialize directory, do not download plugins
    terraform init -get-plugins=false 

  - initialize directory, do not verify plugins for Hashicorp signature
    terraform init -verify-plugins=false 

    terraform init -backend-config=PATH/TO/CONFIGURATION_FILE -reconfigure

    terraform init -input=false

# Plan  
  - Output the deployment plan to plan.out 
    terraform plan -out plan.out 

  - outputs a destroy plan
    terraform plan -destroy 

# Apply
  - Apply changes without being prompted to enter “yes”
    terraform apply --auto-approve

  - use the plan.out plan file to deploy infrastructure
    terraform apply plan.out
  
  - only apply/deploy changes to the targeted resource
    terraform apply -target=aws_instance.my_ec2
  
  - pass a variable via command-line while applying a configuration
    terraform apply -var my_region_variable=us-east-1

  - lock the state file so it can’t be modified by any other Terraform apply or modification action(possible only where backend allows locking)
    terraform apply -lock=true

  - do not reconcile state file with real-world resources(helpful with large complex deployments for saving deployment time)
    terraform apply refresh=false

  - number of simultaneous resource operations
    terraform apply --parallelism=5

# Get
  terraform get
  terraform get -update=true

# Misc
  - reconcile the state in Terraform state file with real-world resources
    terraform refresh

  - get information about providers used in current configuration
    terraform providers

    terraform version #display Terraform binary version, also warns if version is old
terraform get -update=true #download and update modules in the “root” module.

# terraform version
  terraform version

# Terraform Workspaces
  - Terraform Workspaces will help to manage same terraform configurations for different environments (eg: dev, staging, production) in the same project directory.

  - Check the workspace
    terraform workspace show

  - create a new workspace
    terraform workspace new mynewworkspace 

  - change to the selected workspace
    terraform workspace select default 

  - list out all workspaces
    terraform workspace list 

# Terraform State Manipulation
  - show details stored in Terraform state for the resource
    terraform state show aws_instance.my_ec2 

  - download and output terraform state to a file
    terraform state pull > terraform.tfstate 

  - move a resource tracked via state to different module
    terraform state mv aws_iam_role.my_ssm_role module.custom_module 

  - replace an existing provider with another
    terraform state replace-provider hashicorp/aws registry.custom.com/aws 

  - list out all the resources tracked via the current state file
    terraform state list 

  - unmanage a resource, delete it from Terraform state file
    terraform state rm aws_instance.myinstace 

    # List state
    $ terraform state list
    aws_iam_user.lb
    aws_instance.myec2

    # Show resource
    $ terraform state show aws_instance.myec2

    # Push terraform state to remote backend
    $ tarraform state push

    # Pull the remote terraform state to a local copy
    $ terraform state pull > terraform.tfstate

    # Update and tell terraform that packet_device.worker has been renamed to packet_device.helper
    $ terraform state mv packet_device.worker packet_device.helper

    # Move the resource block into the child module configuration
    $ terraform state mv packet_device.worker module.worker.packet_device.worker

    # Remove the resource from state but it will not remove the resource from cloud/provider.
    $ terraform state rm aws_instance.myec2


# Terraform Import And Outputs
  - terraform import aws_instance.new_ec2_instance i-abcd1234 #import EC2 instance with id i-abcd1234 into the Terraform resource named “new_ec2_instance” of type “aws_instance”
terraform import 'aws_instance.new_ec2_instance[0]' i-abcd1234 #same as above, imports a real-world resource into an instance of Terraform resource
terraform output #list all outputs as stated in code
terraform output instance_public_ip # list out a specific declared output
terraform output -json #list all outputs in JSON format

# Terraform Graph(Dependency Graphing)
terraform graph | dot -Tpng > graph.png #produce a PNG diagrams showing relationship and dependencies between Terraform resource in your configuration/code

# Terraform Taint/Untaint(mark/unmark resource for recreation -> delete and then recreate)
terraform taint aws_instance.my_ec2 #taints resource to be recreated on next apply
terraform untaint aws_instance.my_ec2 #Remove taint from a resource
terraform force-unlock LOCK_ID #forcefully unlock a locked state file, LOCK_ID provided when locking the State file beforehand

# Terraform Cloud
terraform login #obtain and save API token for Terraform cloud
terraform logout #Log out of Terraform Cloud, defaults to hostname app.terraform.io


Terraform Variable Assignment
You can pass variables to Terraform in different methods.

1. Environment variables – with a prefix TF_VAR_

$ export TF_VAR_instance_type=t2.micro
2. Command Line Flags

$ terraform plan -var="instancetype=t2.small"
3. From a variable file – use terraform.tfvars – terraform will load all variables from this file. If different var files to be used then,

$ terraform plan -var-file="custom.tfvars
4. Variable Defaults – can keep variable default in another .tf file.

$ cat variables.tf
variable "my_ip" {
default = "10.1.10.10/32"
}

# Terraform Basic Blocks
  - Terraform Block
    1. Special block used to configure some behaviour 
    2. Specifying a required Terraform CLI Version
    3. Specifying Provider Requirements & Versions
    4. Configure a Terraform Backend (Terraform State)

  - Provider Block
    1. Terrafrom relies on providers to interact with Remote Systems
    2. Declare Providers for Terraform to install providers and use them.
    3. Providers configurations belong to Root Module

  - Resource Block
    1. Each Resource blocks describes one or more Infrastructure Objects
    2. Resource Syntax: How to declare Resources
    3. Resource Behaviour: How terraform handles resource declarations?
    4. Provisioners: We can configure Resource post-creation actions

# Data Source
  - A data source represents a piece of read-only information that is fetched from the provider (in this case, AWS) every time you run Terraform.
  - Adding a data source to your Terraform configurations does not create anything new; it’s just a way to query the provider’s APIs for data and to make that 
    data available to the rest of your Terraform code.

# Null Resource
  - It does not provision any resurce 
  - It Does the followings (Use Cases)
    1. Execute Commands
    2. Run Shell Scripts
    3. Run Ansible Playbook
    4. Run Python Program
  - triggers 
    It trigger the provisioner when there is a change in the resource mentioned in the triggers 

# user_data
  - used to pass data to an instance at launch time
  - install some package into the VM/EC@ while provisioning
  - Execute some shell script in user_data
  - It is specified as a string in the Terraform configuration file and can contain any valid data that is base64-encoded when the instance launches.

# use cases of user_data
  - Installing software or packages: Installing software or packages on an instance at launch time. For example, you might use user_data to install a web server
    or database server when an instance is launched.
  - Configuring the instance: Configuring the instance to run a specific application or service. For example, you might use user_data to configure an instance to 
    run a web application.
  - Setting the hostname or other instance metadata: Setting the hostname or other instance metadata. This can be useful for identifying instances in a cluster or
    for customizing the instance's environment.
  - Configuring the instance: Configuring the instance to communicate with other resources, such as an S3 bucket or a database.
  - Automating the setup: Automating the setup of an instance when it is launched, rather than manually configuring it after it has been launched. This can save
    time and effort when launching multiple instances.

# Terrform Import
  - Import manually created resources into Terraform code
  - Syntax: terraform import <RESOURCE_TYPE>.<RESOURCE_NAME> <RESOURCE_ID>

# Terraform Template 

# Terraform Debug
  - Log Levels,  Debug, Trace, Info, Warn
  - Set Environment Variables
    1. TF_LOG = DEBUG/TRACE/INFO/WARN
    2. TF_LOG_PATH = <LOG_FILE_PATH>

    3. Include Validation

# Terragrunt
  - Remove code duplicacy across project environments

# Terraform State luck
  - Use LuckId as index key in dynamoDB 

# Terraform state file
  - Local Terrafrom State File
  - Remote Terrafrom State File

# Terraform Provisioners
  1. file: Copy a file from local machine to the cloud destination (Ec2, VM)
  2. loca-exec: Execute bash command in local machine, Create a file in the local machine
  3. remote-exec: Execute bash command in remote (cloud) machine

# Terraform depends_on

  1. Used to specify dependencies between resources within a Terraform configuration.
  2. Allows Terraform to properly order the creation, modification, and destruction of resources.
  3. Ensuring that dependent resources are created or modified only after the resources they depend on have been created or modified