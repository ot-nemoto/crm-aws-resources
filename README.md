# crm-aws-resources

- [CRM](https://github.com/nemodija/crm-compose)をAWSに構築するためのCloudFormation

## deploy

### environments

|Name|Type|Default|Description|
|--|--|--|--|
|ImageId|`AWS::EC2::Image::Id`|ami-0cc75a8978fbbc969|EC2インスタンスのAMI|
|InstanceType|String|t2.small|EC2インスタンスのInstanceType|
|SnapshotId|String|(*required*)|`/crm`にマウントするボリュームの[スナップショット](https://ap-northeast-1.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-1#Snapshots:sort=desc:startTime)|
|DockerComposeVersion|String|1.26.2|利用するdocker-composeのバージョン|
|CertificateArn|String|(*required*)|ロードバランサのリスナ(HTTPS)に設定する証明書(ACM)のArn|

```sh
# e.g.
SNAPSHOT_ID=snap-0927dbdc6113398f0
CERTIFICATE_ARN=$(aws acm list-certificates \
  --query 'CertificateSummaryList[?DomainName==`*.example.com`].CertificateArn' \
  --output text)
```

### create-stack

```sh
aws cloudformation create-stack \
    --stack-name crm \
    --parameters ParameterKey=SnapshotId,ParameterValue=${SNAPSHOT_ID} \
                 ParameterKey=CertificateArn,ParameterValue=${CERTIFICATE_ARN} \
    --capabilities CAPABILITY_IAM \
    --template-body file://template.yaml
```
