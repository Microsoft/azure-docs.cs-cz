---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83586146"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB

Následující tabulky obsahují odkazy na šablony Azure Resource Manager pro Azure Cosmos DB:

## <a name="core-sql-api"></a>Rozhraní API Core (SQL)

|**Šablona**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru s propustností automatického škálování](manage-sql-with-resource-manager.md#create-autoscale) | Tato šablona vytvoří účet rozhraní API Core (SQL) ve dvou oblastech, databázi a kontejner s propustností automatického škálování. |
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru s analytickým úložištěm](manage-sql-with-resource-manager.md#create-analytical-store) | Tato šablona vytvoří účet základního (SQL) rozhraní API v jedné oblasti s kontejnerem nakonfigurovaným s povoleným analytickým standardem TTL a možností ručního škálování nebo propustnosti. |
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
