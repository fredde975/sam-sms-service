# AWS Serverless Application

article about SAM: https://alexharv074.github.io/2019/03/02/introduction-to-sam-part-ii-template-and-architecture.html#the-default-role

This is a sample template for AWS Serverless Application - Below is a brief explanation of what we have generated for you:

```bash
.
├── README.md                               <-- This instructions file
├── pom.xml                                 <-- Java dependencies
├── src
│   ├── main
│   │   └── java
│   │       └── helloworld                  <-- Source code for a lambda function
│   │           ├── App.java                <-- Lambda function code
│   │           └── GatewayResponse.java    <-- POJO for API Gateway Responses object 
│   └── test                                <-- Unit tests
│       └── java
│           └── helloworld
│               └── AppTest.java
└── template.yaml
```

##Development information

**validate sam template:** `>sam validate`

**start local apigw:** `>sam local start-api`

**start local lambdas:** `>sam local start-lambda`

**test a local lambda:** 
```
>sam local invoke "SendSMSFunction"  -e sendSMS/event.json

209-07-05 09:02:37 Found credentials in shared credentials file: ~/.aws/credentials
2019-07-05 09:02:38 Invoking index.sendSMS (nodejs8.10)

Fetching lambci/lambda:nodejs8.10 Docker container image......
2019-07-05 09:02:40 Mounting /Users/fredrik/git/sam-sms-project/.aws-sam/build/SendSMSFunction as /var/task:ro inside runtime container
START RequestId: 8d7a6ee5-13e9-136e-775a-e7784f592c04 Version: $LATEST
2019-07-05T07:02:42.723Z        8d7a6ee5-13e9-136e-775a-e7784f592c04    Sending message Hello there! How are you doing? to receiver +46739888132
2019-07-05T07:02:48.212Z        8d7a6ee5-13e9-136e-775a-e7784f592c04    Sent message to +46739888132
END RequestId: 8d7a6ee5-13e9-136e-775a-e7784f592c04
REPORT RequestId: 8d7a6ee5-13e9-136e-775a-e7784f592c04  Duration: 5640.77 ms    Billed Duration: 5700 ms        Memory Size: 128 MB     Max Memory Used: 42 MB  

{"ResponseMetadata":{"RequestId":"bd6346ed-b105-526e-a5c8-1375495086bf"},"MessageId":"8409fdd2-15c7-54d4-a9c6-a84aed922005"}
 
```

**Invoke a lambda remote (you need all the paramaters to invoke!):** 
```
>aws lambda invoke --function-name Iterator  --invocation-type Event --profile fredrikDeveloper2 --region us-east-1 outfile.txt --payload '{"count": 3, "index": 0, "step": 1}'

>aws lambda invoke --function-name aws-serverless-application3-SendSMSFunction-OZU8VOFW6HCD  --invocation-type Event --profile fredrikDeveloper2 --region us-east-1 outfile.txt --payload '{ "receiver": "+46739888132", "sender": "MySMS",  "message": "Hello there! How are you doing?", "endDate": "2019-07-01T13:13:00Z"}'
```


## lambda integration vs. proxy integration
When using SAM to generate an API for my function I always get "proxy integration". There does not seem to be a way to get "lambda integration" with SAM.

The difference with these when it comes to the function is how I can get the data from the input.

**with lamda integration**
```
{ Comment: 'Test my SMS function',
receiver: '+46739888132',
sender: 'MySMS',
message: 'Hello there! How are you doing?',
endDate: '2019-07-01T13:13:00Z' }
```
 
**with proxy integration**

In order to read the body you will need get that part of the input:

```
let receiver = 'receiver';

    if (event.body) {
        let body = JSON.parse(event.body)
        if (body.receiver) 
            receiver = body.receiver;
    }
```

