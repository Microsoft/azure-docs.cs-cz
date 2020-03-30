---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB
description: K vytvoření a konfiguraci Azure Cosmos DB použijte šablony Azure Resource Manageru.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961861"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB

Následující tabulky obsahují odkazy na šablony Azure Resource Manageru pro Azure Cosmos DB:

## <a name="sql-core-api"></a>Rozhraní SQL (Core) API

|**Šablony**|**Popis**|
|---|---|
|[Vytvoření účtu Azure Cosmos, databáze, kontejneru](manage-sql-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet ROZHRANÍ API SQL (Core) ve dvou oblastech se dvěma kontejnery se sdílenou propustností databáze a kontejnerem s vyhrazenou propustností. Propustnost lze aktualizovat opětovným odesláním šablony s hodnotou vlastnosti aktualizované propustnost. |
|[Vytvoření účtu, databáze a kontejneru Azure Cosmos s uloženou procedurou, aktivační událostí a UDF](manage-sql-with-resource-manager.md#create-sproc) | Tato šablona vytvoří účet ROZHRANÍ API SQL (Core) ve dvou oblastech s uloženou procedurou, aktivační událostí a UDF pro kontejner. |

## <a name="mongodb-api"></a>Rozhraní MongoDB API

|**Šablony**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, kolekce](manage-mongodb-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet pomocí rozhraní API Azure Cosmos DB pro MongoDB ve dvou oblastech s povoleným více hlavním serverem. Účet Azure Cosmos bude mít dva kontejnery, které sdílejí propustnost na úrovni databáze. |

## <a name="cassandra-api"></a>Rozhraní Cassandra API

|**Šablony**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, keyspace, tabulka](manage-cassandra-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní CASSAndRo VE dvou oblastech s povoleným více hlavním serverem. Účet Azure Cosmos bude mít dvě tabulky, které sdílejí propustnost na úrovni keyspace. |

## <a name="gremlin-api"></a>Rozhraní Gremlin API

|**Šablony**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, databáze, grafu](manage-gremlin-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet gremlin rozhraní API ve dvou oblastech s povolenou více hlavních serverem. Účet Azure Cosmos bude mít dva grafy, které sdílejí propustnost na úrovni databáze. |

## <a name="table-api"></a>Rozhraní Table API

|**Šablony**|**Popis**|
|---| ---|
|[Vytvoření účtu Azure Cosmos, tabulka](manage-table-with-resource-manager.md#create-resource) | Tato šablona vytvoří účet rozhraní TABLE API ve dvou oblastech s povoleným více hlavním serverem. Účet Azure Cosmos bude mít jednu tabulku. |

> [!TIP]
> Pokud chcete povolit sdílenou propustnost při použití rozhraní Table API, povolte propustnost na úrovni účtu na webu Azure Portal.

Referenční dokumentaci najdete [na stránce Azure Resource Manager pro Azure Cosmos DB.](/azure/templates/microsoft.documentdb/allversions)
