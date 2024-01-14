## Ebextensions

Elastic beanstalk is based on the cloudformation to create environments and resources within those enbironments. You can .ebextensions folder to add new resources within EB environments.

![EBextension](./images/image-1.png)

## Enable HTTPS

![HTTPS](./images/image-2.png)

## Environment Cloning


#### Why clone environments?

![Clone](./images/image-3.png)

1- Clone Production environment into Test Environment for testing
2- Clone environment to check compatbility of an application with new version of platform. 

## EB and Docker

![Single Container](./images/image-4.png)

Multi container = Multiple containers per instance

MultiContainer = Elastic Beanstalk takes care of Amazon ECS tasks including cluster creation, task definition and task execution.

![Multi](./images/image-5.png)

![digram](./images/image-6.png)

![docker example](./images/image-7.png)