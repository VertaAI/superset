# Checklists

```sql
select distinct model_version_id as Version_ID, rm.name as Registered_Model_Name,
  CASE
    WHEN rmv.stage = 0 THEN "Unknown"
    WHEN rmv.stage = 1 THEN "Development"
    WHEN rmv.stage = 2 THEN "Staging"
    WHEN rmv.stage = 3 THEN "Production"
    WHEN rmv.stage = 4 THEN "Archived"
    WHEN rmv.stage = 5 THEN "Unassigned"
  END AS rmv_stage,
  rmv.description as version_description
from development_registry.model_version_checklist_template_item_value cl
Join development_registry.model_version rmv on rmv.id = cl.model_version_id
join development_registry.registered_model rm on rm.id = rmv.registered_model_id
where cl.completed = 0
LIMIT 10000
```

# Endpoints
```sql
SELECT
  ep.id,
  ep.created,
  ep.path,
  ep.description
FROM
  development_deployment.endpoints ep
ORDER BY
  created DESC
LIMIT
  1000 -- select * from development_deployment.live_endpoints
```
# External Deployments
```sql
select * from development_registry.external_deployment
```

# Model Versions
```sql
SELECT
  id AS version_id,
  registered_model_id AS rmv_id,
  experiment_run_id AS experiment_run,
  time_created,
  FROM_UNIXTIME(time_created/1000) AS actually_created,
  stage,
  CASE
    WHEN stage = 0 THEN "Unknown"
    WHEN stage = 1 THEN "Development"
    WHEN stage = 2 THEN "Staging"
    WHEN stage = 3 THEN "Production"
    WHEN stage = 4 THEN "Archived"
    WHEN stage = 5 THEN "Unassigned"
  END AS rmv_stage
FROM
  development_registry.model_version
ORDER BY
  ID DESC
LIMIT
  10000
```

# Registered Models
```sql
SELECT
  id,time_created, name,
  CASE
    WHEN task_type = 1 THEN "Other"
    WHEN task_type = 2 THEN "Classification"
    WHEN task_type = 3 THEN "Clustering"
    WHEN task_type = 4 THEN "Detection"
    WHEN task_type = 5 THEN "Regression"
    WHEN task_type = 6 THEN "Transcription"
    WHEN task_type = 7 THEN "Translations"
    ELSE "Unspecified"
  END AS Task,  CASE
    WHEN data_type = 1 THEN "Other"
    WHEN data_type = 2 THEN "Audio"
    WHEN data_type = 3 THEN "Image"
    WHEN data_type = 4 THEN "Tabular"
    WHEN data_type = 5 THEN "Text"
    WHEN data_type = 6 THEN "Video"
    ELSE "None Specified"
  END AS Data
FROM
  development_registry.registered_model
ORDER BY
  id DESC
LIMIT
  1000
```
# Users

```sql
SELECT
  -- count(distinct email)
  email,
  FROM_UNIXTIME(user_created_timestamp/1000) AS join_date
FROM
  development_uac.organization_v2 orgs
  JOIN development_uac.user_group groups on orgs.all_users_group_id = groups.group_id
  join development_uac.user_details_autoincrement details on details.verta_id = groups.user_id
```
