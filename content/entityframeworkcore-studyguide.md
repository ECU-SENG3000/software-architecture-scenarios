# Entity Framework Core Study Guide

## What is Entity Framework?
- Modern, open-source, and cross-platform object-relational mapper (ORM)  
- Enables .NET developers to work with databases using objects  
- Supports many database engines 

### Modeling Approaches
- **Code First** – create models in code that generate and maintain DB schema  
- **Database First** – generate models for an existing database  
- **Model First** – visually design DB and models  
- Allows queries using LINQ  
  - Example:  
    ```csharp
    db.Users.Where(u => u.name == "Brian").First()
    ```

---

## Why Use an ORM?
- No need to wait for DBA actions  
- Faster deployments  
- Easily set up multiple environments  
- Developers can have local copies of the database  
- Seed data is part of source code  
- Less boilerplate code  
- Maintain code in one place  
- No separate SQL to maintain  
- Easily switch databases (e.g., SQLite → PostgreSQL)  
- Prevent schema changes from breaking code  
- Consistent/reusable data access  
- Easier refactoring  
- Database agnostic  
- Object-oriented approach  
- Change tracking  
- SQL injection protection  
- LINQ support  

---

## Entity Framework – Code First Key Elements
- Install required NuGet packages  
- Define at least one model (models/entities define table schema)  
- Connection string (usually in `appsettings.json`)  
- Create DB context class  
  - Inherit from `DbContext`  
  - Pass options to base constructor  
  - Use `DbSet<>` for every model/table  
- Add table seeding to `OnModelCreating` event handler  
- Register `DbContext` for dependency injection  

---

## Current List of DB Providers
- SQL Server  
- Azure SQL  
- SQLite  
- In-Memory DB  
- Cosmos  
- PostgreSQL  
- MySQL  
- MariaDB  
- Oracle  
- Google Cloud  
- MongoDB  
- Couchbase  
- DB2  
- BigCommerce  
- MS Dynamics  
- FreshBooks  
- Magento  
- Mailchimp  
- QuickBooks  
- FileContextCore  
- FileBaseContext  
- Salesforce  
- SugarCRM  
- Zoho  
- Apache Kafka  
- InterBase  
- Firebird  
- Access  
- Teradata  
- Snowflake  
- Informix  
- Spanner  

---

## Required Packages for Entity Framework
Using **dotnet CLI**:
```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Relational
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet add package Microsoft.EntityFrameworkCore.Design

# Plus the database provider specific package
# e.g.
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
# or
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
# or
dotnet add package Aspire.Npgsql.EntityFrameworkCore.PostgreSQL
# or etc.
```


## EF Tool

### Purpose
- Required to perform migrations and database updates

### Installation
```bash
dotnet tool install --global dotnet-ef
```

### Creating a Migration
- After making a change to a model or configuration:
```bash
dotnet ef migrations add MigrationName
```
- Newly created migration file is placed in the Migrations folder

### Updating the Database
```bash
dotnet ef database update
```
- All existing migrations that have not been applied yet will be applied
- Migrations are applied in the order they were created


---

# EF Core Providers & NuGet Packages

| Database Provider | NuGet Package(s) |
|-------------------|------------------|
| SQL Server        | `Microsoft.EntityFrameworkCore.SqlServer` |
| Azure SQL         | `Microsoft.EntityFrameworkCore.SqlServer` |
| SQLite            | `Microsoft.EntityFrameworkCore.Sqlite` |
| PostgreSQL        | `Npgsql.EntityFrameworkCore.PostgreSQL` |
| Aspire PostgreSQL | `Aspire.Npgsql.EntityFrameworkCore.PostgreSQL` |
| MySQL             | `Pomelo.EntityFrameworkCore.MySql` |
| MariaDB           | `Pomelo.EntityFrameworkCore.MySql` |
| Oracle            | `Oracle.EntityFrameworkCore` |
| Cosmos DB         | `Microsoft.EntityFrameworkCore.Cosmos` |
| In-Memory DB      | `Microsoft.EntityFrameworkCore.InMemory` |
| MongoDB           | `MongoDB.EntityFrameworkCore` |
| Couchbase         | `Couchbase.EntityFrameworkCore` |
| DB2               | `EntityFrameworkCore.Db2` |
| Informix          | `EntityFrameworkCore.Informix` |
| Firebird          | `FirebirdSql.EntityFrameworkCore.Firebird` |
| InterBase         | `EntityFrameworkCore.InterBase` |
| Access            | `EntityFrameworkCore.Jet` |
| Snowflake         | `Snowflake.Data.EntityFrameworkCore` |
| Teradata          | `Teradata.EntityFrameworkCore` |
| Google Cloud Spanner | `Google.Cloud.EntityFrameworkCore.Spanner` |

---

### Notes
- Always install **base EF Core packages** first:  
  - `Microsoft.EntityFrameworkCore`  
  - `Microsoft.EntityFrameworkCore.Relational`  
  - `Microsoft.EntityFrameworkCore.Tools`  
  - `Microsoft.EntityFrameworkCore.Design`  
- Then add the **provider-specific package** from the table above.  
- Some providers (e.g., MySQL, MariaDB) share the same NuGet package.  
- Community-driven providers (like Firebird, Informix, DB2) may have different levels of support and version compatibility.  