The whole input for lambda proxy:
```
resource: '/send-sms',
path: '/send-sms',
httpMethod: 'POST',
headers: 
{ Accept: '*/*',
'Accept-Encoding': 'gzip, deflate',
'cache-control': 'no-cache',
'CloudFront-Forwarded-Proto': 'https',
'CloudFront-Is-Desktop-Viewer': 'true',
'CloudFront-Is-Mobile-Viewer': 'false',
'CloudFront-Is-SmartTV-Viewer': 'false',
'CloudFront-Is-Tablet-Viewer': 'false',
'CloudFront-Viewer-Country': 'SE',
'Content-Type': 'application/json',
Host: 'pymzukyr67.execute-api.us-east-1.amazonaws.com',
'Postman-Token': 'bf8e016b-f1f6-42c7-a94e-057cff983dc5',
'User-Agent': 'PostmanRuntime/7.1.1',
Via: '1.1 b76b599f5a094e362930b9cd4c9288fe.cloudfront.net (CloudFront)',
'X-Amz-Cf-Id': 'lzd1NH2hTfjN8YlC_f53hxON4ZznBZMrVviwjnoxI63s2FVwXKCT_w==',
'X-Amzn-Trace-Id': 'Root=1-5d1f08aa-26f9a09854fa4a98710e9958',
'X-Forwarded-For': '81.170.220.10, 52.46.41.81',
'X-Forwarded-Port': '443',
'X-Forwarded-Proto': 'https' },
multiValueHeaders: 
{ Accept: [ '*/*' ],
'Accept-Encoding': [ 'gzip, deflate' ],
'cache-control': [ 'no-cache' ],
'CloudFront-Forwarded-Proto': [ 'https' ],
'CloudFront-Is-Desktop-Viewer': [ 'true' ],
'CloudFront-Is-Mobile-Viewer': [ 'false' ],
'CloudFront-Is-SmartTV-Viewer': [ 'false' ],
'CloudFront-Is-Tablet-Viewer': [ 'false' ],
'CloudFront-Viewer-Country': [ 'SE' ],
'Content-Type': [ 'application/json' ],
Host: [ 'pymzukyr67.execute-api.us-east-1.amazonaws.com' ],
'Postman-Token': [ 'bf8e016b-f1f6-42c7-a94e-057cff983dc5' ],
'User-Agent': [ 'PostmanRuntime/7.1.1' ],
Via: 
[ '1.1 b76b599f5a094e362930b9cd4c9288fe.cloudfront.net (CloudFront)' ],
'X-Amz-Cf-Id': [ 'lzd1NH2hTfjN8YlC_f53hxON4ZznBZMrVviwjnoxI63s2FVwXKCT_w==' ],
'X-Amzn-Trace-Id': [ 'Root=1-5d1f08aa-26f9a09854fa4a98710e9958' ],
'X-Forwarded-For': [ '81.170.220.10, 52.46.41.81' ],
'X-Forwarded-Port': [ '443' ],
'X-Forwarded-Proto': [ 'https' ] },
queryStringParameters: null,
multiValueQueryStringParameters: null,
pathParameters: null,
stageVariables: null,
requestContext: 
{ resourceId: '5la8rr',
resourcePath: '/send-sms',
httpMethod: 'POST',
extendedRequestId: 'cV5KmGYxIAMFy3g=',
requestTime: '05/Jul/2019:08:22:02 +0000',
path: '/Prod/send-sms',
accountId: '487526570401',
protocol: 'HTTP/1.1',
stage: 'Prod',
domainPrefix: 'pymzukyr67',
requestTimeEpoch: 1562314922220,
requestId: 'f72a9c69-9efd-11e9-b78c-8d1017039c72',
identity: 
{ cognitoIdentityPoolId: null,
accountId: null,
cognitoIdentityId: null,
caller: null,
sourceIp: '81.170.220.10',
principalOrgId: null,
accessKey: null,
cognitoAuthenticationType: null,
cognitoAuthenticationProvider: null,
userArn: null,
userAgent: 'PostmanRuntime/7.1.1',
user: null },
domainName: 'pymzukyr67.execute-api.us-east-1.amazonaws.com',
apiId: 'pymzukyr67' },
body: '{\n "Comment": "Test my SMS function",\n "receiver": "+46739888132",\n "sender": "MySMS",\n "message": "Hello there! How are you doing?",\n "endDate": "2019-07-01T13:13:00Z",\n}',
isBase64Encoded: false }
```

