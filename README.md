# Kubernetes CloudFormation
This project is a series of [AWS CloudFormation](https://aws.amazon.com/cloudformation/) templates that will deploy a [Kubernetes cluster](https://kubernetes.io) into an [Amazon Web Services VPC](https://aws.amazon.com/vpc/).  The templates are designed to be deployed through a [CodePipeline](https://aws.amazon.com/codepipeline/) that is triggered by changes to a [Git](https://git-scm.com) repository.

The project is heavily influenced by [Kelsey Hightower's](https://github.com/kelseyhightower) excellent ["Kubernetes the Hard Way"](https://github.com/kelseyhightower/kubernetes-the-hard-way).  One day I'll write a [blog](https://keithmsharp.wordpress.com) describing in detail how this works.

## Deploying the Pipeline
There are three CloudFormation templates in the [`pipeline`](https://github.com/keithsharp/kubernetes-cloudformation/tree/master/pipeline) directory:

1. `s3.yaml` - creates an S3 bucket for pipeline artifacts and outputs the bucket name.
2. `pipeline-roles.yaml` - creates a role for the pipeline to use to access the artifact bucket and a role to invoke CloudFormation.  Takes the artifcat bucket name as an input and outputs the ARNs of the two roles it creates.
3. `pipeline.yaml` - creates a pipeline with a GitHub source stage and a CloudFormation deployment stage.  Takes the two role ARNs and the various GitHub parameters as inputs and outputs the console URL for the newly created pipeline.

You can deploy the templates as a nested stack by using the CLI.  Follow these steps:

1. Clone the Git repository and `cd pipeline`.
2. Upload the three templates: `s3.yaml`, `pipeline-roles.yaml`, and `pipeline.yaml` to an S3 bucket.
3. Note the S3 URLs of the files you've uploaded and update the file `pipeline-master.yaml` to reference these URLs in `TemplateURL` fields.
4. Run the following command, with appropriate parameter values:

```
aws cloudformation deploy \
  --template-file ./pipeline-master.yaml \
  --stack-name STACKNAME \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
    GitHubUser=GITHUB_USERNAME \
    GitHubToken=GITHUB_OAUTH_TOKEN \
    GitHubRepo=GITHUB_REPO_NAME \
    GitHubBranch=master
```

The other way to deploy the pipeline to to use the CloudFormation console to deploy the templates, one at a time, in the order listed above, copying and pasting the outputs and inputs as appropriate.

## The VPC
The [vpc](https://github.com/keithsharp/kubernetes-cloudformation/tree/master/vpc) directory holds the CloudFormation YAML file and JSON Parameters file to deploy the VPC structure.  Once you have created the pipeline, as described above, AWS CodePipeline and AWS CloudFormation should automatically kick in and deploy the VPC.  This takes a few seconds to trigger so be patient.  You can track the status using the AWS CodePipeline Web Console.

Note that the parameters file format is different for parameters supplied to a CloudFormation template through the CloudFormation Web Console or CLI to those supplied to a CloudFormation deployment stage in a CodePipeline.  It would be nice if Amazon made these formats the same, and switched them to YAML!

Keith Sharp, [kms@passback.co.uk](mailto:kms@passback.co.uk)
