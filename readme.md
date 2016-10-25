# Drupal Deployment in Cloudformation

## Guide Overview

In this lab guide, the necessary process is provided for deploying a cloudformation Drupal application. The architecture of the system features a [nested stack](https://aws.amazon.com/blogs/devops/use-nested-stacks-to-create-reusable-templates-and-support-role-specialization/) framework which builds a fault-tolerant mySQL RDS backend which powers the load balanced EC2 front-end. This guide covers the steps from prerequisites to cleanup.

### Prerequisites

1. PEM Key
2. Clone of this repository
3. AWS Permissions

## Deployment

### Establish DevOps workflow

* **Location Configuration** -
Locating template resources to the correct locations

   1. Clone repo - https://github.com/gd01rory/Drupal_Wordpress_RDS.git
   2. Create S3 bucket - **drupalstack**
   3. Add a folder called templates this bucket
   4. Copy json files to /templates directory
   5. IAM Permissions to enable read of json files

### Cloudformation

* **Quick Deploy**
Consider security precautions such as Drupal credentials and SSHLocation. Default credentials set to admin/password.

    1. <a href="https://console.aws.amazon.com/cloudformation/home#/stacks/new?stackName=ion&templateURL=https://s3.amazonaws.com/drupalstack/templates/root.json">Launch Stack</a>
    2. Select **Next** to proceed with the next step of the wizard.
    3. Review parameter groups
          DrupalSiteEMail - Change to Drupal admin's email address
          DrupalKeyName - Change PEM key from "enhance" to PEM created in pre-req
          DrupalSSHLocation - The IP of Drupal admin's location
    4. Select **Next** to proceed with the next step of the wizard.
    5. Select **Next** to skip the **Options** step of the wizard.
    6. Select **Create** to start the creation of the stack.
    7. Wait 20 mins until the stack reaches the state **CREATE_COMPLETE**

* **Custom Deploy** -
Each parameter is detailed for deployment

    1. **Create stack** in cloudformation console
    2. Specify an Amazon S3 template URL: https://s3.amazonaws.com/drupalstack/templates/root.json then select      **Next** to proceed through the wizard.
    3. Specify each parameter

          **Stack Name**
          1. Name of the cloudformation stack you will create

          **DevOps**
          1. TemplateBucket - S3 bucket where json files were cloned to

          **Application**
          1. DrupalInstanceType - Drupal EC2 instance size
          2. DrupalSiteAdmin - Drupal front end administrator username
          3. DrupalSiteEMail - Email address for Drupal administrator
          4. DrupalSiteName - Name of Drupal site
          5. DrupalSitePassword - Drupal site admin account password
          6. DrupalWebServerCapacity - Initial number of Drupal EC2 instances

          **Database**
          1. DrupalDBName - Drupal database name
          2. DrupalDBUsername - Database admin account username
          3. DrupalDBPassword - Database admin account password
          4. DrupalDBRootPassword - Database admin account root password
          5. DrupalDBClass - RDS instance class
          6. DrupalDBAllocatedStorage - Size of the database (Gb)
          7. DrupalMultiAZDatabase - Create a fault-tolerant, multi-AZ MySQL RDS database instance

          **Access**
          1. DrupalKeyName - Name of an existing EC2 KeyPair to enable SSH access to the instances
          2. DrupalSSHLocation - IP address range used to SSH into EC2 instances (/32 recommended)

    4. Specify a key/value pair for any tags, then select **Next** to proceed through the wizard.
    5. Finally, **Toggle Checkbox** indicating you "... acknowledge that AWS Cloudformation might create IAM resources with custom names."
    6. Select **Create** to deploy the stack
    7. Wait 20 mins until the stack reaches the state **CREATE_COMPLETE**

* **Test Deployment**

When each stack status reads **CREATE_COMPLETE** this means all of the AWS resources have deployed. Now, we can test the elastic load balancer to make sure it's sending requests to the load balanced EC2s. The root template called "ion" outputs a URL for us to accomplish this test. If you can login to the application front-end, the deployment test is successful.

**Process**

    1. **Select** Stack Name **ion** then select **Outputs** tab on lower pane.
    2. **Click** the **URL** for landing page
    3. Login to application using credentials

### Cleanup

This section provides a process for tearing down your Cloudformation environment. It can be accomplished through the AWS Cloudformation console.

    1. **Select** ion, **Click actions**, **Select** Delete Stack
    2. The root stack will begin a termination process and remove each stack subsequently.
    3. Remove json files (optional)

### Design considerations

The next phase of the design should include a Route53 mechanism for creation of a dynamic Cname via cloudformation parameters enabling more of a private customer SaaS like experience.

The template is structured into nested stacks for reasons of scalability. The main stack is responsible for parameter configuration and the DB stack is created for the connection of the Drupal stack. The elastic load balancer endpoint enables fault tolerance through instance health checks configured on http port 80 and this is indicative of our application's health. The RDS instance is deployed in a multi-AZ configuration which also provides redundancy in the event of a database tier failure. The architecture of the Drupal system will tolerate end-to-end failures at the ELB, EC2, and RDS layers.

![Drupal Architecture](https://s3.amazonaws.com/drupalstack/templates/diagram/drupal.png)
