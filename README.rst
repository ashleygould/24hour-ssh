24hour-ssh
==========

This project provides tools to permit users temporary ssh access to AWS EC2 instances.


Assumptions for this project
----------------------------

- language: python 3.6
- libs: boto3
- testing: pytest, moto
- documentation: ReSTructuredText, Sphinx
- builds: travisci


Stakeholders
------------

- Deborah Samarov
- David Brunnet
- Mike Spitalieri

Users
-----

check spelling

- Boris Grib...
- Mahtaj Khamneian
- Rajesh Sharma
- Robert Mijango
- Pari Khodayari
- Salman Mohammad
- Nirmalsit Singh
- Venkatesh Mutyala


Requirments per Deborah
-----------------------

- Only support queue users allowed to launch 24hour-ssh script
- Authentication/authorization vi AD or IAM credentials
- MFA required
- Script prompts for:
  - user name
  - target host
  - SN INC or REQ Id
  - reason for access request
- After access, the target instance is scheduled for recycle
- Access request events and actions logged 
- Access request events trigger email notification to ucpathinfrastructure@ucop.edu 
- Monthy Audit report generated

Questions about Requirements
----------------------------

- who do audit reports get sent to?
- what is content of audit report?
- should access rights be limited once authenticated to target instance?





Release Plan
------------


Release 1
---------

actions limited to ashley-training, ucpathops-poc

- user granted IAM permissions to create cloud9 instances
- user creates their own cloud9 instance in target vpc/sg
- user installs 24hour-ssh tool onto cloud9 with pip
- user runs script from cloud9 instance
- script prompts for target host
- script generates temporary ssh keypair
- script calls ssm service to install generated ssh public key into ~ec2-user/.ssh/authorizedkeys file
- script deletes temporary ssh keypair from cloud9 instance after 24 hours

Release 2
---------

actions limited to ashley-training, ucpathops-poc

- create log bucket
- create cloudwatch stream
- script logs activity to cloudwatch
- script posts sns notice to specified email addresses
- script launches lambda task to recycle target instance in 24 hours
- script prompts for alternative ssh public key
- script uploads alternative ssh public key into cloud9 
- log ssh key fingerprint

Release 3
---------

- audit reporting

Technical Questions
-------------------

- how to manage authorized key entries?
- list of target hosts? 
- how does SSM work? 

Tasks for Neel:
---------------

- cfn template to build one s3 bucket, one ec2 instance.
- setup pytest,moto tests for ec2 functions - tests must pass
- setup travis-ci autobuild to run tests
- make cfn template to build 3 ec2 instances with tags: {key = 24hourssh, value = enabled}
  these must be in the same ec2 security group as your cloud9 instance
- play with SSM from console. figure out about runCommand
- Python script '24hour-ssh.py' with tests



Tasks for Ashley:
-----------------

- user granted IAM permissions 
- README documentation for users
- study Amazon Service Manager (SSM)
- create basic usage tests for ssm



Project Layout
--------------
- ./.travis.yml
- ./setup.py

#tests 

- ./test/__init.py
- ./test/fixtures
- ./test/fixtures/id_rsa.pub
- ./test/test_ec2ssh.py
- ./test/cli/test_sudo_access.py
- ./test/cli/__init__.py

#ec2ssh

- ./ec2ssh/cli
- ./ec2ssh/cli/
- ./ec2ssh/cli/sudo_access.py
- ./ec2ssh/cli/__init__.py
- ./ec2ssh/ssm.py
- ./ec2ssh/__init__.py

#samples

- ./samples/test_ssm.py
- ./samples/test_s3.py
- ./samples/test_ec2.py
- ./samples/ssm.py

#cloudformation

- ./cfn
- ./cfn/cfn-resources
- ./cfn/cfn-resources/initializer.yaml


Installation
------------

git clone https://github.com/ucop-tds/24hour-ssh.git

# assume python venv
pip install -e 24hour-ssh

# as ec2-user but not python venv
pip install --user -e 24hour-ssh


import ec2ssh

