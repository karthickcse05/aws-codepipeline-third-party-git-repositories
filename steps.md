### git clone https://github.com/karthickcse05/aws-codepipeline-third-party-git-repositories.git .

1. ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

aws secretsmanager create-secret --name codepipeline_git --secret-string file://codepipeline_git_rsa --profile karthick-iam --region us-east-1 --query ARN --output text

Make a note of the ARN, which is required later.

arn:aws:secretsmanager:us-east-1:713792433226:secret:codepipeline_git-hcXmUf

export S3_BUCKET_NAME=codepipeline-git-custom-action-${ACCOUNT_ID} 
aws s3 mb s3://${S3_BUCKET_NAME} --region us-east-1

https://www.7-zip.org/download.html
https://www.7-zip.org/

zip -jr codepipeline_git.zip ./lambda/lambda_function.py && aws s3 cp codepipeline_git.zip s3://codepipeline-git-custom-action-713792433226/codepipeline_git.zip

7z a codepipeline_git.zip ./lambda/lambda_function.py



aws s3 cp codepipeline_git.zip s3://codepipeline-git-custom-action-713792433226/codepipeline_git.zip
 aws s3 cp codepipeline_git.zip s3://codepipeline-git-custom-action-713792433226/codepipeline_git.zip --profile karthick-iam


to create vpc with private subnet 

aws cloudformation create-stack \
--stack-name vpc-gitlab \
--template-body file://./cfn/vpc-privatepublic.yaml \
--capabilities CAPABILITY_IAM --profile karthick-iam --region us-east-1


export vpcId="vpc-03f3afb1d04b5c0ba"
export subnetId1="subnet-0f01fd3bc6ebd2a24"
export subnetId2="subnet-0805c7f712fbb1150"
export GIT_SOURCE_STACK_NAME="thirdparty-codepipeline-git-source"
export S3_BUCKET_NAME="codepipeline-git-custom-action-713792433226"
export ZIP_FILE_NAME="codepipeline_git.zip"
aws cloudformation create-stack \
--stack-name ${GIT_SOURCE_STACK_NAME} \
--template-body file://./cfn/third_party_git_custom_action.yaml \
--parameters ParameterKey=SourceActionVersion,ParameterValue=1 \
ParameterKey=SourceActionProvider,ParameterValue=CustomSourceForGit \
ParameterKey=GitPullLambdaSubnet,ParameterValue=${subnetId1}\\,${subnetId2} \
ParameterKey=GitPullLambdaVpc,ParameterValue=${vpcId} \
ParameterKey=LambdaCodeS3Bucket,ParameterValue=${S3_BUCKET_NAME} \
ParameterKey=LambdaCodeS3Key,ParameterValue=${ZIP_FILE_NAME} \
--capabilities CAPABILITY_IAM --profile karthick-iam --region us-east-1


A custom source action type is now available on the account where you deployed the stack. You can check this on the CodePipeline console by attempting to create a new pipeline. You can see your source type listed under Source provider.



export SSH_URL="git@gitlab.com:karthickcse05/cdk-demo.git"
export SAMPLE_STACK_NAME="third-party-codepipeline-git-source-test"
export SecretsManagerArn="arn:aws:secretsmanager:us-east-1:713792433226:secret:codepipeline_git-hcXmUf"
aws cloudformation create-stack \
--stack-name ${SAMPLE_STACK_NAME} \
--template-body file://./cfn/sample_pipeline_custom.yaml \
--parameters ParameterKey=Branch,ParameterValue=master \
ParameterKey=GitUrl,ParameterValue=${SSH_URL} \
ParameterKey=SourceActionVersion,ParameterValue=1 \
ParameterKey=SourceActionProvider,ParameterValue=CustomSourceForGit \
ParameterKey=CodePipelineName,ParameterValue=sampleCodePipeline \
ParameterKey=SecretsManagerArnForSSHPrivateKey,ParameterValue=${SecretsManagerArn} \
ParameterKey=GitHubToken,ParameterValue=glpat-cD_s1XFxgYJhNchGnJXr \
--capabilities CAPABILITY_IAM --profile karthick-iam --region us-east-1


aws cloudformation describe-stacks --stack-name ${SAMPLE_STACK_NAME} --profile karthick-iam --region us-east-1 --output text --query "Stacks[].Outputs[?OutputKey=='CodePipelineWebHookUrl'].OutputValue"


add webhook URL in the gitlab project


aws cloudformation delete-stack --stack-name third-party-codepipeline-git-source-test --profile karthick-iam --region us-east-1

aws cloudformation delete-stack --stack-name thirdparty-codepipeline-git-source --profile karthick-iam --region us-east-1



aws cloudformation delete-stack --stack-name vpc-gitlab --profile karthick-iam --region us-east-1

aws s3 rb s3://codepipeline-git-custom-action-713792433226 --force --profile karthick-iam --region us-east-1