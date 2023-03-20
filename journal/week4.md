# Week 4 — Postgres and RDS

## Provision RDS Instance

```sh
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username root \
  --master-user-password ******** \
  --allocated-storage 20 \
  --availability-zone us-east-1a \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection
```
![Progress DB Instance](./assets/progress-db-instance.png)

![Progress DB Instance Console](./assets/db-instance-console.png)

###     Temporarily stop an RDS instance

![Progress DB Instance Stopped](./assets/db-instance-stopped.png)


### Write several bash scripts for database operations
I created a `bin` folder in the `backend-flask` and added  the following bash scripts for database operations

**Shell script to connect to DB**
`db-connect`
```sh
#!/usr/bin/bash

if ["$1" = "prod"]; then
    echo "RUNNING IN PRODUCTION"
    CONNECTION_URL=$POSTGRESQL_PROD_CONNECTION_URL
else
    echo "RUNNING IN DEVELOPMENT"
    CONNECTION_URL=$POSTGRESQL_CONNECTION_URL
fi

psql $CONNECTION_URL
```

**Shell script to create DB**
`db-create`
```sh
#!/usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'

LABEL="CREATE CRUDDUR DATABASE"

printf "${CYAN}${LABEL}${NO_COLOR}\n"

DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<< "$POSTGRESQL_CONNECTION_URL")
psql $DB_CONNECTION_URL -c "CREATE DATABASE cruddur;"
```

**Shell script to drop to DB**
`dp-drop`

```sh
#!/usr/bin/bash 
echo "DROP CRUDDUR DATABASE"
DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<< "$POSTGRESQL_CONNECTION_URL")
psql $DB_CONNECTION_URL -c "DROP DATABASE cruddur;"
```
**Shell script to load schema into database**
`db-schema-load`
```sh
LABEL="DATABASE SCHEMA LOAD"

printf "${CYAN}${LABEL}${NO_COLOR}\n"

SCHEMA_PATH=$(realpath .)/db/schema.sql

if ["$1" = "prod"]; then
    echo "RUNNING IN PRODUCTION"
    CONNECTION_URL=$POSTGRESQL_PROD_CONNECTION_URL
else
    echo "RUNNING IN DEVELOPMENT"
    CONNECTION_URL=$POSTGRESQL_CONNECTION_URL
fi

psql $CONNECTION_URL cruddur < $SCHEMA_PATH
```

**Shell script to seed schema**
`db-seed`

```sh
#!/usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'

LABEL="DATABASE SEED"

printf "${CYAN}${LABEL}${NO_COLOR}\n"

SEED_PATH=$(realpath .)/db/seed.sql

CONNECTION_URL=$POSTGRESQL_CONNECTION_URL

if ["$1" = "prod"]; then
    echo "USING PRODUCTION URL"
    CONNECTION_URL=$POSTGRESQL_PROD_CONNECTION_URL
else
    echo "USING DEVELOPMENT URL"
fi
psql $CONNECTION_URL cruddur < $SEED_PATH

```

I connected to postgres with connection url

![Postgresql connection url](./assets/psql-connection-url.png)

![Postgresql Cruddur table](./assets/list-cruddur-tables.png)

View table in POSTGRES extension
![Postgresql table in extensions](./assets/seed-user-data.png)




    Remotely connect to RDS instance
    Programmatically update a security group rule
    Write several bash scripts for database operations
    Operate common SQL commands
    Create a schema SQL file by hand
    Work with UUIDs and PSQL extensions
    Implement a postgres client for python using a connection pool
    Troubleshoot common SQL errors
    Implement a Lambda that runs in a VPC and commits code to RDS
    Work with PSQL json functions to directly return json from the database
    Correctly sanitize parameters passed to SQL to execute
