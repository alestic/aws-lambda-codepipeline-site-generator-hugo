
# Static site generator plugin: Hugo

This is a static site generator plugin for the [AWS Git-backed static
website stack][stack]. This plugin treats the Git repository content
as source for [Hugo][hugo], a static website generator.

This plugin takes the form of an AWS Lambda function that is deployed
in a ZIP file in an S3 bucket. When the static website stack is
created with CloudFormation, the site generator AWS Lambda function
parameters are pointed to the ZIP file in the S3 bucket, causing the
AWS Lambda function to be created and run in the new stack.

When the stack is running and the CodeCommit Git repository contents
are updated, CodePipeline automatically invokes this AWS Lambda
function, providing it a ZIP file of the CodeCommit Git branch
contents. This function turns that site source into the static web
site contents, and passes back a ZIP file. The stack then syncs that
content to the S3 bucket that serves the static website.

This Hugo static site generator plugin runs the following command:

    ./hugo --source=SOURCEDIR --destination=SITEDIR

where SOURCEDIR contains the content of the CodeCommit Git repository,
and SITEDIR is the resulting content that will be sync'd to the S3
bucket serving the static website.

When passing parameters to the AWS Git-backed static website
CloudFormation template, specify:

- **GeneratorLambdaFunctionS3Bucket** - The S3 bucket containing this
  AWS Lambda function ZIP file. E.g., "run.alestic.com"

- **GeneratorLambdaFunctionS3Key** - The S3 key containing this AWS
  Lambda function ZIP file.  E.g.,
  "lambda/aws-lambda-codepipeline-site-generator-hugo.zip"

- **GeneratorLambdaFunctionRuntime** - "python2.7"

- **GeneratorLambdaFunctionHandler** - "index.handler"

- **GeneratorLambdaFunctionUserParameters** - If this value starts
  with a dash (-) then it will be appended to the hugo command
  line. If it does not start with a dash, then it will be ignored.

  You may use this value to specify options like "--theme THEMENAME".

  People with warped minds could stick a semicolon (;) in this and run
  arbitrary static site post-processing commands in the AWS Lambda
  environment. This is not guaranteed to be supported in future
  versions.

[stack]: https://github.com/alestic/aws-git-backed-static-website
[hugo]: https://gohugo.io/
