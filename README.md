# AWS SSM Parameter Store GitHub Action

This GitHub Action allows you to inject parameters into AWS Systems Manager Parameter Store. It was created to solve the problem of secret management when using infrastructure as code.

Terraform requires extra work in order to secure your state file because the secret is stored as plain text. This would require you to encrypt/decrypt the statefile when you are modifying state.

CloudFormation requires you to store it before you can reference it.

This action helps solve this problem by allowing the user to provide the path and the value that wants to be stored in a GitHub Action and the value can be passed in as a secret from the github actions. Pair this with the `Environments` feature and you can have a GitHub Action CI that also manages secrets.

## Inputs

### `aws-region`

**Required** The AWS Region to use.

### `ssm-path`

**Required** The value to be stored in the SSM Parameter Store Path.

### `ssm-value-description`

A description of the value being inserted into SSM.

### `ssm-value`

**Required** The value to be stored in the SSM Parameter Store Path.

### `ssm-value-type`

**Required** The value type to store the SSM Parameter (String | StringList | SecureString). Default `SecureString`.

### `ssm-value-overwrite`

**Required** TOverwrite the value of the SSM Parameter when inserting. Default `true`.

### `ssm-kms-key-id`

The AWS KMS Key ARN to use to encrypt the key. Default uses the AWS Provided KMS Key ID .

## Example usage

```yaml
name: store_secret
on:
  pull_request:
    branches:
      - master
jobs:
  add_to_ssm:
    runs-on: Ubuntu-20.04
    name: Store a Secret
    steps:
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1-node16
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: eu-west-1
      - name: Add hello SecureString to SSM
        uses: landtechnologies/action-aws-ssm-parameter-store@v2.0.0
        with:
          ssm-path: "/dwardu89/hello"
          ssm-value: ${{ secrets.WORLD }}
          aws-region: eu-west-1
```

## Required IAM Permissions

In order to use this, your AWS User/Role must allow the `ssm:PutParameter` action.

e.g.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PutSSMParameters",
      "Effect": "Allow",
      "Action": "ssm:PutParameter",
      "Resource": ["arn:aws:ssm:eu-west-1:1234567890:parameter/dwardu89/hello"]
    }
  ]
}
```
