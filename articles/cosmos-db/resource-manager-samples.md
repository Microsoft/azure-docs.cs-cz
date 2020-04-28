---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193254"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB

Následující tabulky obsahují odkazy na šablony Azure Resource Manager pro Azure Cosmos DB:

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

|**Šablona**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru](manage-sql-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet SQL (Core) rozhraní API ve dvou oblastech se dvěma kontejnery s propustností sdílené databáze a kontejnerem s vyhrazenou propustností. Propustnost lze aktualizovat opětovným odesláním šablony s aktualizovanou hodnotou vlastnosti propustnosti. |
|[Vytvoření účtu Azure Cosmos, databáze a kontejneru s uloženou procedurou, triggerem a systémem souborů UDF](manage-sql-with-resource-manager.md#create-sproc) | Tato šablona vytvoří účet SQL (Core) rozhraní API ve dvou oblastech s uloženou procedurou, triggerem a systémem souborů UDF pro kontejner. |
|[Vytvoření privátního koncového bodu pro existující účet Azure Cosmos](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Tato šablona vytvoří privátní koncový bod pro existující účet rozhraní SQL API služby Azure Cosmos v existující virtuální síti. |
|[Vytvoření účtu Azure Cosmos na bezplatné úrovni](manage-sql-with-resource-manager.md#free-tier) |  Tato šablona vytvoří účet Azure Cosmos DB na úrovni Free. |

## <a name="mongodb-api"></a>Rozhraní MongoDB API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, kolekce](manage-mongodb-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet s použitím rozhraní Azure Cosmos DB API pro MongoDB ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dva kontejnery, které sdílejí propustnost na úrovni databáze. |

## <a name="cassandra-api"></a>Rozhraní Cassandra API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, místa na místě, tabulky](manage-cassandra-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní API Cassandra ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dvě tabulky, které sdílejí propustnost na úrovni klíčů. |

## <a name="gremlin-api"></a>Rozhraní Gremlin API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, grafu](manage-gremlin-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní Gremlin API ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dva grafy, které sdílejí propustnost na úrovni databáze. |

## <a name="table-api"></a>Rozhraní Table API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, tabulky](manage-table-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní API pro tabulky ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít jednu tabulku. |

> [!TIP]
> Pokud chcete povolit sdílenou propustnost při použití rozhraní API pro tabulky, povolte propustnost na úrovni účtu na webu Azure Portal.

Referenční dokumentaci najdete v tématu [Azure Resource Manager reference for Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) Page.
