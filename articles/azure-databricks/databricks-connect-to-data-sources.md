---
title: 'Připojení k různým zdrojům dat z Azure Databricks '
description: Zjistěte, jak se připojit k Azure SQL Database, Azure Data Lake Store, úložiště objektů blob, Cosmos DB, Event Hubs a Azure SQL Data Warehouse z Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129381"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Připojení ke zdrojům dat z Azure Databricks

Tento článek obsahuje odkazy na všechny různé zdroje dat v Azure, které můžete připojit k Azure Databricks. Postupujte podle příkladů v těchto odkazech extrahovat data ze zdrojů dat Azure (například Azure Blob Storage, Azure Event Hubs atd.) do clusteru Azure Databricks a spouštět analytické úlohy na nich. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít pracovní prostor Azure Databricks a cluster Spark. Postupujte podle pokynů na [webu Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Zdroje dat pro Azure Databricks

Následující seznam obsahuje zdroje dat v Azure, které můžete použít s Azure Databricks. Úplný seznam zdrojů dat, které lze použít s Azure Databricks, najdete v [tématu Zdroje dat pro Azure Databricks](/azure/databricks/data/data-sources/index).

- [Databáze Azure SQL](/azure/databricks/data/data-sources/sql-databases)

    Tento odkaz poskytuje rozhraní DataFrame API pro připojení k databázím SQL pomocí JDBC a jak řídit paralelismus čtení prostřednictvím rozhraní JDBC. Toto téma obsahuje podrobné příklady pomocí rozhraní Scala API s zkrácenými příklady Pythonu a Spark SQL na konci.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Tento odkaz obsahuje příklady, jak použít hlavní server služby Azure Active Directory k ověření pomocí azure data lake storage. Obsahuje také pokyny, jak získat přístup k datům v Azure Data Lake Storage z Azure Databricks.

- [Úložiště objektů blob Azure](/azure/databricks/data/data-sources/azure/azure-storage)

    Tento odkaz obsahuje příklady, jak přímo přistupovat k Azure Blob Storage z Azure Databricks pomocí přístupového klíče nebo SAS pro daný kontejner. Odkaz také obsahuje informace o tom, jak získat přístup k azure blob storage z Azure Databricks pomocí rozhraní RDD API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Tento odkaz obsahuje pokyny, jak používat [konektor Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) z Azure Databricks pro přístup k datům v Azure Cosmos DB.

- [Centra událostí Azure](/azure/event-hubs/event-hubs-spark-connector)

    Tento odkaz obsahuje pokyny, jak používat [konektor Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z Azure Databricks pro přístup k datům v Azure Event Hubs.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Tento odkaz obsahuje pokyny, jak používat konektor Azure SQL Data Warehouse pro připojení z Azure Databricks.
    

## <a name="next-steps"></a>Další kroky

Další informace o zdrojích, odkud můžete importovat data do Azure Databricks, najdete [v tématu Zdroje dat pro Azure Databricks](/azure/databricks/data/data-sources/index).


