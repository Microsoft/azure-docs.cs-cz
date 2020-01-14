---
title: 'Architecture: On-premises Apache Hadoop to Azure HDInsight'
description: Learn architecture best practices for migrating on-premises Hadoop clusters to Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934694"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrate on-premises Apache Hadoop clusters to Azure HDInsight - architecture best practices

This article gives recommendations for the architecture of Azure HDInsight systems. It's part of a series that provides best practices to assist with migrating on-premises Apache Hadoop systems to Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Use multiple workload-optimized clusters

Many on-premises Apache Hadoop deployments consist of a single large cluster that supports many workloads. This single cluster can be complex and may require compromises to the individual services to make everything work together. Migrating on-premises Hadoop clusters to Azure HDInsight requires a change in approach.

Azure HDInsight clusters are designed for a specific type of compute usage. Because storage can be shared across multiple clusters, it's possible to create multiple workload-optimized compute clusters to meet the needs of different jobs. Each cluster type has the optimal configuration for that specific workload. The following table lists the supported cluster types in HDInsight and the corresponding workloads.

|Úloha|HDInsight Cluster type|
|---|---|
|Batch processing (ETL / ELT)|Hadoop, Spark|
|Datové sklady|Hadoop, Spark, Interactive Query|
|IoT / Streaming|Kafka, Storm, Spark|
|NoSQL Transactional processing|HBase|
|Interactive and Faster queries with in-memory caching|Interactive Query|
|Datové vědy|ML Services, Spark|

The following table shows the different methods that can be used to create an HDInsight cluster.

|Nástroj|Browser based|Příkazový řádek|Rozhraní REST API|SDK|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|×||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|×|×|×|×|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||×|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||×|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||×|×||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||×|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||×|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||×|
|[Azure Resource Manager templates](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||×|||

For more information, see the article [Cluster types in HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Use transient on-demand clusters

HDInsight clusters may go unused for long periods of time. To help save on resource costs, HDInsight supports on-demand transient clusters, which can be deleted once the workload has been successfully completed.

When you delete a cluster, the associated storage account and external metadata aren't removed. The cluster can later be re-created using the same storage accounts and meta-stores.

Azure Data Factory can be used to schedule creation of on-demand HDInsight clusters. For more information, see the article [Create on-demand Apache Hadoop clusters in HDInsight using Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Decouple storage from compute

Typical on-premises Hadoop deployments use the same set of machines for data storage and data processing. Because they're colocated, compute and storage must be scaled together.

On HDInsight clusters, storage doesn't need to be colocated with compute and can either be in Azure storage, Azure Data Lake Storage or both. Decoupling storage from compute has the following benefits:

- Data sharing across clusters.
- Use of transient clusters since the data isn't dependent on cluster.
- Reduced storage cost.
- Scaling storage and compute separately.
- Data replication across regions.

Compute clusters are created close to storage account resources in an Azure region to mitigate performance cost of separating compute and storage. High-speed networks make it efficient for the compute nodes to access the data inside Azure storage.

## <a name="use-external-metadata-stores"></a>Použití externích úložišť metadat

There are two main metastores that work with HDInsight clusters: [Apache Hive](https://hive.apache.org/) and [Apache Oozie](https://oozie.apache.org/). The Hive metastore is the central schema repository that can be used by data processing engines including Hadoop, Spark, LLAP, Presto, and Apache Pig. The Oozie metastore stores details about scheduling and the status of in progress and completed Hadoop jobs.

HDInsight uses Azure SQL Database for Hive and Oozie metastores. There are two ways to set up a metastore in HDInsight clusters:

1. Default metastore

    - No additional cost.
    - Metastore is deleted when the cluster is deleted.
    - Metastore can't be shared among different clusters.
    - Uses basic Azure SQL DB, which has a five DTU limit.

1. Custom external metastore

    - Specify an external Azure SQL Database as the metastore.
    - Clusters can be created and deleted without losing metadata including Hive schema Oozie job details.
    - Single metastore db can be shared with different types of clusters.
    - Metastore can be scaled up as needed.
    - For more information, see [Use external metadata stores in Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Best practices for Hive Metastore

Some HDInsight Hive metastore best practices are as follows:

- Use a custom external metastore to separate compute resources and metadata.
- Start with an S2 tier Azure SQL instance, which provides 50 DTU and 250 GB of storage. If you see a bottleneck, you can scale the database up.
- Don't share the metastore created for one HDInsight cluster version with clusters of a different version. Different Hive versions use different schemas. For example, a metastore can't be shared with both Hive 1.2 and Hive 2.1 clusters.
- Back up the custom metastore periodically.
- Keep the metastore and HDInsight cluster in the same region.
- Monitor the metastore for performance and availability using Azure SQL Database Monitoring tools, like Azure portal or Azure Monitor logs.
- Execute the `ANALYZE TABLE` command as required to generate statistics for tables and columns. Například, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Best practices for different workloads

- Consider using LLAP cluster for interactive Hive queries with improved response time [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) is a new feature in Hive 2.0 that allows in-memory caching of queries. LLAP makes Hive queries much faster, up to [26x faster than Hive 1.x in some cases](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Consider using Spark jobs in place of Hive jobs.
- Consider replacing impala-based queries with LLAP queries.
- Consider replacing MapReduce jobs with Spark jobs.
- Consider replacing low-latency Spark batch jobs using Spark Structured Streaming jobs.
- Consider using Azure Data Factory (ADF) 2.0 for data orchestration.
- Consider Ambari for Cluster Management.
- Change data storage from on-premises HDFS to WASB or ADLS or ADFS for processing scripts.
- Consider using Ranger RBAC on Hive tables and auditing.
- Consider using CosmosDB in place of MongoDB or Cassandra.

## <a name="next-steps"></a>Další kroky

Read the next article in this series:

- [Infrastructure best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
