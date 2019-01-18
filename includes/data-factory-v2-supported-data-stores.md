---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 12/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: daf7e9ff536894995e880be66d47d5fbf68bf30e
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54363863"
---
| Kategorie | Úložiště dat | Podporované jako zdroj | Podporované jako jímka | Podporované prostředím [Azure IR](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) | Podporované [místním prostředím IR](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime) |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Azure Blob Storage](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Cosmos DB (SQL API)](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Rozhraní API služby Azure Cosmos DB pro MongoDB](../articles/data-factory/connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Lake Storage Gen1](../articles/data-factory/connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Data Lake Storage Gen2 Preview](../articles/data-factory/connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure Database pro MariaDB](../articles/data-factory/connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure Database for MySQL](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure Database for PostgreSQL](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Azure File Storage](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Database](../articles/data-factory/connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Spravovaná Instance Azure SQL Database](../articles/data-factory/connector-azure-sql-database-managed-insance.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Index Azure Search](../articles/data-factory/connector-azure-search.md) | |✓ |✓ |✓  |
| &nbsp; |[Azure Table Storage](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓ |✓ |✓  |
| **Database** |[Amazon Redshift](../articles/data-factory/connector-amazon-redshift.md) |✓ | |✓ |✓  |
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓ |✓  |
| &nbsp; |[Drill (Preview)](../articles/data-factory/connector-drill.md) |✓ | |✓ |✓  |
| &nbsp; |[Google BigQuery](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓ |✓  |
| &nbsp; |[Greenplum](../articles/data-factory/connector-greenplum.md) |✓ | |✓ |✓  |
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓ |✓  |
| &nbsp; |[Hive](../articles/data-factory/connector-hive.md) |✓ | |✓ |✓  |
| &nbsp; |[Apache Impala (Preview)](../articles/data-factory/connector-impala.md) |✓ | |✓ |✓  |
| &nbsp; |[Informix](../articles/data-factory/connector-odbc.md#ibm-informix-source) |✓ | | |✓  |
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓ |✓  |
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-odbc.md#microsoft-access-source) |✓ | | |✓  |
| &nbsp; |[MySQL](../articles/data-factory/connector-mysql.md) |✓ | |✓ |✓  |
| &nbsp; |[Netezza](../articles/data-factory/connector-netezza.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle](../articles/data-factory/connector-oracle.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓ |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/connector-postgresql.md) |✓ | |✓ |✓  |
| &nbsp; |[Presto (Preview)](../articles/data-factory/connector-presto.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  |
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓ | |✓  |
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓ |✓  |
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  |
| &nbsp; |[Teradata](../articles/data-factory/connector-teradata.md) |✓ | | |✓  |
| &nbsp; |[Vertica](../articles/data-factory/connector-vertica.md) |✓ | |✓ |✓  |
| **NoSQL** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓ |✓  |
| &nbsp; |[Couchbase (Preview)](../articles/data-factory/connector-couchbase.md) |✓ | |✓ |✓  |
| &nbsp; |[MongoDB](../articles/data-factory/connector-mongodb.md) |✓ | |✓ |✓  |
| **File** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Systém souborů](../articles/data-factory/connector-file-system.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[FTP](../articles/data-factory/connector-ftp.md) |✓ | |✓ |✓  |
| &nbsp; |[Google Cloud Storage](../articles/data-factory/connector-google-cloud-storage.md) |✓ | |✓ |✓  |
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓ |✓  |
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ | |✓ |✓  |
| **Obecný protokol** |[Obecná rozhraní HTTP](../articles/data-factory/connector-http.md) |✓ | |✓ |✓  |
| &nbsp; |[Obecná OData](../articles/data-factory/connector-odata.md) |✓ | |✓ |✓  |
| &nbsp; |[Obecná rozhraní ODBC](../articles/data-factory/connector-odbc.md) |✓ |✓ | |✓  |
| &nbsp; |[Obecný REST](../articles/data-factory/connector-rest.md) |✓ | |✓ |✓  |
| **Služby a aplikace** |[Amazon Marketplace Web Service (Preview)](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  |
| &nbsp; |[Common Data Service pro aplikace](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Concur (Preview)](../articles/data-factory/connector-concur.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Dynamics AX (Preview)](../articles/data-factory/connector-dynamics-ax.md) |✓ | |✓ |✓  |
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Google AdWords (Preview)](../articles/data-factory/connector-google-adwords.md) |✓ | |✓ |✓  |
| &nbsp; |[HubSpot (Preview)](../articles/data-factory/connector-hubspot.md) |✓ | |✓ |✓  |
| &nbsp; |[Jira (Preview)](../articles/data-factory/connector-jira.md) |✓ | |✓ |✓  |
| &nbsp; |[Magento (Preview)](../articles/data-factory/connector-magento.md) |✓ | |✓ |✓  |
| &nbsp; |[Marketo (Preview)](../articles/data-factory/connector-marketo.md) |✓ | |✓ |✓  |
| &nbsp; |[Office 365 (Preview)](../articles/data-factory/connector-office-365.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Eloqua (Preview)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Responsys (Preview)](../articles/data-factory/connector-oracle-responsys.md) |✓ | |✓ |✓  |
| &nbsp; |[Oracle Cloud Service (Preview)](../articles/data-factory/connector-oracle-service-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[Paypal (Preview)](../articles/data-factory/connector-paypal.md) |✓ | |✓ |✓  |
| &nbsp; |[QuickBooks (Preview)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓ |✓  |
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Cloud služeb Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[Salesforce Marketing Cloud (Preview)](../articles/data-factory/connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  |
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  |
| &nbsp; |[SAP ECC](../articles/data-factory/connector-sap-ecc.md) |✓ | |✓ |✓ |
| &nbsp; |[ServiceNow](../articles/data-factory/connector-servicenow.md) |✓ | |✓ |✓  |
| &nbsp; |[Shopify (Preview)](../articles/data-factory/connector-shopify.md) |✓ | |✓ |✓  |
| &nbsp; |[Square (Preview)](../articles/data-factory/connector-square.md) |✓ | |✓ |✓  |
| &nbsp; |[Webová tabulka (tabulka HTML)](../articles/data-factory/connector-web-table.md) |✓ | | |✓  |
| &nbsp; |[Xero (Preview)](../articles/data-factory/connector-xero.md) |✓ | |✓ |✓  |
| &nbsp; |[Zoho (Preview)](../articles/data-factory/connector-zoho.md) |✓ | |✓ |✓  |

> [!NOTE]
> Konektory s označením *Preview* si můžete vyzkoušet a poskytnout nám k nim zpětnou vazbu.  Pokud do svého řešení chcete zavést závislost na konektorech ve verzi Preview, kontaktujte [podporu Azure](https://azure.microsoft.com/support/).