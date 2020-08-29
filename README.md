# AWS STS - Generate Credentials
GitHub Actions - AWS STS Generate Credentials - Role As A Service (RaaS)

![GitHub Actions - AWS STS Generate Credentials](https://github.com/emvaldes/generate-credentials/workflows/GitHub%20Actions%20-%20AWS%20STS%20Generate%20Credentials/badge.svg)

**<span style="color:red">1</span>** -) Create a Public|Private GitHub Repository for your project

**<span style="color:red">2</span>** -) Inject your project into your GitHub Repository

**<span style="color:red">3</span>** -) Create the GitHub Secrets to store sensitive|private data

```console
AWS_ACCESS_KEY_ID:      Terraform AWS Access Key-Id (e.g.: AKIA2...VT7DU).
AWS_DEFAULT_ACCOUNT:    The AWS Account number (e.g.: 123456789012).
AWS_DEFAULT_REGION:     The AWS Default Region (e.g.: us-east-1)
AWS_SECRET_ACCESS_KEY:  Terraform AWS Secret Access Key (e.g.: zBqDUNyQ0G...IbVyamSCpe)
DEVOPS_ACCESS_ROLE:     Defines the STS Assume-Role for the Terraform user.
DEVOPS_ACCOUNT_ID:      It's intended to mask the AWS IAM User-ID when it gets listed.
DEVOPS_ACCOUNT_NAME:    A placeholder for the Deployment Service Account's name (terraform).
```
**<span style="color:red">4</span>** -) Create a GitHub Action - Pipeline to deploy your project: .github/workflows/main.yaml <br>
**Note**: This is just a prototype of how to use this GitHub Action (uses: emvaldes/generate-credentials@v1.0)

[Pipeline Deployment](https://github.com/emvaldes/generate-credentials/blob/master/.github/workflows/main.yaml)

```console
name: GitHub Actions - AWS STS Generate Credentials
## on: [push]
on:
####----------------------------------------------------------------------------
  workflow_dispatch:
    name: 'Manual Deployment'
    description: 'Triggering Manual Deployment'
    inputs:
      logLevel:
        description: 'Log level'
        required: true
        default: 'warning'
      tags:
        description: 'Generate Credentials'
####----------------------------------------------------------------------------
  push:
    branches: [ master ]
    paths:
      - action.yaml
####----------------------------------------------------------------------------
env:
  awscli_download: 'awscli.amazonaws.com'
  awscli_package: "awscli-exe-linux-x86_64.zip"
  ## AWS Enviroment variables
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  ## AWS Default variables
  AWS_DEFAULT_ACCOUNT: ${{ secrets.AWS_DEFAULT_ACCOUNT }}
  AWS_DEFAULT_REGION: ${{ secrets.AWS_DEFAULT_REGION }}
  ## DevOps environment variables
  DEVOPS_ACCOUNT_NAME: ${{ secrets.DEVOPS_ACCOUNT_NAME }}
  DEVOPS_ACCESS_ROLE: ${{ secrets.DEVOPS_ACCESS_ROLE }}
####----------------------------------------------------------------------------
jobs:
  generate-credentials:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
####----------------------------------------------------------------------------
      ## Updating AWS CLI (latest)
      - name: Updating AWS CLI (lastest)
        id: update-awscli
        run: |
          aws --version >/dev/null 2>&1 && {
              echo -e >&2 "AWS CLI is Installed ... Ok! ";
              which aws ;
              aws --version ;
            } ;
          echo -e "Upgrading AWS-CLI to version 2.0.40" ;
          ## https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html
          cd /tmp ;
          wget --quiet "https://${awscli_download}/${awscli_package}" \
               --directory-prefix=/tmp/ --output-document=awscliv2.zip ;
          unzip awscliv2.zip 1>/dev/null ;
          ls -l /usr/local/bin/aws ;
          sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update ;
          aws --version ;
        continue-on-error: false
      - name: Check On Failures
        if: steps.update-awscli.outputs.status == 'failure'
        run: |
          echo -e "Warning: Installation Failed [Status]: ${{ steps.update-awscli.outputs.status }}" ;
####----------------------------------------------------------------------------
      - name: AWS STS Assume Role
        uses: ./
        id: request_credentials
        with:
          session-timestamp: 'DevOpsPipeline--20200827193000'
          aws-default-account: ${AWS_DEFAULT_ACCOUNT}
          aws-default-region: ${AWS_DEFAULT_REGION}
          aws-access-key-id: ${AWS_ACCESS_KEY_ID}
          aws-secret-access-key: ${AWS_SECRET_ACCESS_KEY}
          devops-access-role: ${DEVOPS_ACCESS_ROLE}
          devops-account-name: ${DEVOPS_ACCOUNT_NAME}
        continue-on-error: false
      - name: Check On Failures
        if: steps.request_credentials.outputs.status == 'failure'
        run: |
          echo -e "Warning: Request Credentials Failed [Status]: ${{ steps.request_credentials.outputs.status }}" ;
####----------------------------------------------------------------------------
      ## Display Environment
      - name: Display Environment
        id: display_environment
        run: |
          echo -e "Displaying Enviroment Settings ..." ;
          echo -e "AWS Access Key-ID: ${AWS_ACCESS_KEY_ID}" ;
          echo -e "AWS Secret Access Key: ${AWS_SECRET_ACCESS_KEY}" ;
          echo -e "AWS Session Token: ${AWS_SESSION_TOKEN}" ;
####----------------------------------------------------------------------------
      ## Display IAM List-Users
      - name: Display IAM List-Users
        id: display_listusers
        run: |
          aws --profile default \
          --region ${AWS_DEFAULT_REGION} \
          iam list-users \
          --query 'Users[?UserName==`'${DEVOPS_ACCOUNT_NAME}'`]' ;
        continue-on-error: false
      - name: Check On Failures
        if: steps.display_listusers.outputs.status == 'failure'
        run: |
          echo -e "Warning: Credentials Validation Failed [Status]: ${{ steps.display_listusers.outputs.status }}" ;
```

**<span style="color:red">4</span>** -) The output will be like this: [Pipeline Execution](https://github.com/emvaldes/generate-credentials/actions?query=workflow%3A%22GitHub+Actions+-+AWS+STS+Generate+Credentials%22)

```console
Injecting Default User-Credentials into AWS-Credentials file: /home/runner/work/generate-credentials/generate-credentials/.aws/credentials

Initiating STS Assume Role request ...
aws --profile default --region *** sts assume-role --role-arn arn:aws:iam::***:role/*** --role-session-name DevOpsPipeline--20200827193000

Fetched STS Assumed Role Values:

Constructed Session Items [array]:
AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN AWS_TOKEN_EXPIRES

Obtaining Caller Identity (Default-Role):
{
    "UserId": "AIDASGOVRMFDVKZVUNYLN",
    "Account": "***",
    "Arn": "arn:aws:iam::***:user/***"
}

Injecting Credential: -> aws_access_key_id = ASIAx...xCG77
Injecting Credential: -> aws_secret_access_key = 4U0Vx...xgUjq
Injecting Credential: -> aws_session_token = IQoJx...xdTY=
Injecting Credential: -> x_principal_arn = arn:aws:iam::***:user/***
Injecting Credential: -> x_security_token_expires = 2020-08-29T19:08:49+00:00

Obtaining Caller Identity (Assumed-Role):
{
    "UserId": "AROAx...xMOJH:DevOpsPipeline--20200101000000",
    "Account": "***",
    "Arn": "arn:aws:sts::***:assumed-role/***/DevOpsPipeline--20200101000000"
}

Completed!
```

Happy Coding & Share your work!
