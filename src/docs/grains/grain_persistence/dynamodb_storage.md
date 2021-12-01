---
title: Amazon DynamoDB Grain Persistence
---

# Amazon DynamoDB Grain Persistence

## Installation

Install the [`Microsoft.Orleans.Persistence.DynamoDB`](https://www.nuget.org/packages/Microsoft.Orleans.Persistence.DynamoDB) package from NuGet.

## Configuration

Configure the Dynamo DB grain persistence provider using the `ISiloBuilder.AddDynamoDBGrainStorage` extension methods.

``` csharp
siloBuilder.AddDynamoDBGrainStorage(
    name: "profileStore",
    configureOptions: options =>
    {
        options.UseJson = true;
        options.AccessKey = /* Dynamo DB access key */;
        options.SecretKey = /* Dynamo DB secret key */;
        options.Service = /* Dynamo DB service name */;
    });
```
If your authentication method requires token/non-default profile name, you can also define those properties like so:
`cat ~/.aws/credentials`
```
[YOUR_PROFILE_NAME]
aws_access_key_id = ***
aws_secret_access_key = ***
aws_security_token = ***
aws_session_expiration = ***
aws_session_token = ***
```

``` csharp
siloBuilder.AddDynamoDBGrainStorage(
  name: "profileStore",
  configureOptions: options =>
  {
      options.UseJson = true;
      options.AccessKey = "***";
      options.SecretKey = "***";
      options.Service = "***";
      options.ProfileName = "***";
      options.Token = "***";
  });
```
For more information on AWS creditionals and named profiles:

Accessing credentials and profiles:
https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/net-dg-config-creds.html#creds-locate
Named profiles: 
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html
