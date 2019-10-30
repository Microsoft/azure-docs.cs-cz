---
title: Šablony Azure Resource Manager pro Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7861196daf3b04430200096a0d42bdcc6e6d5cf9
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053225"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manager pro Azure Cosmos DB

Následující tabulky obsahují odkazy na šablony Azure Resource Manager pro Azure Cosmos DB:

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

|**Šablona**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru](manage-sql-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet SQL (Core) rozhraní API ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dva kontejnery, které sdílejí propustnost na úrovni databáze. |
|[Vytvoření účtu Azure Cosmos, databáze a kontejneru s uloženou procedurou, triggerem a systémem souborů UDF](manage-sql-with-resource-manager.md#create-sproc) | Tato šablona vytvoří účet SQL (Core) rozhraní API ve dvou oblastech s uloženou procedurou, triggerem a systémem souborů UDF pro kontejner. |
|[Aktualizace propustnosti (RU/s) pro databázi](manage-sql-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu rozhraní API SQL (Core). |
|[Aktualizace propustnosti (RU/s) pro kontejner](manage-sql-with-resource-manager.md#container-ru-update) | Tato šablona aktualizuje propustnost kontejneru v účtu rozhraní API SQL (Core). |

## <a name="mongodb-api"></a>MongoDB API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, kolekce](manage-mongodb-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet s použitím rozhraní Azure Cosmos DB API pro MongoDB ve dvou oblastech s povoleným více hlavními servery. Účet Azure Cosmos bude mít dva kontejnery, které sdílejí propustnost na úrovni databáze. |
|[Aktualizace propustnosti (RU/s) pro databázi](manage-mongodb-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu rozhraní MongoDB API. |
|[Aktualizace propustnosti (RU/s) pro kolekci](manage-mongodb-with-resource-manager.md#collection-ru-update) | Tato šablona aktualizuje propustnost kontejneru v účtu rozhraní MongoDB API. |

## <a name="cassandra-api"></a>Rozhraní API Cassandra

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
