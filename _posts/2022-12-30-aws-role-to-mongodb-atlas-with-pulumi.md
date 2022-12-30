---
published: true
---
The other day i was working on a project that required using a NoSQL database; after a few weeks trying out AWS's documentDB and realizing that it requires more maintenance than expected(especially if you dont want to deal with VPC settings and service idle time) we ended up trying out MongoDB atlas.

As it was not straightforward to know how to do it by consulting the pulumi documentation, i decided to writedown this blog and a gist just in a case i need to do the same thing in the future.

These 2 segments of the MongoDB atlas documentation explains the steps quite clearly on how to set the process manually however the problem is how to automate the deployment especially when dealing with different roles from different REALM's / stages(staging, prod...)

- Unified AWS access [link](https://www.mongodb.com/docs/atlas/security/set-up-unified-aws-access/ "MongoDB atlas unified access to AWS")
- Passwordless authentication [link](https://www.mongodb.com/docs/atlas/security/passwordless-authentication/#set-up-passwordless-authentication-with-aws-iam-roles "Passwordless auth to mongoDB atlas with IAM role")

The gist with the whole automation code can be found [here](https://gist.github.com/kenseii/37f19a22af2ab084e89ab9d6817734a1 "iam role to atlas pulumi")
"iam role to mongodb-db-atlas using pulumi")

In case you are not using pulumi you can also get the same automation result using atlas's api however that would require some more work setting up the role in AWS with another tool.
