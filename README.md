# Welcome to your CDK TypeScript project

This is a blank project for CDK development with TypeScript.

The `cdk.json` file tells the CDK Toolkit how to execute your app.

## Useful commands

* `npm run build`   compile typescript to js
* `npm run watch`   watch for changes and compile
* `npm run test`    perform the jest unit tests
* `npx cdk deploy`  deploy this stack to your default AWS account/region
* `npx cdk diff`    compare deployed stack with current state
* `npx cdk synth`   emits the synthesized CloudFormation template

### Architecture
#### 🟦 Client Layer (Top)
```
[🧑 User]
    |
    ▼
[🌐 Browser (HTML + JS)]
```

#### ☁️ Frontend Layer
```
[🌩️ Amazon CloudFront (CDN)]
   | Custom Domain: www.example.com
   | SSL/TLS via ACM (us-east-1)
   ▼
[🪣 Amazon S3 (Static Website Bucket)]
   | index.html, JS, CSS
   | OAC (Origin Access Control)

```

#### 🟨 Backend API Layer
```
[🌐 API Gateway (REST API)]
   | POST /register
   ▼
[🧠 AWS Lambda (register-student)]
   | Environment: TABLE_NAME
   ▼
[🗃️ DynamoDB (StudentTable)]
   | Primary Key: email
```

#### 🔒 Security & Permissions
CloudFront → S3 via OAC
Lambda → DynamoDB via IAM role
API Gateway triggers Lambda
S3 is private (not public)

### Commands for development

#### Initial development
```
cd static_website_demo
npm install           # Install all dependencies from package.json
npm run build         # Compile TypeScript to JavaScript
```

Now build Lambda function

```
cd lambda
npm install           # Install local lambda dependencies like aws-sdk (if needed locally)
npx tsc               # Compile TypeScript to JavaScript
```
Ensure your tsconfig.json and cdk.Code.fromAsset point to the compiled .js output (like dist/).


#### Bootstrap
Only once per account

```cdk bootstrap```

#### Deployment flow
```
[💻 Developer Machine]
|
├── npx cdk deploy
├── Code in /lambda and /website
└── Website deployed via s3deploy.BucketDeployment
```

```
npm run clean && npm run build && cdk deploy
```

#### Testing

For Lambda, use this as a test event

```
{
  "body": "{\"name\":\"Jane Doe\",\"email\":\"jane@example.com\",\"course\":\"Math\"}"
}
```

Through Postman

```
POST https://<api-id>.execute-api.<region>.amazonaws.com/prod/register
Headers:
  Content-Type: application/json

Body (raw JSON):
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "course": "Math"
}

```

Lastly, navigate to the URL and submit the form

```
https://<YOUR_ENDPOINT>.cloudfront.net
```

![website demo](./demo_output/static%20website%20demo.png)
