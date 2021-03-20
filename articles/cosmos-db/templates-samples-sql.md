---
title: Šablony Azure Resource Manager pro Azure Cosmos DB Core (SQL API)
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 52fa5957cdbc3ae73ebc4b36e04582741199414a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340596"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek ukazuje jenom Azure Resource Manager příklady šablon pro účty rozhraní API Core (SQL). Můžete také najít příklady šablon pro rozhraní API pro [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md)a [Table](templates-samples-table.md) .

## <a name="core-sql-api"></a>Rozhraní API Core (SQL)

|**Šablona**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru s propustností automatického škálování](manage-with-templates.md#create-autoscale) | Tato šablona vytvoří účet rozhraní API Core (SQL) ve dvou oblastech, databázi a kontejner s propustností automatického škálování. |
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru s analytickým úložištěm](manage-with-templates.md#create-analytical-store) | Tato šablona vytvoří účet základního (SQL) rozhraní API v jedné oblasti s kontejnerem nakonfigurovaným s povoleným analytickým standardem TTL a možností ručního škálování nebo propustnosti. |
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru se standardní (ruční) propustností](manage-with-templates.md#create-manual) | Tato šablona vytvoří účet rozhraní API Core (SQL) ve dvou oblastech, databázi a kontejner se standardní propustností. |
|[Vytvoření účtu Azure Cosmos, databáze a kontejneru s uloženou procedurou, triggerem a systémem souborů UDF](manage-with-templates.md#create-sproc) | Tato šablona vytvoří účet základního (SQL) rozhraní API ve dvou oblastech s uloženou procedurou, triggerem a systémem souborů UDF pro kontejner. |
|[Vytvoření privátního koncového bodu pro existující účet Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní API Azure Cosmos Core (SQL) v existující virtuální síti. |
|[Vytvoření účtu Azure Cosmos na bezplatné úrovni](manage-with-templates.md#free-tier) |  Tato šablona vytvoří účet rozhraní API pro Azure Cosmos DB Core (SQL) na bezplatné úrovni. |

Referenční dokumentaci najdete v tématu [Azure Resource Manager reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) Page.
