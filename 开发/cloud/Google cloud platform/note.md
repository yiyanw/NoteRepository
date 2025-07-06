# GCP

# Regions and zones

* regions at least have three zones

# VM

* Machine family

  * general purpose - E2, N2, N2D, N1

  * Memory optimised - M1, M2 - high RAM

  * Compute Optimised - C2 - high computation performance

  * demo server

    * `apt update`
    * `apt install apache2` | `apt -y install apache2 `
    * (optional) `service apache2 start`

  * restart will lose public IP address

    * need to set a static IP address (VPC network - external ip addresses)
      * need to be within same region

  * Simplify process

    * startup script

      * ```bash
        #!/bin/bash
        apt update
        apt -y install apache2
        echo "Hello world form $(hostname) $(hostname -i)" > /var/www/html/index.html
        ```

    * instace templates

    * customised image
  
* preemtible VM

  * less cost
  * good if project has high fault tolerance 
  * good if project's workload is not immediate
  * maxium runtime is 24 hours
  
* spot VM

  * latest version preemtible VM
  * does not have a maximuym runtime
  
* Live Migration & Availability Policy

  * live migration
  * running instance is migrated to another host in the same zone
    * not support for GPUs and preemtible instances

  * availability policy
  * automatic restart
    * on host maintenance


# Load Balancing

* fully distributed 

* features
  * health check
  * auto scalling
  * global load balancing with single anycast IP
  
* settings
  * Backend - send requests to (instance group)
  * frontend - receive requests from 
  
* SSL/TLS termination

  * client to balancer - https
  * Balancer to server - http

* workflow

  * <img src="https://cloud.google.com/load-balancing/images/choose-lb.svg"/>
  * external 
    * HTTP or HTTPS
      * regional LB
      * Global or classic LB

    * TCP
      * SSL offload - SSL Proxy
      * global LB or IPv6 - TCP proxy
      * preserve client IPs - external network LB
      * Otherwise TCP proxy

    * UDP
      * external network LB

    * ESP or ICMP 
      * external network LB

  * internal
    * TCP or UDP
      * Internal TCP/UDP LB
    * HTTP or HTTPS
      * Internal HTTP(S) LB

* feature

  * external
    * https
    * global external http or https
    * proxy
    * 80 8080 or 443

  * internal
    * https
    * regional internal http or https 
    * proxy
    * 80 8080 443

  * Ssl proxy
    * global external tcp with ssl
    * proxy 

  * tcp proxy
    * global external tcp without ssl
    * proxy

  * External netwoprk tcp/udp
    * Regional external tcp or udp
    * Pass-through

  * internal tcp/udp
    * regional internal tcp/udp
    * pass-through

# Cloud Storage

* object storage
  * cannot partially update a object
  
* restful api to access

* CLI to access

  * gsutil

  * examples:

    * set project for gcloud

      * `gcloud config set project <PROJECT_NAME>`

    * create bucket 

      * `gsutil mb gs://<BKT_NAME>`

    * list all active bucket

      * `gsutil ls gs://<BKT_NAME>`

    * copy obecjt

      * `gsutil cp gs://<SRC_BKT_NAME>/<OBJECT_NAME> gs://<DEST_BKT_NAME>/<COPY_NAME>`
      * can use for uploading and downloading

    * Rewrite, mv, etc....

    * configing

      * `gstuil versioning set on/off gs://<BKT_NAME>`
      * `gstuil uniformbucketlevelaccess set on/off gs://<BKT_NAME>`
      * set access permissions for specific object
        * `gstuil acl `
        * `gsutil acl ch -u john.doe@example.com:WRITE gs://<BKT_NAME>/OBJ_PATH`
          * Permissions R(read) W(write) O(owner)
          * scope - User, allAuthentictedUsers, allUser(-u), Group(-g), Project(-p)
          * can use json_file to set it
            * `gsuitl acl set <JSON_FILE> gs://<BKT_NAME>`

      * Setup IAM role - 
        * `gsutil iam ch MBR_TYPE:MBR_NAME:IAM_ROLE gs://BKT_NAME` 
        * `gsutil iam ch user:me@myemail.com:objectCreator gs://BKT_NAME`
        * `gsutil iam ch allUsers:objectViewer gs://BKT_NAME` (set bucket to public)
      * signed url for temporary access
        * `gsutil signurl -d 10m <YOUR_KEY> gs://BKT_NAME/OBJ_PATH` (10 mins)

