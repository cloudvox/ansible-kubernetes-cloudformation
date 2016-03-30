ansible-kubernetes-cloudformation
===
A group of playbooks required to build a CoreOS Kubernetes stack using Amazon Cloudformation. The intent is to have a reproducable, production capabale, pod & worker node auto-scaling docker deployment solution. All of the work on these playbooks have mirrored the steps taken by the CoreOS team with the awesome kube-aws tool, [documentation here](https://coreos.com/kubernetes/docs/latest/kubernetes-on-aws.html). The overall goal is to allow anisble aws shops to manage and deploy Kubernetes using the tools they already use for orchcestration and configuration management.

#### Why docker? 
Docker has revolutionized the ability to rapidly protoype and deploy isolated services in a complicated application stack. Shortening the time between development interations and increasing stability by limiting the scope of individual deployments, all while decreasing the resources and overhead other solutions like virtualization require. Find out more [here] (https://www.docker.com/enterprise)

#### Why Kubernetes?
Kubernetes is battle tested, having been developed by Google to manage and run their production workloads at scale. One of the more challenging parts of use Docker is that while development cycles are greatly impacted, the industry best practices for going to production are still limited. The challenges of managing a product so early in its life cycle at scale leaves much to be desired, Kubernetes bridges that gap by prodiving proven methods and best practices for managing docker containers, in a light weight portable package. Find out more [here] (https://github.com/kubernetes/kubernetes/wiki/Why-Kubernetes)

#### Why CloudFormation?
There are still a few major challenges left to be solved once you have decided on Kubernetes. Horizontal worker node auto-scaling is not part of the package yet, the ability to scale the actual cloud hardware when load demands is handled very well by AWS CloudFormation and Auto-Scaling Groups. Initial cluster, master and worker nodes require manual setup up, while not difficult this still leads to problems with replicating hosts or your entire stack when required. CloudFormations makes this task simple, provide a JSON document detailing your stack, and within minutes you have fully functional infrastructure. Find out more [here] (https://aws.amazon.com/cloudformation/)

#### Why Ansible?
Let me get the most important reason out of the way first, this is the tool our company has settled on for configuration management. Having used Chef and Puppet in the past, each has its own benefits and drawbacks, but in a nutshell, at least at our scale, not having to install a client, having easy to read and code configuration files, and ansible not having an opinion on the central collection of our playbooks make it an easy choice for us. Find out more [here] (https://www.ansible.com/how-ansible-works)

#### Out of curiosity, why not ECS (Amazon EC2 Container Service)
I will just make a quick note here, I deffinitly explored the option of using ECS, with the ECS service being so new there are multiple gaps in the technology. In general the service does not approach scalability from a container (service) prospective, there are no provisions to scale particular containers across your nodes in a distributed fashion. While it does have auto-scaling of docker nodes based on CPU/Mem etc, not having fine grained control over the automation of a docker service limits its capacity for production use.

Please note, I have not yet decided how to handle the kube assets folder, the created kubecontrol is required to manage the stack with kubectl. As it stands today the assets are created as a subfolder with the PKI credentials gitignored. Considering each stack would in essense have its own folder with no secure or private information, aside from stack name and external DNS name, in theory they could just be commited with your fork of this repo.

### Current Status: PKI created, working on userdata.

#### Pre-Requirements for OSX workstation
* Xcode CLI tools: [Instructions] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/Install-xcode-cli-tools)
* Homebrew: [Instructions] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/Install-Homebrew)
* Ansible: [Instructions] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/Install-ansible)


#### Install aws-cli and boto (required by ansible cloud modules to communicate with the variable aws api's)
```
ansible-playbook bootstrap_osx.yml -K
```

#### Setup AWS credentials
aws-cli, boto (tool ansible uses to communicate with aws-cli), and cloudformation require the following variables to communicate with the various aws API's during setup
Information on where to obtain each of these varaibles can be found [here] ()
```
export AWS_ACCESS_KEY_ID=MY-ACCESS-KEY
export AWS_SECRET_ACCESS_KEY=MY-SECRET-KEY
export AWS_REGION=MY-AWS-REGION
export AWS_KMS_KEY=MY-AWS-ARN
```

* Generate kube-aws AWS KMS Key (if you already have a kube-aws KMS key created, you can skip this step)
```
aws kms --region=$AWS_REGION create-key --description="kube-aws assets"
aws kms create-alias --alias-name alias/kube-aws --target-key-id MY-AWS-KMS-ARN-FROM-ABOVE-COMMAND
```
* Setup KMS Key (if you used the step above, this is the same value used in --target-key-id)
```

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
