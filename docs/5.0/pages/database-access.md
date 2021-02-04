---
title: Database Access Guide
description: How to set up and configure Teleport Database Access with AWS Aurora PostgreSQL
---

# Database Access

Teleport can provide secure access to PostgreSQL and MySQL databases, while
improving both access control and visibility.

Some of the things you can do with Database Access include:

* Allow users to retrieve short-lived database credentials using single sign-on
  flow thus maintaining their organization-wide identity.
* Configure rich role-based access control policies for databases and implement
  custom [access workflows](./enteprise/workflow.md).
* Capture database access events as well as query activity in the audit log.

## Demo video

In the following demo video we connect to a PostgreSQL server with `psql` and
pgAdmin 4 after authenticating with Github, execute a few SQL queries and
observe them in the audit log.

<video autoPlay loop muted playsInline controls>
  <source src="https://goteleport.com/teleport/videos/database-access-preview/dbaccessdemo.mp4" type="video/mp4" />
  <source src="https://goteleport.com/teleport/videos/database-access-preview/dbaccessdemo.webm" type="video/webm" />
Your browser does not support the video tag.
</video>

## Getting started

In this guide we will use Teleport Database Access to connect to a PostgreSQL
flavored AWS Aurora database.

Here's an overview of what we will do:

1. Configure AWS Aurora database with IAM authentication.
2. Download and install Teleport and connect it to the Aurora database.
3. Connect to the Aurora database via Teleport.

## Step 1/3. Setup Aurora

In order to allow Teleport connections to an Aurora instance, it needs to support
IAM authentication.

If you don't have a database provisioned yet, create an instance of an Aurora
PostgreSQL in the [RDS control panel](https://console.aws.amazon.com/rds/home).
Make sure to choose "Standard create" database creation method and enable
"Password and IAM database authentication" in the Database Authentication dialog.

For existing Aurora instances, the status of IAM authentication is displayed on
the Configuration tab and can be enabled by modifying the database instance.

Next, create the following IAM policy attached to a user whose credentials a
Teleport process will be using to allow it to connect to the database:

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Action": [
             "rds-db:connect"
         ],
         "Resource": [
             "arn:aws:rds-db:<region>:<account-id>:dbuser:<resource-id>/*"
         ]
      }
   ]
}
```

!!! note "Resource ID"

    Database resource ID is shown on the Configuration tab of a particular
    database instance in RDS control panel, under "Resource id". For regular
    RDS database it starts with `db-` prefix. For Aurora, use the database
    cluster resource ID (`cluster-`), not individual instance ID.

Finally, connect to the database and create a database account with IAM auth
support (or update an existing one). Once connected, execute the following
SQL statements to create a new database account and allow IAM auth for it:

```sql
CREATE USER alice;
GRANT rds_iam TO alice;
```

For more information about connecting to the PostgreSQL instance directly,
see Amazon [documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ConnectToPostgreSQLInstance.html).

## Step 2/3. Setup Teleport

Teleport Database Access is available starting from `6.0.0-alpha.1` pre-release.

Download the appropriate version of Teleport for your platform from the table
below, or visit our [downloads page](https://goteleport.com/teleport/download).

{!docs/5.0/pages/preview/releases-table.md!}

!!! warning

    Note, pre-releases are not suitable for production usage!

Start Teleport using the following command and point it to your Aurora database
instance. Make sure to update the database endpoint and region appropriately.

```shell
sudo teleport start \
  --roles=proxy,auth,db \
  --db-name=aurora \
  --db-protocol=postgres \
  --db-uri=postgres-aurora-instance-1.abcdefghijklm.us-west-1.rds.amazonaws.com:5432 \
  --db-aws-region=us-west-1
```

!!! note "AWS credentials"

    The node where the Teleport process is started should have AWS credentials
    configured with the policy from [step 1](#step-13-setup-aurora).

Create a Teleport user that is allowed to connect to a particular database
(e.g. `postgres`) within the Aurora instance as a particular database account
(e.g. `alice`).

```shell
sudo tctl users add alice root \
  --db-names=postgres \
  --db-users=alice
```

## Step 3/3. Connect to database

Now that Aurora is configured with IAM authentication, Teleport is running and
the local user is created, we're ready to connect to the database.

Log into Teleport with the user we've just created. Make sure to use `tsh`
version `6.0.0-alpha.2` or newer that includes Database Access support.

For simplicity, we're using an `--insecure` flag to accept Teleport's
self-signed certificate. For production usage make sure to configure proxy
with a proper certificate/key pair. See Teleport's general
[quickstart guide](./quickstart.md#step-1c-configure-domain-name-and-obtain-tls-certificates-using-lets-encrypt).

```shell
tsh login --insecure --proxy=localhost:3080 --user=alice
```

Now we can inspect available databases and retrieve credentials for the
configured Aurora instance:

```shell
tsh db ls
tsh db login aurora
```

Finally, connect to the database using `psql` command shown in the output of
`tsh db login` command, which looks similar to this:

```shell
psql "service=<cluster>-aurora user=alice dbname=postgres"
```

## Next steps

Congratulations on completing the Teleport Database Access getting started
guide!

For the next steps, dive deeper into the topics relevant to your Database
Access use-case, for example:

* Learn how to connect to a [self-hosted database](./database-access/configuration.md#self-hosted-postgresql).
* Learn how to configure Database Access via Teleport [configuration file](./database-access/configuration.md#configure-teleport).
* Learn about Database Access [role-based access control](./database-access/rbac.md).
* See [frequently asked questions](./database-access/faq.md).