* storage classes

  * standard
  * Nearline - once a month
  * Cold line - once a quarter
  * Archive - once a year

* feature

  * key is unique in bucket
  * single object up to 5TB

* uploading and loading

  * simple upload
  * multipart upload
  * resumable upload
  * streamging transfers
  * parallel composite uploads - file is broken into small chunks and uploaded
  * simple download
  * streamiing download
  * Sliced object download

* versioning

  * prevent accidental deletion & provides history

* lifecycle management

  * automatically move files between storage classes
  * claesses
    * age, createdbefore, isLive, matchsStorageClass, numberOfNewerVersions ...

  * actions
    * setStorageClass
    * Deletion

* encryption

  * using google-managed encryption key by default
  * or using KMS key management system




# Authentication and Authorization with IAM (Identity and Access Management)

* Authentication (is it the right user?)
* authorization (do they have the right access)
* Identities can be
  * A GCP user
  * a Group of GCP Users
  * An Application running in GCP
  * An Application running in data center
  * Unauthenticated users
* provides different granular controls
  * specific action
  * specific resources
  * specific IP
  * Specific time window
* **Roles**: a set of permissions (to perform specific actions on specific resources) - (e.g. Storage Object Admin)
  * roles do not know about members
* **Policy**: assign roles to members - (e.g. colleague)

## Roles

* roles == permissions
* three types
  * basic roles - owner / editor / viewer
    * Viewer - Read
    * editor - viewer + edit
    * Owner - Edit + manage roles + billing
    * do not directly use basic roles
  * predefined roles - fine grained roles predefined and managed by google
    * Example: storage admin, storage object admin, storage object viewer, storage object creator
  * Custom roles

## policy

Binding things together

* principal (members)

  *  google account
  * service account
    * specific purpose (e.g. application on VM need access to cloud storage)
      * personal account is not a good idea for this scenario
  * google group - a set of google accounts and service accounts, this group will attach email
  * google workspace account
  * cloud identity domain
  * all authenticated users
  * all users

* role

* condition

  * conditions
  * Descriptions

  


# Big query

* use same SQL 
* Can directly access some public dataset
* Sandbox - https://youtube.com/watch?v=StEuT-pntZQ

## visualizing

* Looker Studio (formerly Data studio) - BI system
* Connected sheets - can directly import data from big query as excel format

## managing access with Cloud IAM

* Pre-defined role with specific rights
  * can assign to user or group
* three different granularities:
  1. table
  2. dataset
  3. project

## save and share bigquery

* Query history
  * moved to button of page
  * Have two types:
    * Personal history
    * Project history
* save query - there is a button named `save` in temp script tab
* share query - after saved the query, the `save` will become to `share` button

* query result
  * temporary table: can find result of each query in query history
  * permanent table:`More` -> `Query Settings` -> `Set a destination table for query results`
    * it will automatically update `destination table` after running the query
  * Save result: we can mannually save temporary table to file or table (or something else)

## view

* combination of multiple tables with simplified data for clients
* can use to hide unexpected data - authorised views

## protect data with authorised views

* create a dataset A with sensitive data
* create an other dataset B for authrorised views
* create new query to select non-sensitive data
  * save it as view in B
* grant permissions for users

## external data sources

* read google sheet in run time
  * can detect the modification in data and only update the modified part
* allow to do query with other platform  - Omni
* with cloud sql

# UDF - user-defined functions

* can be SQL or JavaScript

* temprary SQL UDF

  * ```sql
    CREATE TEMP FUNCTION test (text STRING)
    	RETURNS STRING
    	AS (REGEXP_REPLACE(LOWER(TRIM(text)), '[^a-zA-Z0-9]+', ''));
    	WITH strings AS
    		(SELECT '   HELLO, WORLD!!!   ' AS text
    		UNION ALL
    		SELECT '  I am $Special$ STrINg' AS test
    		UNION ALL
    		SELECT '  John, Doe  '  AS text)
    	SELECT text, test(text) AS processed_text
    	FROM strings;
    ```

