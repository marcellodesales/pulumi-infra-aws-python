# Pulumi Infra

* Creates the infrastructure for the project

> **Ref**: https://www.pulumi.com/docs/get-started/aws

# Setup

* AWS Setup
* Pulumi Account

## AWS Setup

* Create a new account or use existing
  * Use the `AWS_PROFILE` approach which is simple

```console
$ export AWS_PROFILE=marcellodesales-aws
```

* Make sure you have full connectivity

```console
$ aws s3 ls --debug
2022-07-10 00:37:58,280 - MainThread - awscli.clidriver - DEBUG - CLI version: aws-cli/2.2.21 Python/3.8.8 Darwin/21.5.0 exe/x86_64
2022-07-10 00:37:58,280 - MainThread - awscli.clidriver - DEBUG - Arguments entered to CLI: ['s3', 'ls', '--debug']
...
...
```

## Pulumi Setup

* Execute the login
* Establish the use of the profile above

```console
$ pulumi new aws-python                                                                                                                         Who called me was -zsh
Manage your Pulumi stacks by logging in.
Run `pulumi login --help` for alternative login options.
Enter your access token from https://app.pulumi.com/account/tokens
    or hit <ENTER> to log in using your browser                   :
We've launched your web browser to complete the login process.

Waiting for login to complete...
```

* After the login, you will be able to execute... 
* Make sure to use the profile

```console
pulumi config set aws:profile marcellodesales-aws
```

* This will change the config at ~/.pulumi

> cdat ~/.pulumi/credentials.json

```json
{
    "current": "https://api.pulumi.com",
    "accessTokens": {
        "https://api.pulumi.com": "pul-6927912193183d*********8beae1"
    },
    "accounts": {
        "https://api.pulumi.com": {
            "accessToken": "pul-692791288******560f8beae1",
            "username": "marcellodesales",
            "organizations": [
                "marcellodesales"
            ],
            "lastValidatedAt": "2022-07-10T00:07:29.084598-07:00"
        }
    }
}%
```

# Deploy Infra

* Just run the following:

```console
$ pulumi up --debug
Previewing update (4trade-serverless-infra-aws)

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/previews/9f300e52-35e9-4f81-9220-1e7b23eaa232

     Type                 Name                                Plan       Info
 +   pulumi:pulumi:Stack  4trade-4trade-serverless-infra-aws  create     120 debugs
 +   └─ aws:s3:Bucket     my-bucket                           create

Diagnostics:
  pulumi:pulumi:Stack (4trade-4trade-serverless-infra-aws):
    debug: registering resource: ty=pulumi:pulumi:Stack, name=4trade-4trade-serverless-infra-aws, custom=False, remote=False
    debug: registering resource: ty=aws:s3/bucket:Bucket, name=my-bucket, custom=True, remote=False
    debug: Waiting for outstanding RPCs to complete
    debug: RPCs successfully completed
    debug: Waiting for 33 outstanding tasks to complete
    debug: beginning rpc register resource
    debug: beginning rpc register resource
    debug: beginning rpc register resource outputs
    debug: resource registration prepared: ty=pulumi:pulumi:Stack, name=4trade-4trade-serverless-infra-aws
    debug: resource registration successful: ty=pulumi:pulumi:Stack, urn=urn:pulumi:4trade-serverless-infra-aws::4trade::pulumi:pulumi:Stack::4trade-4trade-serverless-infra-aws
```

# Add HTML to Bucket

* Create an html fle

```console
$ cat <<EOT > index.html
<html>
    <body>
        <h1>Hello, Pulumi!</h1>
    </body>
</html>
EOT 
```

Then, updated the main file with the block for it and the bucket to serve it as html (see commit)

