# AWS STS
AWS IAM Role As A Service (RaaS)

name: GitHub Actions - AWS STS Assume Role
on: [push]
####----------------------------------------------------------------------------
env:
  ## AWS Enviroment variables
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  ## AWS Default variables
  AWS_DEFAULT_ACCOUNT: ${{ secrets.AWS_DEFAULT_ACCOUNT }}
  ## AWS Shared Credentials file
  AWS_SHARED_CREDENTIALS_FILE: ${{ github.workspace }}/credentials
  ## DevOps environment variables
  DEVOPS_ACCOUNT_NAME: ${{ secrets.DEVOPS_ACCOUNT_NAME }}
  DEVOPS_ACCOUNT_ID: ${{ secrets.DEVOPS_ACCOUNT_ID }}
  DEVOPS_ACCESS_ROLE: ${{ secrets.DEVOPS_ACCESS_ROLE }}
####----------------------------------------------------------------------------
jobs:
  credentials:
    runs-on: ubuntu-latest
    steps:
      - name: checkout
        uses: actions/checkout@v2
####----------------------------------------------------------------------------
      - name: AWS STS Assume Role
        uses: ./
        id: request_credentials
        with:
          verbose-mode: 'true'
          aws-access-key-id: ${{ env.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ env.AWS_SECRET_ACCESS_KEY }}
          aws-shared-credentials-file: "${{ env.AWS_SHARED_CREDENTIALS_FILE }}"
          aws-default-profile: 'default'
          aws-default-region: 'us-east-1'
          aws-default-account: ${{ env.AWS_DEFAULT_ACCOUNT }}
          session-timestamp: 'DevOpsPipeline--20200827193000'
          devops-access-role: ${{ env.DEVOPS_ACCESS_ROLE }}
          devops-account-id: ${{ env.DEVOPS_ACCOUNT_ID }}
          devops-account-name: ${{ env.DEVOPS_ACCOUNT_NAME }}
####----------------------------------------------------------------------------
