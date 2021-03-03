---
id: working_with_data_sources
author: GoodData
sidebar_label: Working with Data Sources
title: Working with Data Sources
---

Creating a Data Source
-------

Create a Snowflake Data Source programmatically:

```ruby
# encoding: utf-8

require 'gooddata'

snowflake_ds_model = {
          'dataSource' => {
              'name' => 'My Data Source',
              'alias' => 'ds_alias',
              'prefix' => 'ds_prefix_',
              'connectionInfo' => {
                  'snowflake' => {
                      'url' => 'jdbc:snowflake://test',
                      'authentication' => {
                          'basic' => {
                              'userName' => 'testing_user',
                              'password' => '***'
                          }
                      },
                      'database' => 'db_test',
                      'schema' => 'schema_test',
                      'warehouse' => 'wh_test'
                  }
              }
          }
      }
client = GoodData.connect
snowflake_ds = client.create(GoodData::DataSource, snowflake_ds_model)
snowflake_ds.save

# After the Data Source is created, you can start working with it
# Using the Snowflake Data Source as source input in a process
project_test = client.projects('project_pid_testing')
process_test = project_test.deploy_process({
          name: 'Testing Process',
          type: 'LCM',
          component: {
              name: 'gdc-cm-bricks',
              version: '1'
          },
          dataSources:[{id: "#{snowflake_ds.id}"}]
      })
# Using the schema, database and password of a Snowflake Data Source as values of a schedule's parameters
schedule_test = process_test.create_schedule(
          '0 15 10 3 *',
          'Testing Schedule',
          params: {
              username: 'test_us',
              database: '${ds_alias.database}',
              schema: '${ds_alias.schema}'
          },
          hidden_params: {
              db_pass: '${ds_alias.password}'
          }
      )
```

Create an Amazon Redshift Data Source:

```ruby
# encoding: utf-8

require 'gooddata'

# Creating an Amazon Redshift Data Source with basic authentication
redshift_ds_model = {
            'dataSource' => {
                'name' => 'My Data Source',
                'alias' => 'ds_alias',
                'prefix' => 'ds_prefix_',
                'connectionInfo' => {
                    'redshift' => {
                        'url' => 'jdbc:redshift://test',
                        'authentication' => {
                            'basic' => {
                                'userName' => 'testing_user',
                                'password' => '***'
                            }
                        },
                        'database' => 'db_test',
                        'schema' => 'schema_test'
                    }
                }
            }
        }
client = GoodData.connect
redshift_ds = client.create(GoodData::DataSource, redshift_ds_model)
redshift_ds.save

# Creating an Amazon Redshift Data Source with access key and secret key authentication
redshift_iam_auth_model = {
            'dataSource' => {
                'name' => 'RedshiftDS_IAM',
                'alias' => GoodData::Rest::Connection.generate_string,
                'prefix' => 'prefix_',
                'connectionInfo' => {
                    'redshift' => {
                        'url' => 'jdbc:redshift://test',
                        'authentication' => {
                            'iam' => {
                                'dbUser' => 'user_iam_test',
                                'accessKeyId' => 'OWIUEWQIO723628',
                                'secretAccessKey' => 'abc123'
                            }
                        },
                        'database' => 'database_test',
                        'schema' => 'schema_test'
                    }
                }
            }
        }
client = GoodData.connect
redshift_iam_auth_ds = client.create(GoodData::DataSource, redshift_iam_auth_model)
redshift_iam_auth_ds.save
```

Create a Google BigQuery Data Source:

```ruby
# encoding: utf-8

require 'gooddata'

bigquery_ds_model = {
          'dataSource' => {
              'name' => 'My Data Source',
              'alias' => 'ds_alias',
              'prefix' => 'ds_prefix_',
              'connectionInfo' => {
                  'bigQuery' => {
                      'authentication' => {
                          'serviceAccount' => {
                              'clientEmail' => 'test@abc.com',
                              'privateKey' => '***'
                          }
                      },
                      'project' => 'project_test',
                      'schema' => 'schema_test'
                  }
              }
          }
      }
client = GoodData.connect
bigquery_ds = client.create(GoodData::DataSource, bigquery_ds_model)
bigquery_ds.save
```

We also support Data Sources for Amazon S3, Agile Data Warehouse Service (ADS) and arbitrary (generic) locations. 

Renaming a Data Source
-------

```ruby
require 'gooddata'

GoodData.connect
ds = GoodData::DataSource.from_id('datasource_id')
ds.name = "New data source name"
ds.save
```

Getting a Data Source by its ID or alias
-------

```ruby
# encoding: utf-8

require 'gooddata'

GoodData.connect
ds_from_id = GoodData::DataSource.from_id('datasource_id')
ds_from_alias = GoodData::DataSource.from_alias('datasource_alias')
```

Getting all Data Sources
-------

```ruby
# encoding: utf-8

require 'gooddata'

GoodData.connect
ds_list = GoodData::DataSource.all
```

Deleting a Data Source
-------

```ruby
# encoding: utf-8

require 'gooddata'

GoodData.connect
ds_from_id = GoodData::DataSource.from_id('datasource_id')
ds_from_alias = GoodData::DataSource.from_alias('datasource_alias')

ds_from_id.delete
ds_from_alias.delete
```
