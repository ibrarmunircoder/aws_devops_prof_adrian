# Project: Host a Static Website on AWS S3 via Cloudformation

## Module 1- Host a Static Website on AWS S3 via CloudFormation

### Overview:

This series of articles provide a step-by-step guide on how to deploy a basic static website to an Amazon S3 (Simple Storage Service) bucket using AWS CloudFormation. Starting with a simple architecture, each module identifies gaps and upcoming issues, and gradually introduces additional layers of complexity to resolve those issues. The steps involved registering a custom domain, creating S3 buckets, configuring Route 53, obtaining SSL/TLS certificate, creating an Origin Access Identity (OAI), setting up CloudFront distributions, and routing DNS traffic using Route 53. By leveraging the power of CloudFormation, developers can automate the infrastructure setup and deployment process, making it easier and more efficient to launch static websites.

In this module, we are going to deploy a simple static website to S3 bucket via AWS CloudFormation in three simple steps:
👉 Step 1. Clone the React Project from the this [Link](https://github.com/ibrarmunircoder/react-aws-cfn-project.git)
👉 Step 2. Create S3 bucket and its policy, and configure it to host a static website
👉 Step 3. Build and deploy static website manually to newly created S3 bucket (optional, you can skip it and go to Module 2 for build automation)

AWS CloudFormation can be a great choice to provision and manage the complete infrastructure and AWS resources in a text file.

### Architecture:

The high-level architecture for our project is illustrated in the diagram below:

![Architecture](./images/image-1.png)

### Initial Setup:

Install required tools:
</br>
👉 any IDE (personally I prefer Visual Studio Code)
</br>
👉 git
👉 Nodejs

### AWS Resources:

Here is the list of AWS resources that we are going to create:
</br>
👉 S3 bucket
</br>
👉 S3 bucket policy

### Why host static website on S3?
Why should you host your static content on S3? Let's break it down!

☑️ Firstly, you no longer need to allocate a specific amount of storage space or plan for it, as S3 buckets automatically scale to accommodate your needs.

☑️ Moreover, since S3 operates as a serverless service, you are relieved of the burden of managing and patching servers that store your files; you simply upload and retrieve your content.

☑️ Additionally, even if your application requires a server (such as a dynamic application), it can be smaller because it doesn't need to handle requests for static content.

### Step 1. Clone a React Application

Firstly, you need to clone the website by simply running the following command in terminal:

```bash
git clone https://github.com/ibrarmunircoder/react-aws-cfn-project.git
```

Secondly, to run the application locally, go inside the cloned project folder and run two commands. First command will install depencies and second will start the project.

```bash
npm install
npm run start
```

![Project](./images/image-2.png)

### Step 2. Create S3 bucket and its policy, and configure it to host a static website

In order to host our website on S3 bucket, let’s create and configure all required resources via CloudFormation.

1️⃣ The following piece of code helps to create a new S3 bucket and configure it to host a static website

```yml
Resources:
  chatGPTStaticWebsiteHostingBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: 'chatgpt-static-website'
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
```

2️⃣ Next step is to set S3 bucket permissions for website access.

When you configure a bucket as a static website, if you want your website to be public, you must disable block public access settings for the bucket and write a bucket policy that grants public read access.

That is why we are going to disable ACLs for our bucket under PublicAccessBlockConfiguration property and set the ownership of S3 bucket content to Object writer (i.e. AWS account that uploads an object owns the object and has full control over it).

```yaml
OwnershipControls:
    Rules:
        - ObjectOwnership: ObjectWriter
PublicAccessBlockConfiguration:
    BlockPublicAcls: false
    BlockPublicPolicy: false
    IgnorePublicAcls: false
    RestrictPublicBuckets: false
```

Basically we are disabling S3 Block Public Access settings to let users get a public access to the website.

![Block Access](./images/image-3.jpg)

3️⃣ Next step is to create an S3 bucket policy

We need to give a public read access to S3 bucket objects. Here is the policy:

```yaml
  bucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref chatGPTStaticWebsiteHostingBucket
      PolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Sid: 'PublicReadForGetBucketObjects'
            Effect: 'Allow'
            Principal: '*'
            Action: 's3:GetObject'
            Resource: !Join
              - ''
              - - 'arn:aws:s3:::'
                - !Ref chatGPTStaticWebsiteHostingBucket
                - '/*'
```

4️⃣ We are done with Resources, now let's output the website URL to make it easier to navigate using the link once the stack is built.

```yaml
Outputs:
  outputWebsiteURL:
    Value: !GetAtt 
      - chatGPTStaticWebsiteHostingBucket
      - WebsiteURL
    Description: Static website URL
```

5️⃣ We are ready to create and run CloudFormation stack based on our template.

I prefer to run the stack from AWS Console as it provides an end-to-end overview of a process flow. But you can always run a stack using the AWS CLI [(here is how)](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-cli.html).


![Create](./images/image-4.png)
![Create](./images/image-5.png)

Once the stack built, you should see a newly created bucket and it's policy under Resources tab:

![Resources](./images/image-6.png)

### Step 3. Build and deploy static website manually to newly created S3 bucket

1️⃣ First, we need to build the production version of our React app locally.

Open Terminal for our app and run npm run build command

```bash
npm run build
```

A new build folder should be autogenerated with static content.

![build](./images/image-7.png)

2️⃣ In AWS Console navigate to our S3 bucket and upload all the files and folders from build folder:

![Upload](./images/image-8.png)
![Upload](./images/image-9.png)

3️⃣ Now refresh the website link - our React app should be up and running:

![Website](./images/image-10.png)


## Module 2- Automate the build of a Static Website Hosted on AWS S3 via CodeBuild and CloudFormation

### Overview:

In Module 1, we have created a simple static web app and hosted it on S3 bucket. However we took baby steps to deploy our static content to S3 bucket manually. Ideally we want to use a tool that would rebuild the source code every time a code change is pushed to the repository and deploy built files to S3 bucket automatically.

In this module I'll show you how to automate the build and deployment via AWS CodeBuild in six simple steps:
</br>
👉 Step 1. Create buildspec YAML file
</br>
👉 Step 2. Provide CodeBuild with access to GitHub repo
</br>
👉 Step 3. Configure how AWS CodeBuild builds your source code
</br>
👉 Step 4. Create IAM role for CodeBuild project
</br>
👉 Step 5. Run the CloudFormation stack
</br>
👉 Step 6. Update frontend source code and watch how it will be built automatically

### Architecture:
The high-level architecture for our project is illustrated in the diagram below:

![Architecture](./images/image-11.jpg)

### AWS Resources:
Here is the list of AWS resources that we are going to create:
</br>
👉 CodeBuild project
</br>
👉 IAM role for CodeBuild project
</br>
👉 GitHub Source Credential for CodeBuild project

### Step 1. Create buildspec YAML file

1️⃣ Install phase of buildspec file

Use the install phase only for installing packages in the build environment. In order to run the production build of our React web app, CodeBuild server needs Nodejs and npm installation.

```yaml
phases:
  install:
    runtime-versions:
      nodejs: 16
    commands:
      - echo "📦 installing packages..."
      - npm install --omit=dev
```

2️⃣ Build phase of buildspec file

Use the build phase for commands that CodeBuild runs during the build.

```yaml
build:
    commands:
        - echo "🚧 Starting compiling packages..."
        - npm  run build
        - echo "✅ Build passed successfully."
```

3️⃣ Artifacts of buildspec file

Artifacts represent information about where CodeBuild can find the build output and how CodeBuild prepares it for uploading to the S3 output bucket. Here we need to specify the path to build folder so that CodeBuild won't deploy a whole frontend project to S3 bucket but files for prod build only:

```yaml
artifacts:
  base-directory: 'build*'
  discard-paths: yes
  files:
    - '**/*'  
```

discard-paths helps to make sure to put all the files in the root folder instead of subfolder. In our case it will take all the files (including index.hmtl file) from build folder and put them in the root of S3 bucket.


### Step 2. Provide CodeBuild with access to GitHub repo
1️⃣ In order to access source code for frontend located on your GitHub account, CodeBuild needs some access privilege. The easiest and safest way to grand CodeBuild an access to GitHub is to create a personal access token.

Navigate to your GitHub and click your profile photo, then click Settings.

![Settings](./images/image-11.png)

In the left sidebar, scroll down and click Developer settings.

![Settings](./images/image-12.png)

In the left sidebar, under Personal access tokens, click Tokens (classic) (since Fine-grained tokens is in beta and might not be compatible with AWS CodeBuild yet), then click Generate new token.

![Settings](./images/image-13.png)

Select the scopes you'd like to grant this token.

![Token](./images/image-14.png)
![Token](./images/image-15.png)
![Token](./images/image-16.png)

Click Generate token. Copy the new token - we will need it while running a CloudFormation stack.

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

2️⃣ In CloudFormation template we are going to create a new AWS::CodeBuild::SourceCredential resource. Here we provide information about the credentials for a GitHub.

It is recommend to use AWS Secrets Manager to store our credentials. But for the sake of simplicity (let's take baby steps) we will pass a newly generated GitHub access token as a CloudFormation parameter for now.

```yaml
Parameters:
  paramPersonalGitHubAccessToken:
    Type: String
    MinLength: 10
    ConstraintDescription: Personal GitHub access token is missing
    Description: Provide your personal GitHub access token for 
    CodeBuild to access your GitHub repo

myCodeBuildSourceCredential:
    Type: AWS::CodeBuild::SourceCredential
    Properties:
      AuthType: PERSONAL_ACCESS_TOKEN
      ServerType: GITHUB
      Token: !Ref paramPersonalGitHubAccessToken
```

### Step 3. Configure how AWS CodeBuild builds your source code