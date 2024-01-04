## Service Control Policies

The service control policy is a json document. These service control policies can be attached to an organization as a whole by attaching them to root container. or they can be attached on or more organizational units or they can event be attached to individual account.

if you have service controle policies attached to management account, The management account of an organization is never affected by SCP's. As an securiy Best Practice, becuse the management account be restricted using SCP's, we should avoid using management account for any AWS resources.

![SCP](./images/image-1.png)

Service control policies don't grant permissions. They only limit what the identities within that account can or can't do.

![Permissions](./images/image-2.png)

## Deny List

![deny list](./images/image-3.png)

## Allow List Architecture

![Allow list](./images/image-4.png)

![Overlap](./images/image-5.png)

![Create OU](./images/image-6.png)

Add account to respective OU

![Move](./images/image-7.png)