---
title:  SQL external table connection strings
description: This article describes how to connect and authenticate to SQL external tables.
ms.reviewer: urishapira
ms.topic: reference
ms.date: 08/11/2024
monikerRange: "azure-data-explorer || microsoft-fabric"
---
# Azure SQL external table connection strings

> [!INCLUDE [applies](../../includes/applies-to-version/applies.md)] [!INCLUDE [fabric](../../includes/applies-to-version/fabric.md)] [!INCLUDE [azure-data-explorer](../../includes/applies-to-version/azure-data-explorer.md)]

To access an SQL external table, a connection string is provided during its creation. This connection string specifies the resource to be accessed and its authentication information.

Supported SQL external table types:

* Azure SQL Database
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure Cosmos DB.

For information on how to manage SQL external tables, see [Create and alter SQL external tables](../../management/external-sql-tables.md).

Regardless of the authentication method used, the principal must have the necessary permissions on the SQL database to perform the desired actions. For more information, see [Required permissions on the SQL database](#required-permissions-on-the-sql-database).

## Supported authentication methods by database type

The following table shows the supported authentication methods for each type of database acting as the source for the external table.

> [!NOTE]
> Where possible, the preferred authentication method is managed identity.

::: moniker range="microsoft-fabric"

| Authentication method | SQL Server | PostgreSQL | MySQL | Cosmos DB |
|--|--|--|--|--|
| [Microsoft Entra integrated (impersonation)](#azure-ad-integrated-impersonation) | :heavy_check_mark: | :x: | :x: | :x: |
| [Username and Password](#username-and-password) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

::: moniker-end

::: moniker range="azure-data-explorer"

| Authentication method | SQL Server | PostgreSQL | MySQL | Cosmos DB |
|--|--|--|--|--|
| [Microsoft Entra integrated (impersonation)](#azure-ad-integrated-impersonation) | :heavy_check_mark: | :x: | :x: | :heavy_check_mark: |
| [Managed identity](#managed-identity) | :heavy_check_mark: | :x: | :x: | :heavy_check_mark: |
| [Username and Password](#username-and-password) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

::: moniker-end

<a name='azure-ad-integrated-impersonation'></a>

## Microsoft Entra integrated (impersonation)

With this authentication method, the user or application authenticates via Microsoft Entra ID, and the same token is then used to access the SQL Server network endpoint. This method is supported for SQL Server and Cosmos DB.

To use Microsoft Entra integrated authentication (impersonation), add `;Authentication="Active Directory Integrated"` to the SQL connection string.

| Example |
|--|
| `"Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;"` |

::: moniker range="azure-data-explorer"

## Managed identity

Your query environment makes requests on behalf of a managed identity and uses its identity to access resources. This method is supported for SQL Server and Cosmos DB.

For a system-assigned managed identity, append `;Authentication="Active Directory Managed Identity"` to the connection string. For a user-assigned managed identity, append `;Authentication="Active Directory Managed Identity";User Id={object_id}` to the connection string.

| Managed identity type | Example |
|--|--|
| System-assigned | `"Server=tcp:myserver.database.windows.net,1433;Authentication="Active Directory Managed Identity";Initial Catalog=mydatabase;"` |
| User-assigned | `"Server=tcp:myserver.database.windows.net,1433;Authentication="Active Directory Managed Identity";User Id=00aa00aa-bb11-cc22-dd33-44ee44ee44ee;Initial Catalog=mydatabase;"` |

::: moniker-end

## Username and password

To authenticate with username and password, set the keywords `User ID` and `Password` in the connection string.

| Example |
|--|
| `"Server=tcp:myserver.database.windows.net,1433;User Id={myUserId};Password={myPlaceholderPassword};Initial Catalog=mydatabase;"` |

## Required permissions on the SQL database

For all authentication methods, the principal (or managed identity) must have the necessary permissions on the SQL database to perform the requested operation:

* Read permissions: table SELECT
* Write permissions:
  * Existing table: table UPDATE and INSERT
  * New table: CREATE, UPDATE, and INSERT

## Related content

* [Create and alter SQL external tables](../../management/external-sql-tables.md)
* [Authentication with the sql_request plugin](../../query/sql-request-plugin.md#authentication-and-authorization)
* [Authentication with the mysql_request plugin](../../query/mysql-request-plugin.md#authentication-and-authorization)
* [Authentication with the postgresql_request plugin](../../query/postgresql-request-plugin.md#authentication-and-authorization)
* [Authentication with the cosmosdb_request plugin](../../query/cosmosdb-plugin.md#authentication-and-authorization)
