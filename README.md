Here’s a **very basic AWS API Gateway project** you can try hands-on 🚀.
It uses **API Gateway + Lambda + IAM Role** and returns a simple JSON response.

---

# 🔹 Project: Hello World with API Gateway

## 📌 Overview

We’ll:

1. Create a **Lambda function** (`hello-world`).
2. Create an **API Gateway REST API**.
3. Connect the API Gateway to Lambda.
4. Test it via a public endpoint.

---

## 🛠️ Steps

### 1. Create IAM Role for Lambda

```bash
aws iam create-role \
  --role-name lambda-basic-role \
  --assume-role-policy-document '{
    "Version": "2012-10-17",
    "Statement": [{
      "Effect": "Allow",
      "Principal": { "Service": "lambda.amazonaws.com" },
      "Action": "sts:AssumeRole"
    }]
  }'
```

Attach policy for CloudWatch logging:

```bash
aws iam attach-role-policy \
  --role-name lambda-basic-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

---

### 2. Create Lambda Function

**lambda\_function.py**

```python
def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "body": "Hello from API Gateway + Lambda!"
    }
```

Zip and deploy:

```bash
zip function.zip lambda_function.py

aws lambda create-function \
  --function-name hello-world \
  --zip-file fileb://function.zip \
  --handler lambda_function.lambda_handler \
  --runtime python3.12 \
  --role arn:aws:iam::<YOUR_ACCOUNT_ID>:role/lambda-basic-role
```

---

### 3. Create API Gateway REST API

```bash
aws apigateway create-rest-api --name "HelloAPI"
```

Get the **root resource ID**:

```bash
aws apigateway get-resources --rest-api-id <API_ID>
```

Add a resource `/hello`:

```bash
aws apigateway create-resource \
  --rest-api-id <API_ID> \
  --parent-id <ROOT_ID> \
  --path-part hello
```

Add **GET method**:

```bash
aws apigateway put-method \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --http-method GET \
  --authorization-type "NONE"
```

Integrate with Lambda:

```bash
aws apigateway put-integration \
  --rest-api-id <API_ID> \
  --resource-id <RESOURCE_ID> \
  --http-method GET \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri arn:aws:apigateway:<REGION>:lambda:path/2015-03-31/functions/arn:aws:lambda:<REGION>:<ACCOUNT_ID>:function:hello-world/invocations
```

---

### 4. Deploy API

```bash
aws apigateway create-deployment \
  --rest-api-id <API_ID> \
  --stage-name dev
```

---

### 5. Test API

Your endpoint will look like:

```
https://<API_ID>.execute-api.<REGION>.amazonaws.com/dev/hello
```

Try in browser or with curl:

```bash
curl https://<API_ID>.execute-api.<REGION>.amazonaws.com/dev/hello
```

Expected output:

```json
Hello from API Gateway + Lambda!
```

---

✅ That’s it — a very basic **API Gateway project** with Lambda.
Would you like me to also give you a **Terraform version** of this setup so you can provision it in one go?