* Permanent SQL UDF

  * create dataset to store pdf

  * ```sql
    CREATE OR REPLACE FUNCTION
    	`<project_name>`.<dataset_name>.<function_name> (text STRING)
    	RETURNS STRING
    	AS (REGEXP_REPLACE(LOWER(TRIM(text)), '[^a-zA-Z0-9]+', ''))
    ```

# how does bigquery store data

* use distributed file system - Colossus

# how does query procesing work in bigquery

* Structure
  * <img src="/Users/ian.wang/Documents/notes/NoteRepository/开发/cloud/Google cloud platform/note.assets/image-20231107130140810.png" alt="image-20231107130140810" style="zoom:50%;" />
  * High-available cluster compute - Dremel
  * Petabit network - Jupiter 
* Process
  * API request management
  * Lexing and parsing SQL
  * Catelog Resolution
  * Query Planning
  * Query plan Execution
  * Scheduling and dynamic planning

# Strategies for optimizing your bigquery queries

* 





# + Terraform

* workflow
  * scope
    * Identify the infrastructure for your project
  * author
    * write the configuration for your infrastructure 
  * initialize
    * install the plugins Terraform needs to manage the infrastrucure
  * plan
    * **preview** the changes Terraform will make to match your configuration
  * apply
    * make the planned changes
* Init, plan, apply
  * Init - 
    * first command aftering writing configuration files
    * initialize a working directory
    * download plugin
      * Local_file
      * random
  * plan
    * Creates execution plan
    * Doesn't change any infrastructure
  * apply
    * execute all changes & provision resource specific configuration

* `local_file` arguments
  
  * https://registry.terraform.io/providers/hashicorp/local/latest/docs/resources/file.html
  * used for creating file with content, permission, etcs ...
  
* `random provider`
  * https://registry.terraform.io/providers/hashicorp/random/latest/docs
  * is a logical provider - works entirely within terraform's logic
    * No interaction with other services
  * generate some random value for various types

* `output` 
  * Output specific value (resource) to console (or somewhere else)

* Variables

  * ```terraform
    variable <variableName> { # declaration
      type = <type>
      default = <value>
    }
    
    var.<variableName> # usage
    ```

  * types of variables

    * string

    * number

    * bool

    * list

      * ```terraform
        variable <xxx> {
        	type = list(string) 
        	default = ["red", "green", "blue"]
        }
        
        var.xxx[1] #usage
        ```

    * Tuple

      * ```terraform
        variable <xxx> {
        	type = tuple(["xx",number,bool])
        	default = ["red", 32, true]
        }
        
        var.xxx[1] #usage
        ```

    * map

      * ```terraform
        variable <xxx> {
        	type = map
        	default = {name = "Ankit", age = 32}
        }
        
        var.xxx['name'] #usage
        ```

    * set

    * Object

  * how to use variables

    * with default value - `var.<variableName>`

    * without default value

      * ```terraform
        variable filename {} # will ask when apply
        ```

      * `terraform apply -var "filename=sample.txt"`

    * export as env variable

      * `export TF_VAR_filename=sample.txt`

  * how to define variable

    * terraform.tfvars
    * Terraform.tfvars.json
    * *.auto.tfvars
    * *.auto.tfvars.json

  * Which variable will load first

    * Order
      * export
      * terraform.tfvars file
      * Variable.auto.tfcars file
      * Terraform apply -var "filename=sample.txt"
    * later commands will overwrite the previous config commands

* Multiple providers can be used in the single file

* dependency

  * implicit dependencies
    * use resources in other resources, terraform will automatically detect the dependencies
  * explicit dependencies
    * `depends_on = [...]`

* Lifecycle rules

  * Resource attributes

  * ```terraform
    resource random_integer name {
      min = 0
      max = 120
      lifecycle{ # default setting
        create_before_destroy = false
        prevent_destory = false
        ignore_changes = []
        }
      }
    ```

  * `create_before_destroy` - create the resource first and then destroy older (default is: destroy -> create)

    * Make sure service will not down for short period.

  * `prevent_destory` - prevents destroy of a resource (need manually operation for deleting)

  * `ignore_changes` - ignore changes to resource - specific tag or all

