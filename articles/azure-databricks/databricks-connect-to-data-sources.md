---
title: 'Připojení k různým zdrojům dat z Azure Databricks '
description: Naučte se, jak se připojit k Azure SQL Database, Azure Data Lake Store, úložišti objektů blob, Cosmos DB, Event Hubs a Azure SQL Data Warehouse z Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: c77d1d1a66d3ee92f5ad3f2016d2160831fa3ad9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299306"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Připojení ke zdrojům dat z Azure Databricks

Tento článek obsahuje odkazy na všechny různé zdroje dat v Azure, které se dají připojit k Azure Databricks. Použijte příklady v těchto odkazech k extrakci dat ze zdrojů dat Azure (například Azure Blob Storage, Azure Event Hubs atd.) do clusteru Azure Databricks a na nich spouštějte analytické úlohy. 

## <a name="prerequisites"></a>Předpoklady

* Musíte mít Azure Databricks pracovní prostor a cluster Spark. Postupujte podle pokynů v části [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Zdroje dat pro Azure Databricks

Následující seznam poskytuje zdroje dat v Azure, které můžete použít s Azure Databricks. Úplný seznam zdrojů dat, které lze použít s Azure Databricks, najdete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Databáze SQL Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Tento odkaz poskytuje rozhraní API dataframe pro připojení k databázím SQL pomocí JDBC a způsob řízení paralelismu pro čtení prostřednictvím rozhraní JDBC. V tomto tématu najdete podrobné příklady použití rozhraní Scala API se zkrácenými příklady Pythonu a Spark SQL na konci.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Tento odkaz poskytuje příklady použití Azure Active Directory instančního objektu k ověřování pomocí Data Lake Store. Poskytuje také pokyny pro přístup k datům v Data Lake Store z Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Tento odkaz poskytuje příklady, jak přímo získat přístup k Azure Blob Storage z Azure Databricks pomocí přístupového klíče nebo SAS pro daný kontejner. Odkaz taky poskytuje informace o tom, jak získat přístup k Azure Blob Storage z Azure Databricks pomocí rozhraní RDD API.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Tento odkaz poskytuje pokyny, jak použít [Azure Cosmos DB konektor Spark](https://github.com/Azure/azure-cosmosdb-spark) z Azure Databricks pro přístup k datům v Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Tento odkaz poskytuje pokyny, jak použít [konektor Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z Azure Databricks pro přístup k datům v Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Tento odkaz poskytuje pokyny, jak pomocí konektoru Azure SQL Data Warehouse připojit z Azure Databricks.
    

## <a name="next-steps"></a>Další kroky

Další informace o zdrojích, ze kterých můžete importovat data do Azure Databricks, najdete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


