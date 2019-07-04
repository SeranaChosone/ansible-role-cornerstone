Role Name
=========

> The Cornerstone is the first stone set in the construction of a masonry foundation, important since all other stones will be set in reference to this stone, thus determining the position of the entire structure.

Cornerstone is a simple ansible role to create a single Virtual Machine on supported Cloud providers.

Requirements
------------

As Cornerstone creates resources within Cloud Providers, it depends on appropriate SDKs for each provider to be installed.

Role Variables
--------------

Available variables are listed below, along with default values (see defaults/main.yml):

    # Platform you wish to deploy a Cornerstone VM onto
    # Currently supports aws, azure
    cornerstone_platform: aws

    # Prefix - What prefix for objects to use. Eg myenv, myapp, etc
    cornerstone_prefix: cornerstone
   
    # Tags - Tags that can be used.
    cornerstone_tag_purpose: "Openshift"
    cornerstone_tag_role: "Master"    
 
    # Cornerstone VM Name
    cornerstone_vm_name: "{{ cornerstone_prefix }}-vm"

    # SSH Credentials
    cornerstone_ssh_admin_username: rhadmin
    cornerstone_ssh_admin_pubkey: <add_your_ssh_public_key_data>
    # The AWS SSH Key to use. Must be pre-created in AWS
    cornerstone_aws_ssh_key_name: mykey
    
    # API Credentials
    # What Boto profile to use for AWS
    cornerstone_aws_profile: myprofile

    # Platform Location/Region
    # Which location/region do you wish to use?
    # Currently supports short versions of aws/azure regions eg uksouth (azure), eu-west-2 (aws)
    cornerstone_location: uksouth

    # Azure Resource Group
    cornerstone_azure_resource_group: "{{ cornerstone_prefix }}-rg"

    # Common Network Objects
    cornerstone_virtual_network_name: "{{ cornerstone_prefix }}vnet"
    cornerstone_virtual_network_cidr: "10.1.0.0/16"
    cornerstone_subnet_name: "{{ cornerstone_prefix }}subnet"

    # Azure Specific Network Objects
    cornerstone_azure_subnet_cidr: "10.1.0.0/20"

    # AWS Specific Network Objects
    cornerstone_aws_subnet_cidr_list:
      - "10.1.0.0/20"
      - "10.1.16.0/20"
      - "10.1.32.0/20"

    # Private or Public IP
    cornerstone_vm_assign_public_ip: true
    cornerstone_public_private_ip: public
    cornerstone_publicip_allocation_method: Dynamic
    cornerstone_publicip_domain_name: null

    # Virtual Machine
    cornerstone_vm_flavour: Standard_D2s_v3
    # AWS AMI Image ID
    cornerstone_vm_aws_ami: ami-12345678
    # Azure Image
    cornerstone_vm_image_offer: RHEL
    cornerstone_vm_image_publisher: RedHat
    cornerstone_vm_image_sku: 7-RAW
    cornerstone_vm_image_version: latest

    # Disks
    # Type of managed disk (AWS)
    cornerstone_aws_vm_disk_managed: gp2
    # Type of managed disk (Azure)
    cornerstone_azure_vm_disk_managed: Standard_LRS
    # Base OS Disk Size (Standard AWS base is 10GB, Azure is 32GB)
    cornerstone_vm_os_disk_size: 32
    # Do we want a separate data disk?
    cornerstone_vm_data_disk: false
    
    # How large?
    #### IMPORTANT #####################################################
    # AWS ec2_instance module for ansible 2.7+ currently has a problem #
    # with converting strings to ints for disk size.                   #
    # To resolve add the code mentioned in                             #
    # https://github.com/ansible/ansible/pull/55716/files              #
    ####################################################################

    cornerstone_vm_data_disk_size: 64

Dependencies
------------

Not a dependency per-se, but used alongside Finial, can subscribe RHEL systems to the Red Hat Network.

For AWS deployments the aws_cli is required. Role for this can be found at https://github.com/kenhitchcock/ansible-role-awscli

Example Playbook
----------------

# For and AWS deployment this will work for multiple instances.
    - hosts: localhost
      vars:
        guests:
          aws-instance01:
            cs_platform: aws
            cs_tag_purpose: "Openshift"
            cs_tag_role: "Master"
            cs_vm_name: aws-instance01
            cs_vm_state: present
            cs_location: eu-west-2
            cs_vm_aws_az: eu-west-2a
            cs_vm_flavour: t2.micro
            cs_vm_aws_ami: ami-051fb39c3a16c8a85
            cs_virtual_network_name: "{{ cornerstone_prefix }}vnet"
            cs_virtual_network_cidr: "10.1.0.0/16"
            cs_subnet_name: "{{ cornerstone_prefix }}subnet"
            cs_public_private_ip: public
            cs_vm_assign_public_ip: true
            cs_publicip_allocation_method: Dynamic
            cs_publicip_domain_name: null
            cs_vm_data_disk: true
            cs_vm_data_disk_device_name: "/dev/sdb"
            cs_vm_data_disk_size: "15"
          aws-instance02:
            cs_platform: aws
            cs_tag_purpose: "Openshift"
            cs_tag_role: "Master"
            cs_vm_name: aws-instance02
            cs_vm_state: present
            cs_location: eu-west-2
            cs_vm_aws_az: eu-west-2b
            cs_vm_flavour: t2.micro
            cs_vm_aws_ami: ami-051fb39c3a16c8a85
            cs_virtual_network_name: "{{ cornerstone_prefix }}vnet"
            cs_virtual_network_cidr: "10.1.0.0/16"
            cs_subnet_name: "{{ cornerstone_prefix }}subnet"
            cs_public_private_ip: public
            cs_vm_assign_public_ip: true
            cs_publicip_allocation_method: Dynamic
            cs_publicip_domain_name: null
            cs_vm_data_disk: true
            cs_vm_data_disk_device_name: "/dev/sdb"
            cs_vm_data_disk_size: "15"

      roles:
         - role: danhawker.cornerstone

# For Azure you cannot use the guest layout yet. The task will only create one instance at a time.

Future Releases
---------------

 - Add support for Libvirt
 - Add support for VSphere
 - Add support for RHEV
 - Add support for OpenStack

License
-------

MIT

Author Information
------------------

Dan Hawker [Github](https://github.com/danhawker)