## Requirements

* AWS CLI already configured with Administrator permission
* [Java SE Development Kit 8 installed](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Docker installed](https://www.docker.com/community-edition)
* [Maven](https://maven.apache.org/install.html)

## Setup process

### Installing dependencies

We use `maven` to install our dependencies and package our application into a JAR file:

```bash
mvn package
```
### Local development

**Invoking function locally through local API Gateway**

```bash
sam local start-api
```

If the previous command ran successfully you should now be able to hit the following local endpoint to invoke your function `http://localhost:3000/hello`

**SAM CLI** is used to emulate both Lambda and API Gateway locally and uses our `template.yaml` to understand how to bootstrap this environment (runtime, where the source code is, etc.) - The following excerpt is what the CLI will read in order to initialize an API and its routes:

```yaml
...
Events:
    HelloWorld:
        Type: Api # More info about API Event Source: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#api
        Properties:
            Path: /hello
            Method: get
```

## Packaging and deployment

AWS Lambda Java runtime accepts either a zip file or a standalone JAR file - We use the latter in this example. SAM will use `CodeUri` property to know where to look up for both application and dependencies:

```yaml
...
    HelloWorldFunction:
        Type: AWS::Serverless::Function
        Properties:
            CodeUri: target/HelloWorld-1.0.jar
            Handler: helloworld.App::handleRequest
```

Firstly, we need a `S3 bucket` where we can upload our Lambda functions packaged as ZIP before we deploy anything - If you don't have a S3 bucket to store code artifacts then this is a good time to create one:

```bash
aws s3 mb s3://BUCKET_NAME
```

Next, run the following command to package our Lambda function to S3:

```bash
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket fredrik-sms-service --profile fredrikDeveloper2 --region us-east-1
```

Next, the following command will create a Cloudformation Stack and deploy your SAM resources.

```bash
sam deploy \
    --template-file packaged.yaml \
    --stack-name aws-serverless-application4 \
    --capabilities CAPABILITY_IAM --profile fredrikDeveloper2 --region us-east-1
```

> **See [Serverless Application Model (SAM) HOWTO Guide](https://github.com/awslabs/serverless-application-model/blob/master/HOWTO.md) for more details in how to get started.**

After deployment is complete you can run the following command to retrieve the API Gateway Endpoint URL:

```bash
aws cloudformation describe-stacks \
    --stack-name aws-serverless-application \
    --query 'Stacks[].Outputs'
```

## Testing

We use `JUnit` for testing our code and you can simply run the following command to run our tests:

```bash
mvn test
```

# Appendix

## AWS CLI commands

AWS CLI commands to package, deploy and describe outputs defined within the cloudformation stack:

```bash
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket REPLACE_THIS_WITH_YOUR_S3_BUCKET_NAME

sam deploy \
    --template-file packaged.yaml \
    --stack-name aws-serverless-application \
    --capabilities CAPABILITY_IAM \
    --parameter-overrides MyParameterSample=MySampleValue

aws cloudformation describe-stacks \
    --stack-name aws-serverless-application --query 'Stacks[].Outputs'
```

## Bringing to the next level

Here are a few ideas that you can use to get more acquainted as to how this overall process works:

* Create an additional API resource (e.g. /hello/{proxy+}) and return the name requested through this new path
* Update unit test to capture that
* Package & Deploy

Next, you can use the following resources to know more about beyond hello world samples and how others structure their Serverless applications:

* [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/)


## SAM templates, lambda functions and permissions
https://github.com/awslabs/serverless-application-model/blob/master/docs/policy_templates.rst

1. The way I got policies to work was first to create a `LambdaExecutionRole`.
    
    ```
      LambdaExecutionRole:
        Type: "AWS::IAM::Role"
        Properties:
          ManagedPolicyArns:
            - "arn:aws:iam::aws:policy/AmazonSNSFullAccess"   <-------- using a managed policy here
          AssumeRolePolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Principal:
                  Service: lambda.amazonaws.com
                Action: "sts:AssumeRole"```
    ```

2. Then in the definition for the lambda function I referenced 
    ```
      SendSMSFunction:
        Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
        Properties:
          Role: !GetAtt [ LambdaExecutionRole, Arn ]               <------ here I'm using the role
          CodeUri: sendSMS/           
          Handler: index.sendSMS
          Runtime: nodejs8.10
    ```

Another way is to use managed policies:

```
  SendSMSFunction:
    Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
    Properties:
      CodeUri: sendSMS/           #point to the code in the project folder structure
      Handler: index.sendSMS
      Runtime: nodejs8.10
      Events:
        HelloWorld:
          Type: Api
          Properties:
            Path: /send-sms
            Method: post
      Policies: AmazonSNSFullAccess        <------- set the policy here
```


Yet another way I got it working:

1. This time I managed to write an inline policy with the permissions. A drawback with inline policy is that when you look in the Aws Console then you don't see what the permissions are.

```SendSMSFunction:
    Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
    Properties:
      CodeUri: sendSMS/           #point to the code in the project folder structure
      Handler: index.sendSMS
      Runtime: nodejs8.10
      Policies:
        - Statement:
          - Sid: AmazonSNSFullAccess   # this is just a name you select on your own
            Effect: Allow
            Action:
              - sns:*
            Resource: '*' 
```

### Policies:
**Reading the documentation below I thouhgt it should be done in a different way than describe above.**


https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#properties

```
Role        string	ARN of an IAM role to use as this function's execution role. If omitted, a default role is created for this function.
Policies    string | List of string | IAM policy document object | List of IAM policy document object | List of SAM Policy Templates	Names of AWS managed IAM policies or IAM policy documents or SAM Policy Templates that this function needs, which should be appended to the default role for this function. If the Role property is set, this property has no meaning.
```


This property grants permission to your Lambda function to publish messages to the HelloWorldTopic SNS topic.

This is necessary, because by default Lambda functions are assigned an IAM role that has no permissions at all (except to write to the CloudWatch logs). That means that for every AWS service that your Lambda function must access, you have to add the appropriate permissions to the Lambda function’s role.

In our case, we grant the required permissions to the function by using a SAM feature called policy templates. Policy templates are a set of predefined IAM policies that can be referenced by a simple string.

In our template we use the SNSPublishMessagePolicy policy template which matches our requirement of granting permission to publish messages to a specific SNS topic. This policy template requires a single argument named TopicName which must be the name of the SNS topic for which publishing permission should be granted. The specifications of all available SAM policy templates are available in this file.

Note that we use another CloudFormation intrinsic function GetAtt to retrieve the name of the HelloWorldTopic SNS topic. This is necessary, because this name is automatically generated and not known at the time of the initial deployment.

**I found one exampel like this:** 
```
##https://stackoverflow.com/questions/48985893/not-able-to-add-policies-in-sam-template
#  SomeFunction:
#    Type: AWS::Serverless::Function
#    Properties:
#      Handler: index.handler
#      Runtime: nodejs8.10
#      Policies:
#        - Statement:
#            - Sid: SSMDescribeParametersPolicy
#              Effect: Allow
#              Action:
#                - ssm:DescribeParameters
#              Resource: '*'
#            - Sid: SSMGetParameterPolicy
#              Effect: Allow
#              Action:
#                - ssm:GetParameters
#                - ssm:GetParameter
#              Resource: '*'
```

But I could not figure out how the statements should be creted. Also tried that exact statement but got an error 


Another example

```
https://github.com/awslabs/serverless-application-model/issues/224

EDIT: After trial and error, I found out that only the statement part from iam-managedPolicy is needed
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-iam-managedpolicy.html

Resources:
  SomeFunction:
    Type: 'AWS::Serverless::Function'
    Properties:
      FunctionName: SomeFunction
      MemorySize: 128
      Handler: index.handler
      Runtime: nodejs6.10
      Timeout: 20
      Policies:
        - Statement:
            - Effect: Allow
              Action:
                - 'cloudformation:DescribeStacks'
                - 'cloudformation:DescribeStackResources'
                - 'cloudformation:CancelUpdateStack'
                - 'iam:PassRole'
              Resource:
                - '*'```