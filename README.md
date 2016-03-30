ansible-kubernetes-cloudformation
===
The repo represents the playbooks required to build a CoreOS Kubernetes stack using Amazon Cloudformation. The intent is to have a reproducable, production capabale, pod & worker node auto-scaling deployment solution. All of the work on these playbooks have mirrored the steps taken by the CoreOS team with the awesome kube-aws tool, [documentation here](https://coreos.com/kubernetes/docs/latest/kubernetes-on-aws.html). The overall goal is to allow anisble configured aws shops to manage and deploy Kubernetes using the tools they already use for orchcestration and configuration management.

Please note, I have not yet decided how to handle the kube assets folder, the created kubecontrol is required to manage the stack with kubectl. As it stands today the assets are created as a subfolder with the PKI credentials gitignored. Considering each stack would in essense have its own folder with no secure or private information, aside from stack name and external DNS name, in theory they could just be commited with your fork of this repo.

### Current Status: PKI created, working on userdata.

#### Requirements for OSX workstation
* Xcode CLI tools: [Instructions] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/Install-xcode-cli-tools)
* Homebrew: [Instructions] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/Install-Homebrew)
* Ansible: [Instructions] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/Install-xcode-cli-tools)



```
brew install ansible
```
* Setup AWS credentials
```
export AWS_ACCESS_KEY_ID=MY-ACCESS-KEY
export AWS_SECRET_ACCESS_KEY=MY-SECRET-KEY
export AWS_REGION=MY-AWS-REGION
```
* Complete bootstrap with ansible
```
ansible-playbook bootstrap.yml -K
```
* Generate kube-aws AWS KMS Key (if you already have a kube-aws KMS key created, you can skip this step)
```
aws kms --region=$AWS_REGION create-key --description="kube-aws assets"
aws kms create-alias --alias-name alias/kube-aws --target-key-id MY-AWS-KMS-ARN-FROM-ABOVE-COMMAND
```
* Setup KMS Key (if you used the step above, this is the same value used in --target-key-id)
```
export AWS_KMS_KEY=MY-AWS-ARN
```
* Generate an AWS EC2 Key Pair (if you already have a keypair you would like to use, you can skip this step)
[Amazon Keypair documentation](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)
* Setup Keypair name
```
export AWS_KEYPAIR=MY-AWS-KEYPAIR
```
* Generate cluster.yaml
```
kube-aws init --cluster-name=my-cluster-name \
--external-dns-name=my-cluster-endpoint \
--region=$AWS_REGION \
--availability-zone=${AWS_REGION}c \
--key-name=$AWS_KEYPAIR \
--kms-key-arn="$AWS_KMS_KEY"
```