```console
$ vim __main__.py
$ pulumi up -y
Previewing update (4trade-serverless-infra-aws)

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/previews/aea54484-6605-46fc-a226-7018177ab9b8

     Type                    Name                                Plan       Info
     pulumi:pulumi:Stack     4trade-4trade-serverless-infra-aws
 ~   ├─ aws:s3:Bucket        my-bucket                           update     [diff: +website]
 ~   └─ aws:s3:BucketObject  index.html                          update     [diff: ~acl,contentType]

Resources:
    ~ 2 to update
    1 unchanged

Updating (4trade-serverless-infra-aws)
  1 """An AWS Python Pulumi program"""
  2

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/updates/3

     Type                    Name                                Status      Info
     pulumi:pulumi:Stack     4trade-4trade-serverless-infra-aws
 ~   ├─ aws:s3:Bucket        my-bucket                           updated     [diff: +website]
 ~   └─ aws:s3:BucketObject  index.html                          updated     [diff: ~acl,contentType]

Outputs:
    bucket_name: "my-bucket-72340a9"

Resources:
    ~ 2 updated
    1 unchanged

Duration: 4s
```

* Confirm 

```console
$ pulumi up -y
Previewing update (4trade-serverless-infra-aws)

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/previews/005b060b-5662-4605-8553-7d8ea06985b7

     Type                 Name                                Plan
     pulumi:pulumi:Stack  4trade-4trade-serverless-infra-aws

Outputs:
  + bucket_endpoint: "http://my-bucket-72340a9.s3-website-us-east-1.amazonaws.com"

Resources:
    3 unchanged

Updating (4trade-serverless-infra-aws)

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/updates/4

     Type                 Name                                Status
     pulumi:pulumi:Stack  4trade-4trade-serverless-infra-aws

Outputs:
  + bucket_endpoint: "http://my-bucket-72340a9.s3-website-us-east-1.amazonaws.com"
    bucket_name    : "my-bucket-72340a9"

Resources:
    3 unchanged

Duration: 2s
```

* Curl the website

```console
$ pulumi stack output bucket_endpoint
http://my-bucket-72340a9.s3-website-us-east-1.amazonaws.com

$ curl $(pulumi stack output bucket_endpoint)
<html>
    <body>
        <h1>Hello, Pulumi!</h1>
    </body>
</html>
```

# Destroy all

```console
$ pulumi destroy
Previewing destroy (4trade-serverless-infra-aws)

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/previews/6d4b8b9d-592a-49cf-ba67-37c52e88e7ee

     Type                    Name                                Plan
 -   pulumi:pulumi:Stack     4trade-4trade-serverless-infra-aws  delete
 -   ├─ aws:s3:BucketObject  index.html                          delete
 -   └─ aws:s3:Bucket        my-bucket                           delete

Outputs:
  - bucket_endpoint: "http://my-bucket-72340a9.s3-website-us-east-1.amazonaws.com"
  - bucket_name    : "my-bucket-72340a9"

Resources:
    - 3 to delete

Do you want to perform this destroy? yes
Destroying (4trade-serverless-infra-aws)

View Live: https://app.pulumi.com/marcellodesales/4trade/4trade-serverless-infra-aws/updates/5

     Type                    Name                                Status
 -   pulumi:pulumi:Stack     4trade-4trade-serverless-infra-aws  deleted
 -   ├─ aws:s3:BucketObject  index.html                          deleted
 -   └─ aws:s3:Bucket        my-bucket                           deleted

Outputs:
  - bucket_endpoint: "http://my-bucket-72340a9.s3-website-us-east-1.amazonaws.com"
  - bucket_name    : "my-bucket-72340a9"

Resources:
    - 3 deleted

Duration: 3s

The resources in the stack have been deleted, but the history and configuration associated with the stack are still maintained.
If you want to remove the stack completely, run 'pulumi stack rm 4trade-serverless-infra-aws'.
```

* Confirm it was deleted

```console
$ curl $(pulumi stack output bucket_endpoint)
error: current stack does not have output property 'bucket_endpoint'
curl: try 'curl --help' or 'curl --manual' for more information
```
