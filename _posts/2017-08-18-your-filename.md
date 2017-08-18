---
published: false
---
## A New Post

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.





#!/bin/bash

if [ $# -lt 7 ]; then
 echo "Usage: $0 <DockerFileDirectory> <file://BatchCloudformation.json> <Stackname> <Securitygroup> <Subnets> <Keypair> <ECSoptimizedAMIid>"
 exit
fi

dockerFilePath="$1"  #"/home/centos/Dockerfolder/    #Dockerfile should reside inside the directory
batchCfnPath="$2"    #"file://Scripts/batchcloudformation.json"

stackName="$3"      
secGroups="$4"      
subnets="$5"
keyName="$6"       
imageId="$7"        

# Below variables can also be sent as arguments

# 2 Compute Environment variables
ODMinvcpu=0
ODDesiredvcpu=0
ODMaxvcpu=256
SpotMinvcpu=0
SpotDesiredvcpu=0
SpotMaxvcpu=256
Instancetype="optimal"    # or comma separated like "\"r4.xlarge,m4.xlarge,r4.16xlarge\""
Bidpercent=60

#Job definition variables
Jobdefvcpu=1
Jobdefmemory=4000
Jobdefattempts=8

# job queue variables
NormalQpriority=100
LowpriorityQpriority=100

# Creates the cloudformation stack
aws cloudformation create-stack --stack-name $stackName --template-body $batchCfnPath --parameters ParameterKey=Subnet,ParameterValue=$subnets ParameterKey=SecurityGroup,ParameterValue=$secGroups ParameterKey=ODMinvcpu,ParameterValue=$ODMinvcpu ParameterKey=ODDesiredvcpu,ParameterValue=$ODDesiredvcpu ParameterKey=ODMaxvcpu,ParameterValue=$ODMaxvcpu ParameterKey=SpotMinvcpu,ParameterValue=$SpotMinvcpu ParameterKey=SpotDesiredvcpu,ParameterValue=$SpotDesiredvcpu ParameterKey=SpotMaxvcpu,ParameterValue=$SpotMaxvcpu ParameterKey=ImageId,ParameterValue=$imageId ParameterKey=Ec2keypair,ParameterValue=$keyName ParameterKey=Instancetype,ParameterValue=$Instancetype ParameterKey=Bidpercent,ParameterValue=$Bidpercent ParameterKey=Jobdefvcpu,ParameterValue=$Jobdefvcpu ParameterKey=Jobdefmemory,ParameterValue=$Jobdefmemory ParameterKey=Jobdefattempts,ParameterValue=$Jobdefattempts ParameterKey=NormalQpriority,ParameterValue=$NormalQpriority ParameterKey=LowpriorityQpriority,ParameterValue=$LowpriorityQpriority --capabilities CAPABILITY_IAM

#Wait till the stack creation is completed
waitstatus=`aws cloudformation wait stack-create-complete --stack-name $stackName`

ondemandCeARN=`aws cloudformation describe-stacks --stack-name $stackName --query "Stacks[0].Outputs[?OutputKey=='OndemandComputeEnvironmentArn'].OutputValue" --output text`
spotCeARN=`aws cloudformation describe-stacks --stack-name $stackName --query "Stacks[0].Outputs[?OutputKey=='SpotComputeEnvironmentArn'].OutputValue" --output text`
NormalQueueArn=`aws cloudformation describe-stacks --stack-name $stackName --query "Stacks[0].Outputs[?OutputKey=='NormalQueueArn'].OutputValue" --output text`
LowPriorityQueueArn=`aws cloudformation describe-stacks --stack-name $stackName --query "Stacks[0].Outputs[?OutputKey=='LowPriorityQueueArn'].OutputValue" --output text`
JobDefinitionArn=`aws cloudformation describe-stacks --stack-name $stackName --query "Stacks[0].Outputs[?OutputKey=='JobDefinition'].OutputValue" --output text`
ECSRepositoryURI=`aws cloudformation describe-stacks --stack-name $stackName --query "Stacks[0].Outputs[?OutputKey=='ECSRepositoryURI'].OutputValue" --output text`

# Build docker file and push to ECR
dockerlogin=`aws ecr get-login --no-include-email`
$dockerlogin
build=`docker build -t $ECSRepositoryURI $dockerFilePath`
docker push $ECSRepositoryURI

# Output of $JobDefinitionArn $NormalQueueArn and $LowPriorityQueueArn shoud be used to submit the jobs
echo "JobDefinitionArn: $JobDefinitionArn"
echo "LowPriorityQueueArn: $LowPriorityQueueArn"
echo "NormalQueueArn: $NormalQueueArn"
echo "Repository URI: $ECSRepositoryURI"