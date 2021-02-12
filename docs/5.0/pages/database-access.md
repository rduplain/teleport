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

## Demo

In the following demo video we connect to a PostgreSQL server with `psql` and
pgAdmin 4 after authenticating with Github, execute a few SQL queries and
observe them in the audit log.

<video autoPlay loop muted playsInline controls>
  <source src="https://goteleport.com/teleport/videos/database-access-preview/dbaccessdemo.mp4" type="video/mp4" />
  <source src="https://goteleport.com/teleport/videos/database-access-preview/dbaccessdemo.webm" type="video/webm" />
Your browser does not support the video tag.
</video>

## Getting Started

Configure Database Access from scratch in a 10 minute [Getting Started](./database-access/getting-started.md)
guide.

## Guides

The following guides are available for configuring supported databases and
deployments:

* [Self-hosted PostgreSQL](./database-access/postgres-self-hosted.md)
* [Self-hosted MySQL](./database-access/mysql-self-hosted.md)
* [AWS RDS/Aurora PostgreSQL](./database-access/postgres-aws.md)
* [AWS RDS/Aurora MySQL](./database-access/mysql-aws.md)

## Resources

To learn more about configuring role-based access control for Database Access,
check out [RBAC](./database-access/rbac.md) section.

[Architecture](./database-access/architecture.md) provides a more in-depth
look at Database Access internals such as networking and security.

See [Reference](./database-access/reference.md) for detailed overview of
Database Access related configuration and CLI commands.

## FAQ

Finally, check out [Frequently Asked Questions](./database-access/faq.md).
