---
subcategory: "Postgres"
---
# databricks_postgres_database Resource
[![Private Preview](https://img.shields.io/badge/Release_Stage-Private_Preview-blueviolet)](https://docs.databricks.com/aws/en/release-notes/release-types)



## Example Usage

### Basic Database Creation

```hcl
resource "databricks_postgres_database" "app_db" {
  parent      = databricks_postgres_branch.production.name
  database_id = "my-app-db"

  spec = {
    postgres_database = "my_app_db"
  }
}
```

### Database with Explicit Owner

```hcl
resource "databricks_postgres_database" "analytics_db" {
  parent      = databricks_postgres_branch.dev.name
  database_id = "analytics"

  spec = {
    postgres_database = "analytics"
    role              = databricks_postgres_role.my_admin_role.name
  }
}
```

-> **Note** Every Lakebase project automatically creates a `production` branch and a default database. If you only need one database on the production branch, you may not need to create an additional `databricks_postgres_database` resource.

## Arguments
The following arguments are supported:
* `parent` (string, required) - The branch containing this database.
  Format: projects/{project_id}/branches/{branch_id}
* `database_id` (string, optional) - The ID to use for the Database, which will become the final component of
  the database's resource name.
  This ID becomes the database name in postgres.
  
  This value should be 4-63 characters, and only use characters available in DNS names,
  as defined by RFC-1123
  
  If database_id is not specified in the request, it is generated automatically
* `spec` (DatabaseDatabaseSpec, optional) - The desired state of the Database
* `provider_config` (ProviderConfig, optional) - Configure the provider for management through account provider.

### ProviderConfig
* `workspace_id` (string,required) - Workspace ID which the resource belongs to. This workspace must be part of the account which the provider is configured with.

### DatabaseDatabaseSpec
* `postgres_database` (string, optional) - The name of the Postgres database.
  
  This expects a valid Postgres identifier as specified in the link below.
  https://www.postgresql.org/docs/current/sql-syntax-lexical.html#SQL-SYNTAX-IDENTIFIERS
  Required when creating the Database.
  
  To rename, pass a valid postgres identifier when updating the Database
* `role` (string, optional) - The name of the role that owns the database.
  Format: projects/{project_id}/branches/{branch_id}/roles/{role_id}
  
  To change the owner, pass valid existing Role name when updating the Database
  
  A database always has an owner

### DatabaseDatabaseStatus
* `postgres_database` (string, optional) - The name of the Postgres database
* `role` (string, optional) - The name of the role that owns the database.
  Format: projects/{project_id}/branches/{branch_id}/roles/{role_id}

## Attributes
In addition to the above arguments, the following attributes are exported:
* `create_time` (string) - A timestamp indicating when the database was created
* `name` (string) - The resource name of the database.
  Format: projects/{project_id}/branches/{branch_id}/databases/{database_id}
* `status` (DatabaseDatabaseStatus) - The observed state of the Database
* `update_time` (string) - A timestamp indicating when the database was last updated

## Import
As of Terraform v1.5, resources can be imported through configuration.
```hcl
import {
  id = "name"
  to = databricks_postgres_database.this
}
```

If you are using an older version of Terraform, import the resource using the `terraform import` command as follows:
```sh
terraform import databricks_postgres_database.this "name"
```