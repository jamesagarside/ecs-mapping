Corelight ECS Ingest Pipeline Mapping
=======================================


Overview
--------
The Elastic Common Schema (https://github.com/elastic/ecs) is a way to unify field names across multiple data sources in Elastic.  
This repository uses Elastic ingest pipelines to ingest data from Corelight or Zeek data and maps the fields to the Elastic Common Schema.  
A few notes on how the ingest pipelines work:
- Requires Elasticsearch index templates from the [Corelight ECS index templates](https://github.com/corelight/ecs-templates) repository
- Field names are replaced in this operation
- Supports both open source Zeek and Corelight
  - Corelight => v21
  - Zeek => 4.x
- The ingest pipelines can be uploaded directly to Elasticsearch (API) or through Kibana (manually)
- Once done, the pipelines apply to new data only and should be done using a new index.  This is because (due to how Elastic works) if the ingestion is done in a mixed index it will cause problems with field name conflicts between old and new field names. If this is a particular concern, you can work around it by having two exports to Elastic - one to a new index with the direct Elastic integration (which will have the new ECS field names) and one to the old index using the JSON exporter (which will have the original Zeek field names).
- Using ingest pipelines will increase CPU consumption within your Elasticsearch nodes due to the real time field mapping.
- ECS version [1.12](https://www.elastic.co/guide/en/ecs/1.12/ecs-reference.html)

As always, we are looking forward to any suggestions and ideas for improvement you have!


License
-------
The pipeline files and automation script are open-source under a BSD license. See ``COPYING``for details.


Installation
------------
Automatic installation (recommended)
 1. Clone the Corelight ECS Ingest Pipeline Mappings repository from GitHub to your workstation or jumpbox.
 2. In ecs_mapping/automatic_install/, locate the template files (template_corelight_*). Edit each file,
       changing the values of the index_patterns field according to your environment.
 3. Run pipelines_import.py (Python3) from ecs_mapping/automatic_install/.
       Note: Corelight recommends using the Python3 script for installation. However if you can’t run Python3 in your environment, there’s also a bash script that executes  the installation (ecs_mapping/automatic_ install/pipelines_import.sh).
 4. Respond to the configuration prompts to complete the installation.
 5. Configure your Corelight Sensor to send events to the new Elasticsearch index.
 6. Load the elasticsearch index templates from the [Corelight ECS Elasticsearch Templates](https://github.com/corelight/ecs-templates) repository. Make sure to use the templates from within the "ingest_pipelines" folder as well.

Manual installation
1. In the Kibana sidebar, open Dev Tools to access the console.
2. In a separate tab, open the manual_install directory in the ecs_mapping repository.
3. Copy the contents of zeek-enrichment-conn-dictionary into the Kibana console and click the play button to execute the request. 
  This command imports enriched tables.
4. Execute the enriched tables using this command in the Kibana console.
   POST /_enrich/policy/zeek-enrichment-conn-policy/_execute
5. In the ecs-mapping repository, locate the template files (template_corelight_*). One at a time, copy the contents of each into the Kibana console. 
  Change the values of the index_patterns fields according to your environment and execute the request.
6. Copy the contents of the corelight_general_pipeline file from the ecs-mapping repository into the Kibana console and execute the request. 
  This command maps the ECS datasets to the appropriate Corelight mapping file.
7. One at a time, copy the contents of each pipeline file (corelight_*_pipeline) into the Kibana console and execute the request. 
   These commands install each pipeline to your environment.
8. Configure your Corelight Sensor to send events to the new Elasticsearch index.
9. Load the elasticsearch index templates from the [Corelight ECS Elasticsearch Templates](https://github.com/corelight/ecs-templates) repository. Make sure to use the templates from within the "ingest_pipelines" folder as well.
    
 In this version both reduced and non reduced logs are in the same pipeline

**Note**
You can change the number of shards and the lifecycle policy in template_corelight_ base_settings.

**With the latest update there was a change to maintain backward compatibility you will need to do the following**

network.vlan.inner.id should be network.inner.vlan.id use an alias or elastic run time fields for backwards compatibility
