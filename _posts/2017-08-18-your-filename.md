---
published: false
---
## A New Post

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.

{
	"Parameters": {
		"Subnet": {
			"Type": "CommaDelimitedList",
			"Description": "Comma separated subnet ID of an existing subnets"
		},
		"SecurityGroup": {
			"Type": "CommaDelimitedList",
			"Description": "Comma separated SecurityGroup ID of an existing SecurityGroup"
		},
		"ODMinvcpu": {
			"Type": "Number",
			"Description": "Minimum vcpu"
		},
		"ODDesiredvcpu": {
			"Type": "Number",
			"Description": "Desired vcpu"
		},
		"ODMaxvcpu": {
			"Type": "Number",
			"Description": "Max vcpu"
		},
		"SpotMinvcpu": {
			"Type": "Number",
			"Description": "Minimum vcpu for spot"
		},
		"SpotDesiredvcpu": {
			"Type": "Number",
			"Description": "Desired vcpu for spot"
		},
		"SpotMaxvcpu": {
			"Type": "Number",
			"Description": "Max vcpu for spot"
		},
		"ImageId": {
			"Type": "String",
			"Description": "ECS optimized instances AMI ID"
		},
		"Ec2keypair": {
			"Type": "AWS::EC2::KeyPair::KeyName",
			"Description": "Key pair which will be used by the compute environment"
		},
		"Instancetype": {
			"Type": "CommaDelimitedList",
			"Description": "Required instance type of compute environment"
		},
		"Bidpercent": {
			"Type": "String",
			"Description": "Spot instance bid percentage"
		},
		"Jobdefvcpu": {
			"Type": "Number",
			"Description": "vCPU value of the job defintion"
		},
		"Jobdefmemory": {
			"Type": "Number",
			"Description": "Memory value of the job definition"
		},
		"Jobdefattempts": {
			"Type": "Number",
			"Description": "Attempts value of the job definition"
		},
		"NormalQpriority": {
			"Type": "Number",
			"Description": "Priority value of the Normal priority Queue"
		},
		"LowpriorityQpriority": {
			"Type": "Number",
			"Description": "Priority value of the low priority Queue"
		}
  },
	"Resources": {
		"AWSBatchServiceRole": {
			"Type": "AWS::IAM::Role",
			"Properties": {
				"AssumeRolePolicyDocument": {
					"Version": "2012-10-17",
					"Statement": [{
						"Effect": "Allow",
						"Principal": {
							"Service": "batch.amazonaws.com"
						},
						"Action": "sts:AssumeRole"
					}]
				},
				"ManagedPolicyArns": [
					"arn:aws:iam::aws:policy/service-role/AWSBatchServiceRole"
				]
			}
		},
		"ecsInstanceRoleProfile": {
      "Type": "AWS::IAM::InstanceProfile",
      "Properties": {
        "Roles": [
          { "Ref": "ecsInstanceRole" }
        ]
      }
    },
		"ecsInstanceRole": {
			"Type": "AWS::IAM::Role",
			"Properties": {
				"AssumeRolePolicyDocument": {
					"Version": "2008-10-17",
					"Statement": [{
						"Sid": "",
						"Effect": "Allow",
						"Principal": {
							"Service": "ec2.amazonaws.com"
						},
						"Action": "sts:AssumeRole"
					}]
				},
				"ManagedPolicyArns": [
					"arn:aws:iam::aws:policy/service-role/AmazonEC2ContainerServiceforEC2Role"
				]
			}
		},
		"spotFleetRole": {
			"Type": "AWS::IAM::Role",
			"Properties": {
				"AssumeRolePolicyDocument": {
					"Version": "2012-10-17",
					"Statement": [{
						"Effect": "Allow",
						"Principal": {
							"Service": [
								"spotfleet.amazonaws.com"
							]
						},
						"Action": [
							"sts:AssumeRole"
						]
					}]
				},
				"ManagedPolicyArns": [
					"arn:aws:iam::aws:policy/service-role/AmazonEC2SpotFleetRole"
				]
			}
		},
		"ECSRepository": {
			"Type": "AWS::ECR::Repository",
			"Properties": {	}
		},				
		"OndemandComputeEnvironment": {
			"Type": "AWS::Batch::ComputeEnvironment",
			"DependsOn": [
				"ecsInstanceRoleProfile",
				"AWSBatchServiceRole"
			],
			"Properties": {
				"Type": "MANAGED",
				"ComputeResources": {
					"Type": "EC2",
					"Ec2KeyPair":{
						"Ref": "Ec2keypair"
					} ,
					"ImageId": {
						"Ref": "ImageId"
					},
					"MinvCpus": {
						"Ref": "ODMinvcpu"
					},
					"DesiredvCpus": {
						"Ref": "ODDesiredvcpu"
					},
					"MaxvCpus": {
						"Ref": "ODMaxvcpu"
					},
					"InstanceTypes": {
						"Ref": "Instancetype"
					},
					"Subnets": {
						"Ref": "Subnet"
					},
					"SecurityGroupIds": {
						"Ref": "SecurityGroup"
					},
					"InstanceRole": {
						"Ref": "ecsInstanceRoleProfile"
					}
				},
				"ServiceRole": {
					"Ref": "AWSBatchServiceRole"
				},
				"State": "ENABLED"
			}
		},
		"SpotComputeEnvironment": {
			"Type": "AWS::Batch::ComputeEnvironment",
			"DependsOn": [
				"ecsInstanceRole",
				"AWSBatchServiceRole"
			],
			"Properties": {
				"Type": "MANAGED",
				"ComputeResources": {
					"Type": "SPOT",
					"Ec2KeyPair": {
						"Ref": "Ec2keypair"
					},
					"ImageId": {
						"Ref": "ImageId"
					},
					"MinvCpus": {
						"Ref": "SpotMinvcpu"
					},
					"DesiredvCpus": {
						"Ref": "SpotDesiredvcpu"
					},
					"MaxvCpus": {
						"Ref": "SpotMaxvcpu"
					},
					"InstanceTypes": {
						"Ref": "Instancetype"
					},
					"Subnets": {
						"Ref": "Subnet"
					},
					"SecurityGroupIds": {
						"Ref": "SecurityGroup"
					},
					"InstanceRole": {
						"Ref": "ecsInstanceRoleProfile"
					},
				"BidPercentage": {
						"Ref": "Bidpercent"
					},
				"SpotIamFleetRole": {
					"Ref": "spotFleetRole"
				}
				},
				"ServiceRole": {
					"Ref": "AWSBatchServiceRole"
				},
				"State": "ENABLED"
			}
		},
		"NormalQueue": {
			"Type": "AWS::Batch::JobQueue",
			"DependsOn": [
				"OndemandComputeEnvironment"
			],
			"Properties": {
			    "JobQueueName": "Normal",
				"Priority": { "Ref": "NormalQpriority" },
				"ComputeEnvironmentOrder": [{
					"Order": 1,
					"ComputeEnvironment": {
						"Ref": "OndemandComputeEnvironment"
					}
				}]
			}
		},
		"LowPriorityQueue": {
			"Type": "AWS::Batch::JobQueue",
			"Properties": {
			    "JobQueueName": "LowPriority",
				"Priority": { "Ref": "LowpriorityQpriority" },
				"ComputeEnvironmentOrder": [{
					"Order": 1,
					"ComputeEnvironment": {
						"Ref": "SpotComputeEnvironment"
					}
				}]
			},
			"DependsOn": [
				"SpotComputeEnvironment"
			]
		},
        "JobDefinition": {
			"Type": "AWS::Batch::JobDefinition",
			"Properties": {
				"Type": "container",
				"ContainerProperties": {
					"Image": { "Fn::Join" : [ "", [{"Ref": "AWS::AccountId"},".dkr.ecr.", {"Ref": "AWS::Region"}, ".amazonaws.com/",{"Ref": "ECSRepository"}, ":latest" ]] },
					"Vcpus": { "Ref": "Jobdefvcpu"	},
					"Memory": { "Ref": "Jobdefmemory" },
					"Command": [ ],
					"MountPoints": [
          {
            "ReadOnly": false,
            "SourceVolume": "arm",
            "ContainerPath": "/arm"
          }
        ],
        "Volumes": [
          {
            "Host": {
              "SourcePath": "/arm"
            },
            "Name": "arm"
          }
        ]
				},
				"RetryStrategy": {
					"Attempts": { "Ref": "Jobdefattempts" }
				}
			}
		}		
	},
	"Outputs": {
		"OndemandComputeEnvironmentArn": {
			"Value": {
				"Ref": "OndemandComputeEnvironment"
			}
		},
		"SpotComputeEnvironmentArn": {
			"Value": {
				"Ref": "SpotComputeEnvironment"
			}
		},
		"NormalQueueArn": {
			"Value": {
				"Ref": "NormalQueue"
			}
		},
		"LowPriorityQueueArn": {
			"Value": {
				"Ref": "LowPriorityQueue"
			}
		},
		"JobDefinition": {
			"Value": {
				"Ref": "JobDefinition"
			}
		},
		"ECSRepositoryURI": {
			"Value": { "Fn::Join" : [ "", [{"Ref": "AWS::AccountId"},".dkr.ecr.", {"Ref": "AWS::Region"}, ".amazonaws.com/",{"Ref": "ECSRepository"}, ":latest" ]] }
		}
	}	
}



