## Elastic Beanstalk

EB is a platform as a service product within AWS. The Platform as a service is a type of service where vendor of the product handles all of the infrastructure. You only provides the code which is executed by vendor.

The user of the service provides code and EB handles environment to run that code.

![EB](./images/image-1.png)

When you create an environment within EB Application, you have to specify environment tiers. The options for this are web server tier and worker tier.

![Architecture](./images/image-2.png)

![Summary](./images/image-3.png)

## Elastic Deployment Policies

![Deployment Methods](./images/image-4.png)


All at once = New Application version is deployed onto instances within the elastic beanstalk at once. It causes outage.

![Once](./images/image-5.png)
![Rolling](./images/image-6.png)

![Rolling With new batch](./images/image-7.png)

![Immutable](./images/image-8.png)
![Traffic Splitting](./images/image-9.png)
![Traffic Splitting](./images/image-10.png)



## Elastic Beanstalk Lifecyle and RDS

If you want to use database service RDS within your Elastic Beanstalk Environment, you have two options 

1- You can create RDS Instance within environment. It is linked specifically to that environment.

![Within](./images/image-11.png)

2- Create RDS Instance outside of EB Environment.

![Outside](./images/image-12.png)


## Decouple Existing RDS within EB from EB Environment

![Decouple](./images/image-13.png)