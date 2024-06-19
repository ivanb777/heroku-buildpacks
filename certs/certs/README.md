### CA Bundles

The `certs` dir contains the RDS CA certs and Amazon Trust CA bundles. As the name suggests, the RDS `rds-global-bundle.pem` bundle is used for establishing secure connections to the RDS instances, whilst the Amazon Trust `amazon-trust-ca-bundle.crt` budle is used for Redshift SSL connection.

For more information about the `rds-global-bundle.pem` visit https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html#UsingWithRDS.SSL.CertificatesAllRegions (Please note: that the AWS `global-bundle.pem` bundle was renamed to `rds-global-bundle.pem`.)

For more information regarding the `amazon-trust-ca-bundle.crt` visit https://docs.aws.amazon.com/redshift/latest/mgmt/connecting-ssl-support.html


Links to other useful information:
* https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL-certificate-rotation.html
* https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html


### Usage

In some cases, the RDS CA certs may already be included with client package, but this is not the rule. Below is a simple guide to help with using the included certs.

In short, we want the clients to use the appropriate CA certs, and to set  ssl mode to `verify-full` for PG and/or `VERIFY_CA` for MySql.

You will need to determine how these are implemented by the client that you are using.

In terms of instances and node packages we've been using so far, the following values/parameters provide a good reference.

RDS Postgres instance:
* `"pg": "^8.11.5"`
* `sslmode=verify-full`
* `sslrootcert=certs/rds-global-bundle.pem`

Redshift instance:
* `"pg": "^8.11.5"`
* `sslmode=verify-full`
* `sslrootcert=certs/amazon-trust-ca-bundle.crt`

RDS MySql:
* `mysql2` >= `v3.9.3` has the up-to-date Amazon RDS SSL CA cert, so you can just use its ssl profile as
    * `ssl=Amazon+RDS`
* otherwise, check the client's implementation for specifying the following options
    * `ssl-ca:certs/amazon-trust-ca-bundle.crt`
    * `ssl-mode=VERIFY_CA`
    

NB: you may need to `URI encode` the CA path, eg. in case that these are passed as connection string.