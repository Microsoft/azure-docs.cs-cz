---
title: Šablony Azure Resource Manager pro Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manager.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961861"
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
