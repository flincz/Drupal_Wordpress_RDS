# Drupal Deployment in Cloudformation

## Guide Overview

In this lab guide, the necessary process is provided for deploying a cloudformation Drupal application. The architecture of the system features a [nested stack](https://aws.amazon.com/blogs/devops/use-nested-stacks-to-create-reusable-templates-and-support-role-specialization/) framework, which builds a fault-tolerant mySQL RDS backend, and powers a load balanced EC2 front-end. This guide cover the steps from prerequisites to cleanup.

### Prerequisites

-PEM Key
-Clone of this repository
-AWS Permissions

## Deployment

### Establish DevOps space

   1. Clone REPO-NAME
   2. Create a bucket - drupalstack
   3. Add a folder called templates this bucket
   4. Copy json files to /templates directory
   5. Assure permissions enable you to read json files

## Cloudformation

* **Quick Install** Consider security precautions such as Drupal credentials and SSHLocation. Default credentials set to admin/password.

    1. <a href="https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=ion&templateURL=https://s3.amazonaws.com/drupalstack/templates/root.json">Launch Stack</a>
    2. Click **Next** to proceed with the next step of the wizard.
    3. Review parameter groups
          DrupalSiteEMail - Change to Drupal admin's email address
          DrupalKeyName - Change PEM key from "enhance" to PEM created in pre-req
          DrupalSSHLocation - The IP of Drupal admin's location
    4. Click **Next** to proceed with the next step of the wizard.
    5. Click **Next** to skip the **Options** step of the wizard.
    6. Click **Create** to start the creation of the stack.
    7. Wait until the stack reaches the state **CREATE_COMPLETE**
