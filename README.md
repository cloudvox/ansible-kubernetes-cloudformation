ansible-kubernetes-cloudformation
===
A group of playbooks required to build a CoreOS Kubernetes stack using Amazon Cloudformation. The intent is to have a reproducable, production capabale, pod & worker node auto-scaling docker deployment solution. All of the work on these playbooks have mirrored the steps taken by the CoreOS team with the awesome kube-aws tool, [documentation here](https://coreos.com/kubernetes/docs/latest/kubernetes-on-aws.html). The overall goal is to allow anisble aws shops to manage and deploy Kubernetes using the tools they already use for provisioning and configuration management, while offering a solid methodolgy for orchestrating services built on docker containers.

#### Why docker? 
Docker has revolutionized the ability to rapidly protoype and deploy isolated services in a complicated application stack. Shortening the time between development interations and increasing stability by limiting the scope of individual deployments, all while decreasing the resources and overhead other solutions like virtualization require. Find out more [here] (https://www.docker.com/enterprise)

#### Why Kubernetes?
Kubernetes is battle tested, having been developed by Google to manage and run their production workloads at scale. One of the more challenging parts of using Docker is that while development cycles are greatly reducted, the industry best practices for going to production are still limited. The challenges of managing an infrastructure platform so early in its life cycle at scale leaves much to be desired. Kubernetes bridges that gap by prodiving proven methods and best practices for managing docker containers, in a light weight portable package. Find out more [here] (https://github.com/kubernetes/kubernetes/wiki/Why-Kubernetes)

#### Why CloudFormation?
There are still a few major challenges left to be solved once you have decided on Docker and Kubernetes. While Horizontal pod (container group) autoscaling is a core part of Kubernetes, horizontal worker node auto-scaling is not part of the package yet. AWS CloudFormation and Auto-Scaling Groups provide the ability to scale the actual cloud hardware when load demands. Initial cluster, master and worker nodes require manual setup up, while not difficult this still leads to problems with replicating hosts or your entire stack when required. CloudFormations makes this task simple, provide a JSON document detailing your stack, and within minutes you have fully functional infrastructure. Find out more [here] (https://aws.amazon.com/cloudformation/)

#### Why Ansible?
Let me get the most important reason out of the way first, this is the tool our company has settled on for configuration management. Having used Chef and Puppet in the past, each has its own benefits and drawbacks, but in a nutshell, at least at our scale, not having to install a client, having easy to read and code configuration files, and ansible not having an opinion on the central collection of our playbooks make it an easy choice for us. Find out more [here] (https://www.ansible.com/how-ansible-works)

#### Why CoreOS?
While the world of linux containers is not a new one, Docker has brought this technology to the forefront. As the operations world migrates from heavy virtual machines to docker container based services, there are a list of new challenges when it comes to scaling an application across containers. CoreOS is an operating system designed entirely to run a docker container infrastructure, from the distributed etcd, and systemd to fleet, the entire OS is designed to allow distributed management of core services across a cluster of host machines. This comes with the enormous benefit of lowering the amount of patches required to sustain a more secure and consistent infrastruture. Find out more [here](https://coreos.com/using-coreos/)

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
aws-cli, boto (tool ansible uses to communicate with aws-cli), and cloudformation require the following variables to communicate with the various aws API's during setup. Information on where to obtain each of these varaibles can be found [here] (https://github.com/cloudvox/ansible-kubernetes-cloudformation/wiki/AWS-Credentials)
```
export AWS_ACCESS_KEY_ID=MY-ACCESS-KEY
export AWS_SECRET_ACCESS_KEY=MY-SECRET-KEY
export AWS_REGION=MY-AWS-REGION
export AWS_KEYPAIR=MY-AWS-KEYPAIR
export AWS_KMS_KEY=MY-AWS-ARN
```

To Be Continued


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
