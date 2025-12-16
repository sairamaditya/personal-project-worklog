# Register steps that were used to install terraform on on amazon linux ec2 instance.


## Command sequence that were run

First command:

```
sudo yum update -y
sudo yum install -y yum-utils gnupg unzip
```


Second command:
```
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
```

Third commamnd:

```
sudo yum install terraform -y
```

Fourth command:

```
terraform --version
```
