# Week 5 — DynamoDB and Serverless Caching

## Required Home Work

  Data Modelling a Direct Messaging System using Single Table Design
Dynamodb Schema load CLI response
![Schema load](./assets/ddb-schema-load.png)


### Provisioning DynamoDB tables with Provisioned Capacity

Using boto3 client, I created `cruddur-messages` in dynamodb with provisioned capacity

```py
#!/usr/bin/env python3

import boto3
import sys

attrs = {
  'endpoint_url': 'http://localhost:8000'
}

if len(sys.argv) == 2:
  if "prod" in sys.argv[1]:
    attrs = {}

ddb = boto3.client('dynamodb',**attrs)

table_name = 'cruddur-messages'


response = ddb.create_table(
  TableName=table_name,
  AttributeDefinitions=[
    {
      'AttributeName': 'pk',
      'AttributeType': 'S'
    },
    {
      'AttributeName': 'sk',
      'AttributeType': 'S'
    },
  ],
  KeySchema=[
    {
      'AttributeName': 'pk',
      'KeyType': 'HASH'
    },
    {
      'AttributeName': 'sk',
      'KeyType': 'RANGE'
    },
  ],
  #GlobalSecondaryIndexes=[
  #],
  BillingMode='PROVISIONED',
  ProvisionedThroughput={
      'ReadCapacityUnits': 5,
      'WriteCapacityUnits': 5
  }
)

print(response) 
```

![Schema load](./assets/create-ddb-table.png)


###   Implementing DynamoDB query using Single Table Design

I implemented the follow dynamodb query

- List Tables using the aws cli
```sh
aws dynamodb list-tables --endpoint-url=http://localhost:8000 \
--query TableNames \
--output table
```
![Dynamodb](./assets/list-ddb-tables.png)


- Drop table using the aws cli
```sh
aws dynamodb delete-table --endpoint-url=http://localhost:8000 \
    --table-name cruddur-messages
```
![Dynamodb](./assets/delete-ddb-table.png)


- I scanned table using boto3 sdk

```py
#!/usr/bin/env python3

import boto3

attrs = {
  'endpoint_url': 'http://localhost:8000'
}
ddb = boto3.resource('dynamodb',**attrs)
table_name = 'cruddur-messages'

table = ddb.Table(table_name)
response = table.scan()

items = response['Items']
for item in items:
  print(item)
```
![Dynamodb](./assets/dynamodb-scantable.png)


###  Writing utility scripts to easily setup and teardown and debug DynamoDB data
I wrote the following scrits to setup dynamodb

- Create Dynamo DB table
```sh
#!/usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'

LABEL="DATABASE SCHEMA LOAD"

printf "${CYAN}${LABEL}${NO_COLOR}\n"

SCHEMA_PATH=$(realpath .)/db/schema.sql

if [ "$1" = "prod" ]; then
    echo "RUNNING IN PRODUCTION"
    CONNECTION_URL=$POSTGRESQL_PROD_CONNECTION_URL
else
    echo "RUNNING IN DEVELOPMENT"
    CONNECTION_URL=$POSTGRESQL_CONNECTION_URL
fi

psql $CONNECTION_URL cruddur < $SCHEMA_PATH
```

- Seed the table with conversation data
- Drop the table
- List tables
- Query the table to list conversations
- Query the table to get conversation

Show preloaded conversation
![Dynamodb](./assets/list-conversation.png)

Show newly created conversation
![Dynamodb](./assets/new-converstion.png)



  
    Utilizing a Global Secondary Index (GSI) with DynamoDB
    Rapid data modelling and implementation of DynamoDB with DynamoDB Local
