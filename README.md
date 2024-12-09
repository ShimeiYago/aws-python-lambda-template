# AWS Python Lambda Template
A template for deploying Python Lambda functions as container images on AWS Lambda. This repository provides a structured and reusable setup to simplify the deployment process.

## Table of Contents

- [Set Environment Variables](#set-environment-variables)
- [Setup](#setup)
- [Local Test](#local-test)
- [Deployment](#deployment)
- [Launch](#launch)
- [License](#license)
- [Author](#author)

## Set Environment Variables

```
REGION="ap-northeast-1"
ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
LAMBDA_NAME="lambda-name"
```

## Setup

1. **Build image**
```
docker build -t ${LAMBDA_NAME}:<tag> .
```

## Local Test

1. **Run Container**
```
docker run --rm -p 9000:8080 -v ./lambda_function.py:/var/task/lambda_function.py ${LAMBDA_NAME}:<tag>
```

Then POST local endpoint.
```
curl "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{}'
```

Request body '{}' is the object handled as event.

## Deployment

1. **Login**

```
aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com
```

2. **Create ECR Repository (Only first time)**

Please run this command only first time.

```
aws ecr create-repository --repository-name $LAMBDA_NAME --region $REGION --image-scanning-configuration scanOnPush=true --image-tag-mutability MUTABLE
```

3. **Add Docker tag**

```
docker tag ${LAMBDA_NAME}:<tag> ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${LAMBDA_NAME}:<tag>
```


4. **Push Image**

```
docker push ${ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${LAMBDA_NAME}:<tag>
```

## Launch (GUI)

(This is only first time)

Create new AWS Lambda from GUI based on the deployed image.

Please select the architecture you used in building the Docker image.

## License

[MIT](https://github.com/tcnksm/tool/blob/master/LICENCE)

## Author

[ShimeiYago](https://github.com/ShimeiYago)