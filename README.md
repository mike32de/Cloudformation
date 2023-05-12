# Table of Contents

## List Instance ID, Type and Name
aws ec2 describe-instances | jq -r '.Reservations[].Instances[]|.InstanceId+" "+.InstanceType+" "+(.Tags[] | select(.Key == "Name").Value)'


## List Instances with Public IP Address and Name
aws ec2 describe-instances --query 'Reservations[*].Instances[?not_null(PublicIpAddress)]' | jq -r '.[][]|.PublicIpAddress+" "+(.Tags[]|select(.Key=="Name").Value)'


## List of VPCs and CIDR IP Block
aws ec2 describe-vpcs | jq -r '.Vpcs[]|.VpcId+" "+(.Tags[]|select(.Key=="Name").Value)+" "+.CidrBlock'


## List of Subnets for a VPC
aws ec2 describe-subnets --filter Name=vpc-id,Values=vpc-0d1c1cf4e980ac593 | jq -r '.Subnets[]|.SubnetId+" "+.CidrBlock+" "+(.Tags[]|select(.Key=="Name").Value)'


## List of Security Groups
aws ec2 describe-security-groups | jq -r '.SecurityGroups[]|.GroupId+" "+.GroupName'


## S3
### List Buckets
aws s3 ls


## API Gateway
### List of API Gateway IDs and Names
aws apigateway get-rest-apis | jq -r '.items[] | .id+" "+.name'


### List of API Gateway Keys
aws apigateway get-api-keys | jq -r '.items[] | .id+" "+.name'


### List API Gateway Domain Names
aws apigateway get-domain-names | jq -r '.items[] | .domainName+" "+.regionalDomainName'


### List of Resources for API Gateway
aws apigateway get-resources --rest-api-id ee86b4cde  | jq -r '.items[] | .id+" "+.path'


### Find Lambda for API Gateway Resource
aws apigateway get-integration --rest-api-id ee86b4cde --resource-id 69ef7d4c8 --http-method GET | jq -r '.uri'


## ELB
### List of ELB Hostnames
aws elbv2 describe-load-balancers --query 'LoadBalancers[*].DNSName'  | jq -r 'to_entries[] | .value'

### List of ELB ARNs
aws elbv2 describe-load-balancers | jq -r '.LoadBalancers[] | .LoadBalancerArn'

### List of ELB Target Group ARNs
aws elbv2 describe-target-groups | jq -r '.TargetGroups[] | .TargetGroupArn'

### Find Instances for a Target Group
aws elbv2 describe-target-health --target-group-arn arn:aws:elasticloadbalancing:ap-southeast-1:987654321:targetgroup/wordpress-ph/88f517d6b5326a26 | jq -r '.TargetHealthDescriptions[] | .Target.Id'


## RDS
### List of DB Clusters
aws rds describe-db-clusters | jq -r '.DBClusters[] | .DBClusterIdentifier+" "+.Endpoint'

### List of DB Instances
aws rds describe-db-instances | jq -r '.DBInstances[] | .DBInstanceIdentifier+" "+.DBInstanceClass+" "+.Endpoint.Address'

### Take DB Instance Snapshot
aws rds create-db-snapshot --db-snapshot-identifier backend-dev-snapshot-0001 --db-instance-identifier backend-dev
aws rds describe-db-snapshots --db-snapshot-identifier backend-dev-snapshot-0001 --db-instance-identifier general

### Take DB Cluster Snapshot
aws rds create-db-cluster-snapshot --db-cluster-snapshot-identifier backend-prod-snapshot-0002 --db-cluster-identifier backend-prod


## ElastiCache
### List of ElastiCache Machine Type and Name
aws elasticache describe-cache-clusters | jq -r '.CacheClusters[] | .CacheNodeType+" "+.CacheClusterId'

### List of ElastiCache Replication Groups
aws elasticache describe-replication-groups | jq -r '.ReplicationGroups[] | .ReplicationGroupId+" "+.NodeGroups[].PrimaryEndpoint.Address'

### List of ElastiCache Snapshots
aws elasticache describe-snapshots | jq -r '.Snapshots[] | .SnapshotName'

### Create ElastiCache Snapshot
aws elasticache create-snapshot --snapshot-name backend-login-hk-snap-0001 --replication-group-id backend-login-hk --cache-cluster-id backend-login-hk

### Delete ElastiCache Snapshot
aws elasticache delete-snapshot --snapshot-name backend-login-hk-snap-0001

### Scale Up/Down ElastiCache Replica
aws elasticache increase-replica-count --replication-group-id backend-login-hk --apply-immediately
aws elasticache decrease-replica-count --replication-group-id backend-login-hk --apply-immediately


## Lambda
### List of Lambda Functions, Runtime and Memory
aws lambda list-functions | jq -r '.Functions[] | .FunctionName+" "+.Runtime+" "+(.MemorySize|tostring)'

### List of Lambda Layers
aws lambda list-layers | jq -r '.Layers[] | .LayerName'

### List of Lambda Layers
aws lambda list-layers | jq -r '.Layers[] | .LayerName'

### List of Source Event for Lambda
aws lambda list-event-source-mappings | jq -r '.EventSourceMappings[] | .FunctionArn+" "+.EventSourceArn'

### Download Lambda Code
aws lambda get-function --function-name DynamoToSQS | jq -r .Code.Location
https://awslambda-ap-se-1-tasks.s3.ap-southeast-1.amazonaws.com/snapshots/987654321/backend-api-function-1fda0de7-a751-4586-bf64-5601a410c170


## Cloudwatch
### List of CloudWatch Alarms and Status
aws cloudwatch describe-alarms | jq -r '.MetricAlarms[] | .AlarmName+" "+.Namespace+" "+.StateValue'

### Create Alarm for EC2 High CPUUtilization
aws cloudwatch put-metric-alarm --alarm-name high-cpu-usage --alarm-description "Alarm when CPU exceeds 70 percent" --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 70 --comparison-operator GreaterThanThreshold  --dimensions "Name=InstanceId,Value=i-123456789" --evaluation-periods 2 --alarm-actions arn:aws:sns:ap-southeast-1:987654321:System-Alerts --unit Percent

### Create Alarm for EC2 High StatusCheckFailed_Instance
aws cloudwatch put-metric-alarm --alarm-name EC2-StatusCheckFailed-AppServer --alarm-description "EC2 StatusCheckFailed for AppServer" --metric-name StatusCheckFailed_Instance --namespace AWS/EC2 --statistic Average --period 60 --threshold 0 --comparison-operator GreaterThanThreshold  --dimensions "Name=InstanceId,Value=i-123456789" --evaluation-periods 3 --alarm-actions arn:aws:sns:ap-southeast-1:987654321:System-Alerts --unit Count


## Route53
### List Domains
aws route53 list-hosted-zones | jq -r '.HostedZones[]|.Id+" "+.Name'

### List Records for a Domain (Zone)
aws route53 list-resource-record-sets --hosted-zone-id /hostedzone/ZEB1PAH4U | jq -r '.ResourceRecordSets[]| if (.AliasTarget!=null) then .Type+" "+.Name+" "+.AliasTarget.DNSName else .Type+" "+.Name+" "+.ResourceRecords[].Value end'

























