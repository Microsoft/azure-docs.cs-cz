---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB
description: Pomocí šablon Azure Resource Manageru k vytvoření a konfigurace služby Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: c907de019b64a6a9d03206514a1147fd43c78106
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078457"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Šablony Azure Resource Manageru pro službu Azure Cosmos DB

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro službu Azure Cosmos DB:

|**Typ rozhraní API** | **odkaz na ukázky**| **Popis** |
|---|---| ---|
|Rozhraní API Core (SQL)| [Vytvoření účtu služby Azure Cosmos DB (více hlavních databází)](manage-sql-with-resource-manager.md) | Tato šablona vytvoří účet rozhraní SQL API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dva kontejnery, které sdílejí propustnosti na úrovni databáze. |
| Rozhraní MongoDB API | [Vytvoření účtu služby Azure Cosmos DB (více hlavních databází)](manage-mongodb-with-resource-manager.md) | Tato šablona vytvoří účet pomocí rozhraní API služby Azure Cosmos DB pro MongoDB ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dva kontejnery, které sdílejí propustnosti na úrovni databáze. |
| Rozhraní Cassandra API | [Vytvoření účtu služby Azure Cosmos DB (více hlavních databází)](manage-cassandra-with-resource-manager.md) | Tato šablona vytvoří účet rozhraní Cassandra API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dvě tabulky, které sdílejí keyspace úroveň propustnosti. |
| Rozhraní Gremlin API| [Vytvoření účtu služby Azure Cosmos DB (více hlavních databází)](manage-gremlin-with-resource-manager.md) | Tato šablona vytvoří účet rozhraní Gremlin API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít dva grafy, které sdílejí propustnosti na úrovni databáze. |
| Rozhraní Table API | [Vytvoření účtu služby Azure Cosmos DB (více hlavních databází)](manage-table-with-resource-manager.md) | Tato šablona vytvoří účet Table API ve dvou oblastech s několika hlavními uzly povolena. Účet Azure Cosmos, bude mít jednu tabulku. |

> [!TIP]
> Pokud chcete povolit sdílený propustnosti při použití rozhraní API tabulky, povolte propustnost úroveň účtu na webu Azure Portal.

Zobrazit [ARM odkaz pro službu Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) stránky referenční dokumentaci.