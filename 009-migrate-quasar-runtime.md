

# Migrate Quasar's jobs runtime

We want to simplify our automated job's runtime by migrating from Jenkins (EC2) to a simpler alternative (Hosted).

**Discussion:** <https://github.com/DoSomething/rfcs/pull/9>

## Problem

The most challenging task for data engineers is to build the adequate plumbing necessary in order to provide analysts with clean and timely data. We identify the sources of the data, extract it, and transport it to our warehouses. Once there, we transform it from it's raw form (dirty) into analysis ready data (clean). This process is repeated per data source, is known as ETL orchestration.

A central part of this orchestration is to schedule and run jobs in an automated fashion. We use Jenkins for this. Although we have adopted better processes and tools like Fivetran (E), DBT (T), and Looker (L). We have identified Jenkins as a source of frustration for the team. Both seasoned engineers and new members find troubleshooting and maintaining both instances (QA, Prod) particularly time consuming. It also adds a significant slope to the learning curve for new members because env variables, runtime dependencies, and logs are inside these boxes. Basically, new members will need to pull their DevOps/SisAdmin hats just to be able to setup their local environment correctly, before troubleshooting any data pipeline issues.

## Proposal

We need a hosted platform that can accommodate all of our current orchestration needs:
- Have alerting and monitoring built in. Including Slack integration.
- Logging management
- CI integration to test new model changes when merging PRs
- We currently do this manually. If there is a PR with changes to models, we manually recompile the DBT model in QA
- Ability to run non DBT jobs:
- ETL monitor script
- CIO Consumer
- Bertly refresh from AWS
- Prod Warehouse dump to QA
- GDPR Compliance. Deletes user's data based on their `deleted_at` value

Given the solutions evaluated below. I recommend:

Continuing using Jenkins as our job runtime for the short term. This will allow us to pivot into investing more engineering resources to make our data even more reliable by integrating a testing framework, cleaning up the work we have done so far with deprecating the Northstar scraper, and renaming of our Fivetran schemas.

In the medium to long term. We believe switching our warehouse to a Big Data friendly DB is in the order. This would be a good time to re-evaluate if there is a tool that better integrates with the warehouse we choose at that time. Example: Google Cloud Composer.

## Evaluated Solutions

I have deep dived into a few platforms and will share my findings here. I have created a sample ETL orchestration in all of them but Astronomer Cloud.

Terminology:
> **DAG**: Directed acyclic graph
> **ETL**: Extract Transform and Load
> **IDE**: Integrated Development Environment
> **CI**: Continuous Integration

### Astronomer cloud (Airflow)