* provider version

  * right now version information is declared in `required_providers` block

  * https://developer.hashicorp.com/terraform/language/providers/requirements

  * demo

    * ```terraform
      terraform {
        required_providers {
          random = {
            source = "hashicorp/random"
            version = "3.6.0"
          }
        }
      }
      
      provider "random" {
        # Configuration options
      }
      ```

* read from local files as resource(data)

  * ```terraform
    data local_file <xxx> {
      filename = "<filename>"
    }
    ```

## set up for gcp

* import `google provider`

  * https://registry.terraform.io/providers/hashicorp/google/latest/docs

  * ```terraform
    terraform {
      required_providers {
        google = {
          source = "hashicorp/google"
          version = "5.9.0"
        }
      }
    }
    
    provider "google" {
      project = "{{YOUR GCP PROJECT ID}}"
      region  = "us-central1"
      zone    = "us-central1-c"
    }
    ```

* Multi-ways to authenticate with GCP

  * username/password

    * `gcloud auth login`

  * google cloud vm (cloud console)

  * service account

    * create a service account for terraform on GCP

    * create key for this service account

    * provide generated to google provider

      * ```terraform
        provider "google" {
          project = "{{YOUR GCP PROJECT ID}}"
          region  = "us-central1"
          zone    = "us-central1-c"
          credentials = "{{key file name}}"
        }
        ```

    * add required permissions to this service account

* create a storage bucket in GCP with terraform

  * ```terraform
    resource google_storage_bucket "GCS1" {
      name          = "bucket-form-tf-up-ianw"
      location      = "australia-southeast1"
    }
    ```

* gcloud

  * login `gcloud auth login`
  * Set project - `gcloud config set project testproj-375201`
  * check authentication list `gcloud auth list`
  * check configurations `gcloud config list`

* how to destroy all settings
  * `terraform destroy`

## Auth with Service Account

* create service account

* generate key on GCP Console

* download key file (json)

* add to provider 

  * ```terraform
    provider "google" {
      ...
      credentials = "{{key file name}}"
    }
    ```

  * 

## VPC

* VPC - Virtual Private Network
* three different types:
  * default
  * auto mode
  * custom mode
* `google_compute_network`'
* `google_compute_subnetwork` - used with custom VPC
* `google_compute_firewall` - access control

## GCE

* GCE - Google Compute Engine

  * https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/compute_instance

* all available images

  * `gcloud compute images list`

* service account

  * ```terraform
    resource "google_compute_instance" "<xxx>" {
      ...
      service_account {
        email = "<xxx>"
        scopes = ["<access>"]
      }
      ...
      }
    ```

* attach disk

  * `google_compute_disk`
  * `google_compute_attached_disk`

## Cloud Run

* some demo images - https://console.cloud.google.com/gcr/images/google-samples/GLOBAL/hello-app

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/data-sources/iam_policy

  * ```terraform
    # public access
    data "google_iam_policy" "pub-1" {
      binding {
        role = "roles/run.invoker"
        members = [ "allUsers" ]
      }
    }
    ```

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/cloud_run_service

  * ```terraform
    resource "google_cloud_run_service" "run_app_from_tf" {
      location = "australia-southeast1"
      name     = "run-app-from-tf"
    
      template {
        spec {
          containers {
            image = "gcr.io/google-samples/hello-app:1.0"
            # image = "gcr.io/google-samples/hello-app:2.0"
          }
        }
      }
    
      traffic {
        percent         = 100
        latest_revision = true
      }
    }
    ```

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/data-sources/cloud_run_service_iam_policy

  * ```terraform
    resource "google_cloud_run_service_iam_policy" "pub_access" {
      policy_data = data.google_iam_policy.pub-1.policy_data
      service     = google_cloud_run_service.run_app_from_tf.name
      location    = google_cloud_run_service.run_app_from_tf.location
    }
    ```

## Cloud Function

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/cloudfunctions_function

