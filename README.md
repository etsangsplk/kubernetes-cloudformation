# Kubernetes CloudFormation
This project is a series of [AWS CloudFormation](https://aws.amazon.com/cloudformation/) templates that will deploy a [Kubernetes cluster](https://kubernetes.io) into an [Amazon Web Services VPC](https://aws.amazon.com/vpc/).  The templates are designed to be deployed through a [CodePipeline](https://aws.amazon.com/codepipeline/) that is triggered by changes to a [Git](https://git-scm.com) repository.

The project is heavily influenced by [Kelsey Hightower's](https://github.com/kelseyhightower) excellent ["Kubernetes the Hard Way"](https://github.com/kelseyhightower/kubernetes-the-hard-way).  One day I'll write a [blog](https://keithmsharp.wordpress.com) describing in detail how this works.

## Deploying the Pipeline
There are three CloudFormation templates in the [`pipeline`](https://github.com/keithsharp/kubernetes-cloudformation/tree/master/pipeline) directory, these should be deployed in the order:

1. `s3.yaml` - creates an S3 bucket for pipeline artifacts and outputs the bucket name.
2. `pipeline-roles.yaml` - creates a role for the pipeline to use to access the artifact bucket and a role to invoke CloudFormation.  Takes the artifcat bucket name as an input and outputs the ARNs of the two roles it creates.
3. `pipeline.yaml` - creates a pipeline with a GitHub source stage and a CloudFormation deployment stage.  Takes the two role ARNs and the various GitHub parameters as inputs and outputs the console URL for the newly created pipeline.

The simplest way to deploy the pipeline to to use the CloudFormation console to deploy the templates, one at a time, in the order listed above, copying and pasting the outputs and inputs as appropriate.

Keith Sharp, [kms@passback.co.uk](mailto:kms@passback.co.uk)
