<!-- TOC start (generated with https://github.com/derlin/bitdowntoc) -->
# Table of Contents 
- [Table of Contents](#table-of-contents)
- [CoalFire Proof-of-concept Azure environment using Terraform](#coalfire-proof-of-concept-azure-environment-using-terraform)
   * [Challenge Overview](#challenge-overview)
- [Solution Overview](#solution-overview)
   * [Introduction](#introduction)
   * [Architecture](#architecture)
   * [Architecture Diagram](#architecture-diagram)
   * [Deployment Steps](#deployment-steps)
   * [Assumptions](#assumptions)
   * [References](#references)
- [Terraform Deployment Instructions](#terraform-deployment-instructions)
   * [1. Prerequisites](#1-prerequisites)
   * [2. Initialize Terraform](#2-initialize-terraform)
      + [Troubleshooting:  ](#troubleshooting)
   * [3. Validate Configuration](#3-validate-configuration)
   * [4. Generate a Plan](#4-generate-a-plan)
   * [5. Apply Changes](#5-apply-changes)
   * [Technical Requirements](#technical-requirements)
      + [Network](#network)
      + [Compute](#compute)
      + [Supporting Infrastructure](#supporting-infrastructure)

<!-- TOC end -->

<!-- TOC --><a name="terraform-azurerm-webserver"></a>
# terraform-azurerm-webserver
Web server environment built using terraform  

This was a fun little challenge that I was given recently. I had loads of fun working on this and decided to keep it around and work on similar challenges. 

<!-- TOC --><a name="coalfire-proof-of-concept-azure-environment-using-terraform"></a>
# CoalFire Proof-of-concept Azure environment using Terraform
This was a proof of concept that I was given as a technical challenge. 

<!-- TOC --><a name="challenge-overview"></a>
## Challenge Overview

Create a proof-of-concept Azure environment using Terraform. The environment will host a basic web server with proper network segmentation and security controls. Use Coalfire’s open source terraform modules as much as possible in your solution ([Coalfire-CF repositories](https://github.com/orgs/Coalfire-CF/repositories?q=visibility:public+terraform-azure)). The use of Terraform modules is required, but it is not required to use ours.

Your challenge when submitted should be all within a public GitHub repository, with your code, a diagram that depicts your solution, and any notes you have from going through the challenge. The main repo README should document your solution, provide deployment steps, and your notes and commentary from going through the challenge. Any detail not provided in the scenario or requirements is up to your discretion.

<!-- TOC --><a name="solution-overview"></a>
# Solution Overview

<!-- TOC --><a name="introduction"></a>
## Introduction
This solution aims to create a proof-of-concept Azure environment using Terraform. The environment will host a basic web server with proper network segmentation and security controls. The solution leverages Coalfire’s open-source Terraform modules to ensure best practices and efficient resource management.

<!-- TOC --><a name="architecture"></a>
## Architecture
The architecture consists of the following components:
- **Virtual Network (VNet)**: A single VNet with multiple subnets to segregate different types of resources.
  - **Web Subnet**: Hosts the web servers.
  - **Application Subnet**: Hosts application servers.
  - **Database Subnet**: Hosts database servers.
  - **Management Subnet**: Hosts management and monitoring tools.
- **Network Security Groups (NSGs)**: Applied to each subnet to control inbound and outbound traffic.
- **Virtual Machines (VMs)**: Deployed in the web and management subnets.
  - **Web VMs**: Running Apache to serve web content.
  - **Management VM**: Used for administrative tasks and SSH access.
- **Load Balancer**: Distributes incoming web traffic across the web VMs.
- **Storage Account**: Used for storing Terraform state files and web logs.

<!-- TOC --><a name="architecture-diagram"></a>
## Architecture Diagram
![Architecture Diagram](/img/architecture-diagram.png)

<!-- TOC --><a name="deployment-steps"></a>
## Deployment Steps
1. **Initialize Terraform**
- Set up the working directory and download required provider plugins.
2. **Validate Configuration**
- Ensure the Terraform configuration files are syntactically correct.
3. **Generate a Plan**
- Create an execution plan to preview the changes Terraform will make to the infrastructure.
4. **Apply Changes**
- Execute the plan to create the resources in Azure.
5. **Verify Deployment**
- Confirm that all resources are created as expected and the web server is accessible.
- Log into each server


<!-- TOC --><a name="assumptions"></a>
## Assumptions
- The user has access to an Azure subscription with sufficient permissions to create the required resources.
- Terraform and Azure CLI are installed and configured on the user's machine.


<!-- TOC --><a name="references"></a>
## References
- [Coalfire-CF Terraform Modules](https://github.com/orgs/Coalfire-CF/repositories?q=visibility:public+terraform-azure)
- [Terraform Documentation](https://www.terraform.io/docs)
- [Azure Documentation](https://docs.microsoft.com/en-us/azure/)
- [Table of Contents Generator](https://github.com/derlin/bitdowntoc)
- [Quickstart: Use Terraform to create a Linux VM](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-terraform?tabs=azure-cli)
- [Mermaid Live Editor](https://mermaid.live/)  

<!-- TOC --><a name="terraform-deployment-instructions"></a>
# Terraform Deployment Instructions

These instructions provide a step-by-step guide for deploying infrastructure using Terraform.

---

<!-- TOC --><a name="1-prerequisites"></a>
## 1. Prerequisites

Before starting, ensure you have the following installed on your system:

- Terraform: Download and install from [terraform.io](https://developer.hashicorp.com/terraform/downloads)
- Cloud CLI Tools (e.g., Azure CLI, AWS CLI, GCP CLI) depending on your cloud provider.
- Code Editor (e.g., VS Code, IntelliJ, etc.)
- Access Credentials:
   - Azure Service Principal / AWS IAM User / GCP Service Account
   - Sufficient permissions for resource creation

---

<!-- TOC --><a name="2-initialize-terraform"></a>
## 2. Initialize Terraform
In the project directory, run:  

`
terraform init  
`

What Happens:
- Required provider plugins are downloaded.  
- Backend storage (if configured) is initialized.  
- Working directory is set up.  

<!-- TOC --><a name="troubleshooting"></a>
### Troubleshooting:  
Ensure backend storage (e.g., Azure Storage, AWS S3) is accessible.  
Verify authentication credentials.  

<!-- TOC --><a name="3-validate-configuration"></a>
## 3. Validate Configuration

To verify the configuration syntax and logic, run:  

`
terraform validate  
`

Expected Output:  

`
Success! The configuration is valid.  
`

If errors occur, address them before proceeding.

<!-- TOC --><a name="4-generate-a-plan"></a>
## 4. Generate a Plan
Preview changes Terraform will make:  

`  
terraform plan  
`  

What to Check:
- Ensure planned resource changes match your expectations.
- Look out for warnings or errors.  
  
<!-- TOC --><a name="5-apply-changes"></a>
## 5. Apply Changes
Deploy infrastructure using:
`  
terraform apply
`

Automated Approval (Not Recommended for Production):  
`  
terraform apply -auto-approve  
`  


<!-- TOC --><a name="technical-requirements"></a>
## Technical Requirements

<!-- TOC --><a name="network"></a>
### Network

- 1 VNet – 10.0.0.0/16
- 4 Subnets:
    - Application, Management, Backend, Web. All /24

<!-- TOC --><a name="compute"></a>
### Compute

- 2 Virtual Machines in an availability set running Linux (your choice of distro) in the web subnet
    - NSG allows SSH from management VM, allows web traffic from the Load Balancer. No external traffic
    - Script the installation of Apache
- 1 Virtual Machine running Linux (your choice of distro) in the Management subnet
    - NSG allows SSH from a single specific IP or network space only

<!-- TOC --><a name="supporting-infrastructure"></a>
### Supporting Infrastructure

- One storage account:
    - GRS Redundant
    - Only accessible to the VM in the Management subnet
    - One container “terraformstate”
    - One container “weblogs”
- One Load balancer that sends web traffic to the VMs in the availability set.
- One key vault