[Airflow](https://airflow.apache.org/docs/stable/) is an OSS workflow (ETL Pipelines) orchestration platform. It was developed by Airbnb. It's the gold standard for ETL orchestration. It's perfectly capable of integrating our current DBT transformations.

[Astronomer](https://www.astronomer.io/docs/) is built on [Kubernetes](https://kubernetes.io/). It's a fully-managed Airflow service.

Pros |
--|

- It’s the most used Platform to orchestrate ETL pipelines. Plenty of organizations use it.
- The Astronomer hosted solution would help us focus on writting our required jobs instead of managing it's infrastructure.
- Astronomer uses the concept of "operators" to extend the capabilities of a DAG (what we would call a job). Through this operators we can recreate all the jobs we have on Jenkins.
  
Cons |
--|
- It has a steep learning curve and the team does not have production experience with it.
- Each job would have to be refactored into DAGs.
- It is a very heavy hammer to wield considering the state of our pipelines. This was confirmed by the advice I got from the DBT Slack community.

General observations |
--|
- Airflow has other competitors like [Luigi](https://github.com/spotify/luigi) and [Azkaban](https://azkaban.github.io/). However, they are slowly falling behind Airflow in the workflow management space. Google has also created a similar solution to Astronomer named [Google Cloud Composer](https://cloud.google.com/composer).
- [Here](https://www.astronomer.io/guides/google-composer-comparison/) is a comparison between GCC and Astronomer.

#### Resolution

Given the state of our codebase, size and composition of our team. Migrating to Astronomer (or any hosted version of Airflow) would introduce a steep learning curve and complexity. It requires substantial refactoring of our Quasar codebase to transpile DBT and misc jobs into DAGs. As well as require the full team’s resources for at least a quarter to confidently implement, tests, and deploy to production. I don't consider it to be the right tool for our team at this time.

### DBT Cloud

[DBT](https://docs.getdbt.com/docs/introduction) (Data Build Tool) is a data transformation framework. It allows data engineers to easily define how to transform raw data into analysis ready data.
 
[DBT Cloud](https://docs.getdbt.com/docs/cloud-overview) is a hosted scheduler for DBT jobs.  

Pros|
--|
- It's hosted. We won’t need to maintain and troubleshoot any EC2 instances.
- It will build the [docs](https://dosomething.github.io/quasar/#!/overview?g_v=1) automatically. We currently use [Github actions](https://github.com/DoSomething/quasar/actions?query=workflow%3A%22DBT+Docs%22) for it. We would have one less thing to maintain.
- Their community is very active and responsive (I got feedback on using Astronomer), as well as their support email (I asked some Slack integration question).

Cons|
--|
- It doesn't support environment variables yet. There is a workaround to this issue coming in the next release of DBT Cloud 0.16. Expected by the end of March.
- **It doesn’t support non-DBT scripts**. These scripts will have to remain in Jenkins until we deprecate them.

General observations|
--|
- Their UI needs to be a bit more clear and robust. 
- It includes an IDE that if setup correctly with Github and Dev warehouse credentials, could be very useful.

#### Resolution

The fact that they don't support non-DBT jobs disqualifies it. We don't feel like splitting our jobs into 2 platforms really benefits anyone. Even if we deprecated the non-DBT jobs. Vendor locking our ability to engineer automated solutions for non-DBT jobs is out of the question.

I reached out to them and asked about if they would ever support this. Their answer was, I quote, "*Nope. dbt is solely the "T" in the ELT process. I don't see us moving away from that any time soon!*"

### Circle CI

[Circle CI](https://circleci.com/docs/2.0/about-circleci/#section=welcome) is a CI/CD platform. It automates application builds, tests, and deployments. It's very versatile since it runs the jobs in their own [Docker](https://www.docker.com/get-started) containers. It supports scheduled jobs.
  
Pros|
--|
- We use it here at DoSomething ([GraphQL](https://github.com/DoSomething/graphql)). Making it easier to tap on the collective engineering knowledge base if need be. As well as making us a source of expertise when it comes to scheduled Circle CI jobs.
- Contains a CLI that would allow devs to test job configurations locally.
- Thanks to its containerized nature. We could theoretically move all of our jobs to Circle CI and deprecate Jenkins.

Cons|
--|
- Front loads the learning curve related to developing, testing, and deploying changes to our scheduled ETL jobs.
- The configuration of the jobs is strictly done via code updates. All jobs have to be defined inside a `config.yml` file.
- Handling QA and Prod scheduled job versions might make the configuration file hard to read. Maybe modularizing the config file and using includes may help with this.

General observations|
--|
- Scheduled jobs can’t be triggered using the friendly UI. They have to be tested locally with the Circle CI CLI -- Which has it's own [limitations](https://circleci.com/docs/2.0/local-cli/#limitations-of-running-jobs-locally).
- It will require a good amount of configuration code changes in order to get all the jobs to build in a containerized environment.
- Although it supports ENV variables a la Heroku. This becomes a problem when testing locally (listed in the limitations link). The Circle CI CLI requires the ENV variables to be passed explicitly and individually in the command.

#### Resolution

It introduces too many changes to the way we collaborate, test, and setup our jobs. Changing a single aspect of any job would require us to change code and deploy to master and production. This is too restrictive. We learned the value of Jenkins flexibility while migrating to use DBT.

If we were to invest all of our resources into refactoring the whole Quasar codebase, as well as adopting a whole new paradigm to manage our jobs, I would rather invest it adopting Astronomer!
