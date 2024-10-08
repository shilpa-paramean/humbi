# Identity

This stack requires the following stacks:

* Audit

This stack applies to AWS accounts of the **workload** and **audit** types.

It requires that the identity-baseline CloudFormation is deployed to the AWS account.

## Deploying Identity Baseline

The identity-baseline template can be found at `_cf-templates/all-accounts/identity-baseline.cf.yml`

1. Login to the AWS Account
2. Go to CloudFormation
3. Click Create Stack
4. Select the file above, click Next
5. Stack name: identity-baseline
6. Fill the other parameters:
    - CreateInfraDeployUser: user to be used in some pipeline systems. Leave as default (false).
    - DNX: allow DNX access to this account. Change to true.
    - SAMLProviderArn or SAMLProviderDocument: will be used to enable Identity Provider and AWS integration. More details below.
    - OrgName: name of your organisation for purpose of using in resources naming. E.g.: dnx
    - TrustArns: leave as default.
7. Click Next, Next then Create Stack.

**SAMLProviderArn or SAMLProviderDocument**

These parameters are used in the "Trust Policy" of the IAM Role created by this stack. For the IAM Role to be accessible, the SAML Provider needs to be defined, which is an XML file generated by your SAML Provider (usually Azure AD or Google Workspace).

You can provide an ARN of an existing Identity Provider already deployed in the account - this is useful when using AWS SSO that automatically creates it.

Or the XML Document can be pasted into the Cloudformation, so it will create. Just be aware that there is a limit of 4 KB, so some XML documents like the Azure AD are not supported.

## Terraform Workspaces

One terraform workspace per AWS Account should be defined for this stack.

The workspace name is the account name.

## Features

### Job Function Roles

Creates IAM Roles for each Job Function Policy maintained by AWS. Full list at: [https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html]()

The option `create_default_roles` when `false` will only create the Administrator and ViewOnly roles.

### CI Deploy

When enabled, creates an IAM Role (and optionally an IAM User) that can be used for application deployments (ECS and Serverless).

This role only has permission to assume the CIDeployAccess role that does the deployments.

### CI Deploy Access

It complements the CI Deploy IAM Role as the Role that actually gives access to the resources to perform deployments.

### IAM Password Policy

Sets a password policy based on the highest security standards.

### AWS Support Roles

Sometimes required, it creates a role that can be used by AWS Support when needed.

### Azure List Roles User

Creates an IAM User with permission to list IAM roles to be given to Azure to assist with SSO setup.