---
title: 'Připojení k různým zdrojům dat z Azure Databricks '
description: Zjistěte, jak se připojit k různým zdrojům dat z Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 8055b5d7e6c53abc385a99d9190a38603ebb968b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417442"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Připojení ke zdrojům dat z Azure Databricks

Tento článek obsahuje odkazy na všechny různé datové zdroje v Azure, který jde připojit k Azure Databricks. Postupovat podle příkladů v těchto odkazů extrahovat data ze zdrojů dat Azure (třeba Azure Blob Storage, Azure Event Hubs, atd.) do clusteru služby Azure Databricks a spouštěním analytických úloh na ně. 

## <a name="prerequisites"></a>Požadavky

* Musíte mít pracovní prostor služby Azure Databricks a Spark cluster. Postupujte podle pokynů na adrese [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Zdroje dat pro Azure Databricks

Následující seznam obsahuje zdroje dat v Azure, které můžete použít s Azure Databricks. Úplný seznam zdrojů dat, které lze použít s Azure Databricks, naleznete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Azure SQL database](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Tento odkaz poskytuje rozhraní API datového rámce pro připojení k databázím SQL pomocí JDBC a tom, jak řídit paralelismu čtení prostřednictvím JDBC rozhraní. Toto téma obsahuje podrobné příklady použití rozhraní Scala API s využitím zkrácený Pythonu a příklady Spark SQL na konci.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Tento odkaz obsahuje příklady, jak používat instanční objekt služby Azure Active Directory k ověření pomocí Data Lake Store. Obsahuje také pokyny o tom, jak přistupovat k datům v Data Lake Store z Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Tento odkaz obsahuje příklady o tom, jak přímý přístup k Azure Blob Storage z Azure Databricks pomocí přístupového klíče nebo sdíleného přístupového podpisu pro daný kontejner. Odkaz také poskytuje informace o tom, jak přistupovat k úložišti blobů Azure z Azure Databricks pomocí rozhraní API RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Tento odkaz obsahuje pokyny k používání [konektor Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) z Azure Databricks pro přístup k datům ve službě Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Tento odkaz obsahuje pokyny k používání [konektor Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z Azure Databricks pro přístup k datům ve službě Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Tento odkaz obsahuje pokyny k používání konektoru Azure SQL Data Warehouse pro připojení z Azure Databricks.
    

## <a name="next-steps"></a>Další postup

Další informace o zdrojích ve kterém můžete importovat data do Azure Databricks najdete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


