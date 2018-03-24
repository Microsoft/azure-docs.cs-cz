---
title: Připojení ke zdrojům různých dat z Azure Databricks | Microsoft Docs
description: Zjistěte, jak se připojit k různých zdrojů dat z Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: nitinme
ms.openlocfilehash: 865313a7c6eabd847529b88ff5fff0b7db438fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Připojení ke zdrojům dat z Azure Databricks

Tento článek obsahuje odkazy na všechny různé datové zdroje v Azure, který může být připojen k Azure Databricks. Postupujte podle příklady v následujících odkazech na extrahovat data ze zdroje dat Azure (například Azure Blob Storage, Azure Event Hubs, atd.) do clusteru služby Azure Databricks a analytické úlohy na je spustit. 

## <a name="prerequisites"></a>Požadavky

* Musí mít pracovní prostor služby Azure Databricks a Spark cluster. Postupujte podle pokynů v [Začínáme s Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Zdroje dat pro Azure Databricks

Následující seznam obsahuje zdroje dat v Azure, který můžete použít s Azure Databricks. Úplný seznam zdrojů dat, které lze použít s Azure Databricks, najdete v části [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Databáze SQL Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Tento odkaz poskytuje rozhraní API DataFrame pro připojení k databázím SQL pomocí JDBC a jak ovládat paralelismu čtení prostřednictvím JDBC rozhraní. Toto téma obsahuje podrobné příklady pomocí rozhraní API Scala zkrácené Python a příklady Spark SQL na konci.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Tento odkaz obsahuje příklady použití objektu služby Azure Active Directory k ověření s Data Lake Store. Obsahuje taky pokyny o tom, jak Azure Databricks přístup k datům v Data Lake Store.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Tento odkaz obsahuje příklady přímý přístup k Azure Blob Storage z Azure Databricks pomocí přístupový klíč nebo SAS pro daný kontejner. Odkaz také poskytuje informace o tom, jak z Azure Databricks přístup k službě Azure Blob Storage pomocí rozhraní API RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Tento odkaz obsahuje pokyny, jak používat [konektor Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) z Databricks Azure pro přístup k datům v Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Tento odkaz obsahuje pokyny, jak používat [konektor Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z Databricks Azure pro přístup k datům v Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Tento odkaz obsahuje pokyny k používání konektoru Azure SQL Data Warehouse k připojení z Azure Databricks.
    

## <a name="next-steps"></a>Další postup

Další informace o zdroji, ze kterých můžete importovat data do Azure Databricks najdete v tématu [zdroje dat pro Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


