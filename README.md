This repo will build a fully functional kubernetes cloudfront stack

* Requirements
* Xcode CLI tools (installs GIT and other tools. Click install, not get Xcode)
```
xcode-select --install
```
* Homebrew
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
* Ansible
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