* ```hcl
  resource "google_storage_bucket" "bucket" {
    name     = "test-bucket"
    location = "US"
  }
  
  resource "google_storage_bucket_object" "archive" {
    name   = "index.zip"
    bucket = google_storage_bucket.bucket.name
    source = "./path/to/zip/file/which/contains/code"
  }
  
  resource "google_cloudfunctions_function" "function" {
    name        = "function-test"
    description = "My function"
    runtime     = "nodejs16"
  
    available_memory_mb   = 128
    source_archive_bucket = google_storage_bucket.bucket.name
    source_archive_object = google_storage_bucket_object.archive.name
    trigger_http          = true
    entry_point           = "helloGET"
  }
  
  # IAM entry for all users to invoke the function
  resource "google_cloudfunctions_function_iam_member" "invoker" {
    project        = google_cloudfunctions_function.function.project
    region         = google_cloudfunctions_function.function.region
    cloud_function = google_cloudfunctions_function.function.name
  
    role   = "roles/cloudfunctions.invoker"
    member = "allUsers"
  }
  ```

## BigQuery

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/bigquery_dataset

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/bigquery_table

* ```terraform
  resource google_bigquery_dataset ds_from_tf {
    dataset_id = "ds_from_tf"
  }
  
  resource google_bigquery_table table_from_tf {
    dataset_id = google_bigquery_dataset.ds_from_tf.dataset_id
    table_id   = "table_from_tf"
  }
  ```

## PubSub

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/pubsub_topic

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/data-sources/pubsub_subscription

* ```terraform
  resource google_pubsub_topic topic_from_tf_ian {
    name = "topic_from_tf_ian"
    schema_settings {
      schema = "projects/testproj-375201/schemas/schema-1"
      encoding = "JSON"
    }
  }
  
  resource google_pubsub_subscription sub_from_tf_ian {
    name  = "sub_from_tf_ian"
    topic = google_pubsub_topic.topic_from_tf_ian.name
  }
  ```

## Spanner (Relational DB)

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/spanner_instance

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/spanner_database

* ```terraform
  resource google_spanner_instance spanner_from_tf_ian {
    config       = "regional-australia-southeast1"
    display_name = "spanner_from_tf_ian"
    name = "spanner-from-tf-ian"
    num_nodes = 1
    labels = {
      "env" = "learning-tf"
    }
  }
  
  resource google_spanner_database spanner_db_from_tf_ian {
    instance = google_spanner_instance.spanner_from_tf_ian.name
    name = "spanner-db-from-tf-ian"
    deletion_protection = false
  }
  ```

## Cloud SQL (sql native relational db)

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/sql_database_instance

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/sql_database

* ```terraform
  resource google_sql_database_instance sql_from_tf_ian {
    region = "australia-southeast1"
    database_version = "MYSQL_8_0"
    settings {
      tier = "db-f1-micro"
    }
  }
  
  resource google_sql_database sql_db_from_tf_ian {
    instance = google_sql_database_instance.sql_from_tf_ian.name
    name     = "sql-db-from-tf-ian"
  }
  ```

## BigTable

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/bigtable_instance
* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/bigtable_table

* ```terraform
  resource google_bigtable_instance bigtable_from_tf_ian {
    display_name = "bigtable from tf"
    name = "bigtable-from-tf-ian"
    cluster {
      cluster_id = "bt-cluster-from-tf-ian"
      num_nodes = 1
    }
    deletion_protection = false
  }
  
  resource google_bigtable_table bt_table_from_tf_ian {
    instance_name = google_bigtable_instance.bigtable_from_tf_ian.name
    name          = "bt-table-from-tf-ian"
  }
  ```

## MemoryStore

* low latency memory store (redis)

* https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/redis_instance

* ```terraform
  resource google_redis_instance ms-from-tf-ian {
    memory_size_gb = 2
    name           = "ms-from-tf-ian"
  }
  ```

## Some key words

* Resource - can be entity or action
  * example of entity: `resource "google_compute_disk"`
  * Example of action: `resource "google_compute_attached_disk"`

* Dynamic - used to create multiple customised blocks
  * https://developer.hashicorp.com/terraform/language/expressions/dynamic-blocks
