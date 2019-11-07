---
title: Šablony Azure Resource Manager pro Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: ecdfe89e899e0d416784ae32a0d66b335c09e2b6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582814"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manager pro Azure Cosmos DB

Následující tabulky obsahují odkazy na šablony Azure Resource Manager pro Azure Cosmos DB:

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

|**Šablona**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru](manage-sql-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet SQL (Core) rozhraní API ve dvou oblastech se dvěma kontejnery s propustností sdílené databáze a kontejnerem s vyhrazenou propustností. Propustnost lze aktualizovat opětovným odesláním šablony s aktualizovanou hodnotou vlastnosti propustnosti. |
|[Vytvoření účtu Azure Cosmos, databáze a kontejneru s uloženou procedurou, triggerem a systémem souborů UDF](manage-sql-with-resource-manager.md#create-sproc) | Tato šablona vytvoří účet SQL (Core) rozhraní API ve dvou oblastech s uloženou procedurou, triggerem a systémem souborů UDF pro kontejner. |

## <a name="mongodb-api"></a>Rozhraní MongoDB API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, kolekce](manage-mongodb-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet s použitím rozhraní Azure Cosmos DB API pro MongoDB ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dva kontejnery, které sdílejí propustnost na úrovni databáze. |
|[Aktualizace propustnosti (RU/s) pro databázi](manage-mongodb-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu rozhraní MongoDB API. |
|[Aktualizace propustnosti (RU/s) pro kolekci](manage-mongodb-with-resource-manager.md#collection-ru-update) | Tato šablona aktualizuje propustnost kontejneru v účtu rozhraní MongoDB API. |

## <a name="cassandra-api"></a>Rozhraní Cassandra API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, místa na místě, tabulky](manage-cassandra-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní API Cassandra ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dvě tabulky, které sdílejí propustnost na úrovni klíčů. |
|[Aktualizace propustnosti (RU/s) pro místo na disku](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Tato šablona aktualizuje propustnost pro místo v rozhraní API Cassandram účtu. |
|[Aktualizace propustnosti (RU/s) pro tabulku](manage-cassandra-with-resource-manager.md#table-ru-update) | Tato šablona aktualizuje propustnost pro tabulku v rozhraní API Cassandram účtu. |

## <a name="gremlin-api"></a>Rozhraní Gremlin API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, grafu](manage-gremlin-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní Gremlin API ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dva grafy, které sdílejí propustnost na úrovni databáze. |
|[Aktualizace propustnosti (RU/s) pro databázi](manage-gremlin-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu rozhraní Gremlin API. |
|[Aktualizace propustnosti (RU/s) pro graf](manage-gremlin-with-resource-manager.md#graph-ru-update) | Tato šablona aktualizuje propustnost grafu v účtu rozhraní Gremlin API. |

## <a name="table-api"></a>Rozhraní Table API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, tabulky](manage-table-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní API pro tabulky ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít jednu tabulku. |
|[Aktualizace propustnosti (RU/s) pro tabulku](manage-table-with-resource-manager.md#table-ru-update) | Tato šablona aktualizuje propustnost pro tabulku v rozhraní API pro tabulkym účtu. |

> [!TIP]
> Pokud chcete povolit sdílenou propustnost při použití rozhraní API pro tabulky, povolte propustnost na úrovni účtu na webu Azure Portal.

Referenční dokumentaci najdete v tématu [Referenční dokumentace ARM pro stránku Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) .
