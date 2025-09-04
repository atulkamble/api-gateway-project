aws lambda create-function \
  --function-name hello-world \
  --zip-file fileb://function.zip \
  --handler lambda_function.lambda_handler \
  --runtime python3.12 \
  --role arn:aws:iam::535002879962:role/lambda-basic-role

aws apigateway get-resources --rest-api-id hogmvrnunh

aws apigateway create-resource \
  --rest-api-id hogmvrnunh \
  --parent-id xdl3v2kck5 \
  --path-part hello

aws apigateway put-method \
  --rest-api-id hogmvrnunh \
  --resource-id xdl3v2kck5 \
  --http-method GET \
  --authorization-type "NONE"

aws apigateway put-integration \
  --rest-api-id hogmvrnunh \
  --resource-id xdl3v2kck5 \
  --http-method GET \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:535002879962:function:hello-world/invocations

aws apigateway create-deployment \
  --rest-api-id hogmvrnunh \
  --stage-name dev

https://hogmvrnunh.execute-api.us-east-1.amazonaws.com/dev/hello
