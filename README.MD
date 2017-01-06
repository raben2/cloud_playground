# Deploy cojure to the cloud
============================

This repository deployes an example application to any cloud provider  using the ansible board tools and apache-libcloud

[your repository](https://github.com/ThoughtWorksInc/infra-problem) is used for the deployment. The build happends with each run.

### AWS is yet not implemented but easy to extend


## Secure your credentials

Create a file .secrets/credentials.yaml and encrypt it with ansible [vault](http://docs.ansible.com/ansible/playbooks_vault.html), containing the following:

```
gcloud_service_account: ${youraccount@google.com}
gcloud_project_id: ${your-project}
gcloud_credentials_json: |
  {
  ${google json goes here}
  }

application_newsfeed_token: ${application-secrets}
````

## Creating a stack

`
ansible-playbook ./boot.yaml --ask-vault-pass
`

The Playbook contains three plays in which the following steps are executed:

+ install dependencies
+ create a gce network
+ update the firewall
+ launch the instances
	- 2 frontend instances
	- 1 backend instance
+ create a gce loadbalancer for the frontend
+ cleanup the decrypted credentials
+ generate a hosts file

## Shutting down

` 
ansible-playbook ./destroy.yaml --ask-vault-pass
`

will shutdown the stack and all resources.
You might want to consider taking images before shutting down.

## Setup and provisioning

`
ansible-playbook -i hosts -l deploy --ask-vault-pass
`

All hosts will be in the deploy group, also a frontend group will be created. you can now start rolling out different roles or playbooks by seting the prefered limit.


## Notes 

* In due to the lb check interval of 1 min it can take a while before the application will be available
* Since the variables of each application differ strongly i recommend to use a seperate group_vars file in each case.
* Currently only CentOS 7 instances can be used, but the setup mechanism can simply be altered to other distributions 
