---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: 501065875cafc035d491e606c016f3ad4dbfc5d4
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791625"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB

Následující tabulky obsahují odkazy na šablony Azure Resource Manager pro Azure Cosmos DB:

## <a name="core-sql-api"></a>Rozhraní API Core (SQL)

|**Šablona**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru s propustností automatického škálování](manage-sql-with-resource-manager.md#create-autoscale) | Tato šablona vytvoří účet rozhraní API Core (SQL) ve dvou oblastech, databázi a kontejner s propustností automatického škálování. |
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru se standardní (ruční) propustností](manage-sql-with-resource-manager.md#create-manual) | Tato šablona vytvoří účet rozhraní API Core (SQL) ve dvou oblastech, databázi a kontejner se standardní propustností. |
|[Vytvoření účtu Azure Cosmos, databáze a kontejneru s uloženou procedurou, triggerem a systémem souborů UDF](manage-sql-with-resource-manager.md#create-sproc) | Tato šablona vytvoří účet základního (SQL) rozhraní API ve dvou oblastech s uloženou procedurou, triggerem a systémem souborů UDF pro kontejner. |
|[Vytvoření privátního koncového bodu pro existující účet Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní API Azure Cosmos Core (SQL) v existující virtuální síti. |
|[Vytvoření účtu Azure Cosmos na bezplatné úrovni](manage-sql-with-resource-manager.md#free-tier) |  Tato šablona vytvoří účet rozhraní API pro Azure Cosmos DB Core (SQL) na bezplatné úrovni. |

## <a name="mongodb-api"></a>Rozhraní MongoDB API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, kolekce s propustností automatického škálování](manage-mongodb-with-resource-manager.md#create-autoscale) | Tato šablona vytvoří účet pomocí rozhraní Azure Cosmos DB API pro MongoDB ve dvou oblastech se dvěma kontejnery, které sdílejí propustnost automatického škálování na úrovni databáze. |
|[Vytvoření účtu Azure Cosmos, databáze, kolekce se standardní (ruční) propustností](manage-mongodb-with-resource-manager.md#create-manual) | Tato šablona vytvoří účet pomocí rozhraní Azure Cosmos DB API pro MongoDB ve dvou oblastech se dvěma kontejnery, které sdílejí standardní propustnost na úrovni databáze. |

## <a name="cassandra-api"></a>Rozhraní Cassandra API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, místa na místě, tabulky s propustností automatického škálování](manage-cassandra-with-resource-manager.md#create-autoscale) | Tato šablona vytvoří účet rozhraní API Cassandra ve dvou oblastech s místem na disku a tabulkou s propustností automatického škálování. |
|[Vytvoření účtu Azure Cosmos, místa na místě, tabulky se standardní (ruční) propustností](manage-cassandra-with-resource-manager.md#create-manual) | Tato šablona vytvoří účet rozhraní API Cassandra ve dvou oblastech s použitím prostoru klíčů a tabulky s ruční propustností. |

## <a name="gremlin-api"></a>Rozhraní Gremlin API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, grafu s propustností automatického škálování](manage-gremlin-with-resource-manager.md#create-autoscale) | Tato šablona vytvoří účet rozhraní API Gremlin ve dvou oblastech s databází a grafem s propustností automatického škálování. |
|[Vytvoření účtu Azure Cosmos, databáze, grafu se standardní (ruční) propustností](manage-gremlin-with-resource-manager.md#create-manual) | Tato šablona vytvoří účet rozhraní API Gremlin ve dvou oblastech s databází a grafem se standardní propustností. |

## <a name="table-api"></a>Rozhraní Table API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, tabulky s propustností automatického škálování](manage-table-with-resource-manager.md#create-autoscale) | Tato šablona vytvoří účet rozhraní API pro tabulky ve dvou oblastech a jednu tabulku s propustností automatického škálování. |
|[Vytvoření účtu Azure Cosmos, tabulky se standardní (ruční) propustností](manage-table-with-resource-manager.md#create-manual) | Tato šablona vytvoří účet rozhraní API pro tabulky ve dvou oblastech a jednu tabulku se standardní propustností. |

Referenční dokumentaci najdete v tématu [Azure Resource Manager reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) Page.
