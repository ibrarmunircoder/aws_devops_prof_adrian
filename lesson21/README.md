## Cross-Stack References

![Cross-Satck](./images/image-1.png)

if you are deploying EC2 in appstack 1 and appstack 2, they couldn't natively reference subnets created by the shared VPC. 

![Corss-Stack](./images/image-2.png)
![Corss-Stack](./images/image-3.png)

## StackSets

Stackset is a feature of cloudformation which allows us to craete, update and delete infrastructure across many regions or many accounts.

![StackSet](./images/image-4.png)
![StackSet](./images/image-5.png)
![StackSet](./images/image-6.png)

Concurrent Account: How many AWS Accounts can be used at the same time? The more concurrent accounts that you define the faster the resources will be deployed as part of stacksets.

Failure Tolerance: Failure tolerance is amount of individual deployments which can fail before the stackset itself is viewed as failed.

Retail Stack: By default, the stack instance will be deleted when you delete stackset. if you want to retain those stack instance within the target accounts, you can define it.

## CloudFormation Deletion Policy

if you delete a logical resource from within a temaplate, apply that template to an existing stack or if you delete a stack entirely, the default behaviour of cloudformation is to delete corresponding physical resource.

![Deletion Policy](./images/image-7.png)
![Deletion Policy](./images/image-8.png)

## CloudFormation Stack Roles:

By default, the cloudformation uses the permissions of an identity who is creating the stack. Stack roles allow you to assume role tol gain permissions to create resources without having to have permissions to interact with AWS services.

![Roles](./images/image-9.png)
![Roles](./images/image-10.png)