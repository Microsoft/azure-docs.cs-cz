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
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129381"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Připojení ke zdrojům dat z Azure Databricks

Tento článek obsahuje odkazy na všechny různé zdroje dat v Azure, které se dají připojit k Azure Databricks. Použijte příklady v těchto odkazech k extrakci dat ze zdrojů dat Azure (například Azure Blob Storage, Azure Event Hubs atd.) do clusteru Azure Databricks a na nich spouštějte analytické úlohy. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít Azure Databricks pracovní prostor a cluster Spark. Postupujte podle pokynů v části [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Zdroje dat pro Azure Databricks

Následující seznam poskytuje zdroje dat v Azure, které můžete použít s Azure Databricks. Úplný seznam zdrojů dat, které lze použít s Azure Databricks, najdete v tématu [zdroje dat pro Azure Databricks](/azure/databricks/data/data-sources/index).

- [Databáze SQL Azure](/azure/databricks/data/data-sources/sql-databases)

    Tento odkaz poskytuje rozhraní API dataframe pro připojení k databázím SQL pomocí JDBC a způsob řízení paralelismu pro čtení prostřednictvím rozhraní JDBC. V tomto tématu najdete podrobné příklady použití rozhraní Scala API se zkrácenými příklady Pythonu a Spark SQL na konci.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Tento odkaz poskytuje příklady použití Azure Active Directory instančního objektu k ověřování pomocí Azure Data Lake Storage. Poskytuje také pokyny pro přístup k datům v Azure Data Lake Storage z Azure Databricks.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Tento odkaz poskytuje příklady, jak přímo získat přístup k Azure Blob Storage z Azure Databricks pomocí přístupového klíče nebo SAS pro daný kontejner. Odkaz taky poskytuje informace o tom, jak získat přístup k Azure Blob Storage z Azure Databricks pomocí rozhraní RDD API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Tento odkaz poskytuje pokyny, jak použít [Azure Cosmos DB konektor Spark](https://github.com/Azure/azure-cosmosdb-spark) z Azure Databricks pro přístup k datům v Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Tento odkaz poskytuje pokyny, jak použít [konektor Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z Azure Databricks pro přístup k datům v Azure Event Hubs.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Tento odkaz poskytuje pokyny, jak pomocí konektoru Azure SQL Data Warehouse připojit z Azure Databricks.
    

## <a name="next-steps"></a>Další kroky

Další informace o zdrojích, ze kterých můžete importovat data do Azure Databricks, najdete v tématu [zdroje dat pro Azure Databricks](/azure/databricks/data/data-sources/index).


