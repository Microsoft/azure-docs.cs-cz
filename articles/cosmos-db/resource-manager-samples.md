---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB
description: Pomocí šablon Azure Resource Manageru k vytvoření a konfigurace služby Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 4a32b0497d2457a740e9c082f990bb9112208bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969178"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro službu Azure Cosmos DB:

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvořte účet služby Azure Cosmos, databáze, kontejner](manage-sql-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní API SQL (Core) ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dva kontejnery, které sdílejí propustnosti na úrovni databáze. |
|[Aktualizovat propustnost (RU/s) pro databázi](manage-sql-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu rozhraní API SQL (Core). |
|[Aktualizovat propustnost (RU/s) pro kontejner](manage-sql-with-resource-manager.md#container-ru-update) | Tato šablona aktualizuje propustnost za kontejner v účtu rozhraní API SQL (Core). |

## <a name="mongodb-api"></a>Rozhraní MongoDB API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvořte účet služby Azure Cosmos, databáze, kolekce](manage-mongodb-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet pomocí rozhraní API služby Azure Cosmos DB pro MongoDB ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dva kontejnery, které sdílejí propustnosti na úrovni databáze. |
|[Aktualizovat propustnost (RU/s) pro databázi](manage-mongodb-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu rozhraní MongoDB API. |
|[Aktualizovat propustnost (RU/s) pro kolekci](manage-mongodb-with-resource-manager.md#collection-ru-update) | Tato šablona aktualizuje propustnost za kontejner v účtu rozhraní MongoDB API. |

## <a name="cassandra-api"></a>Rozhraní Cassandra API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvořit účet služby Azure Cosmos, prostor klíčů, tabulku](manage-cassandra-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní Cassandra API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dvě tabulky, které sdílejí keyspace úroveň propustnosti. |
|[Aktualizovat propustnost (RU/s) prostor klíčů](manage-cassandra-with-resource-manager.md#keyspace-ru-update) | Tato šablona aktualizuje propustnost pro prostor klíčů v Apache Cassandra API účtu. |
|[Aktualizovat propustnost (RU/s) pro tabulku](manage-cassandra-with-resource-manager.md#table-ru-update) | Tato šablona aktualizuje propustnost pro tabulky v účtu rozhraní Cassandra API. |

## <a name="gremlin-api"></a>Rozhraní Gremlin API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvořte účet služby Azure Cosmos, databáze a grafu](manage-gremlin-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní Gremlin API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dva grafy, které sdílejí propustnosti na úrovni databáze. |
|[Aktualizovat propustnost (RU/s) pro databázi](manage-gremlin-with-resource-manager.md#database-ru-update) | Tato šablona aktualizuje propustnost pro databázi v účtu Gremlin API nevztahují. |
|[Aktualizace pro graf propustnosti (RU/s)](manage-gremlin-with-resource-manager.md#graph-ru-update) | Tato šablona aktualizuje propustnost pro graf v účtu Gremlin API nevztahují. |

## <a name="table-api"></a>Rozhraní Table API

|**Šablona**|**Popis**|
|---| ---|
|[Vytvoření účtu služby Azure Cosmos, tabulka](manage-table-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet Table API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít jednu tabulku. |
|[Aktualizovat propustnost (RU/s) pro tabulku](manage-table-with-resource-manager.md#table-ru-update) | Tato šablona aktualizuje propustnost pro tabulky v účet Table API. |

> [!TIP]
> Pokud chcete povolit sdílený propustnosti při použití rozhraní API tabulky, povolte propustnost úroveň účtu na webu Azure Portal.

Zobrazit [ARM odkaz pro službu Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) stránky referenční dokumentaci.