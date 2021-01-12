---
title: Odkaz na Azure synapse (Preview) pro Azure Cosmos DB podporované funkce
description: Seznamte se s aktuálním seznamem akcí, které podporuje Azure synapse Link pro Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b58474758ac4d26b347dc72d84be401d15a3846b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119811"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Podporované funkce Azure Synapse Linku pro Azure Cosmos DB

Tento článek popisuje aktuálně podporované funkce v Azure Synapse Linku pro Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Podpora Azure synapse

V Azure Cosmos DB existují dva typy kontejnerů:
* Kontejner HTAP-kontejner s povoleným odkazem synapse Tento kontejner obsahuje transakční úložiště i analytické úložiště. 
* Kontejner OLTP-kontejner s odkazem Synaspe není povolen. Tento kontejner obsahuje jenom transakční úložiště a žádné analytické úložiště.

> [!IMPORTANT]
> Odkaz na Azure synapse pro Azure Cosmos DB se v současnosti podporuje v pracovních prostorech synapse, které nemají povolenou spravovanou virtuální síť. 

K kontejneru Azure Cosmos DB se můžete připojit bez povolení odkazu synapse. V tomto scénáři můžete jen číst a zapisovat do transakčního úložiště. Níže je uveden seznam aktuálně podporovaných funkcí v rámci odkazu na synapse pro Azure Cosmos DB. 

| Kategorie              | Popis |[Fond Apache Spark](../sql/on-demand-workspace-overview.md) | [Fond SQL bez serveru](../sql/on-demand-workspace-overview.md) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Podpora Runtime** |Podporované běhové prostředí Azure synapse pro přístup k Azure Cosmos DB| ✓ | Preview |
| **Podpora rozhraní Azure Cosmos DB API** | Podporovaný druh Azure Cosmos DB rozhraní API | SQL/MongoDB | SQL/MongoDB |
| **Objekt**  |Objekty, jako je tabulka, která se dá vytvořit, ukazující přímo na Azure Cosmos DB kontejner| Datový rámec, zobrazení, tabulka | Zobrazení |
| **Oprávnění**    | Typ kontejneru Azure Cosmos DB, který se dá přečíst | OLTP/HTAP | HTAP  |
| **Psal**   | Dá se použít modul runtime Azure Synapse k zápisu dat do kontejneru Azure Cosmos DB. | Yes | No |

* Pokud zapisujete data do kontejneru Azure Cosmos DB ze Sparku, k tomuto procesu dochází v transakčním úložišti Azure Cosmos DB. Bude to mít vliv na transakční výkon Azure Cosmos DB díky využívání jednotek žádosti.
* Vyhrazená integrace fondu SQL prostřednictvím externích tabulek není v současné době podporovaná.
 
## <a name="supported-code-generated-actions-for-spark"></a>Podporované akce generované kódem pro Spark

| Gesto              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Načíst do datového rámce** |Načtení a načtení dat do datového rámce Spark |✓| ✓ |
| **Vytvořit tabulku Spark** |Vytvoření tabulky ukazující na kontejner Azure Cosmos DB|✓| ✓ |
| **Zapsat datový rámec do kontejneru** |Zápis dat do kontejneru|✓| ✓ |
| **Načíst datový proud streamování z kontejneru** |Streamování dat pomocí Azure Cosmos DB změny kanálu|✓| ✓ |
| **Zápis datového proudu streamování do kontejneru** |Streamování dat pomocí Azure Cosmos DB změny kanálu|✓| ✓ |


## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Podporované akce generované kódem pro fond SQL bez serveru

| Gesto              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Zkoumání dat** |Prozkoumat data z kontejneru se známou syntaxí T-SQL a automatickým odvozením schématu|X| ✓ |
| **Vytváření zobrazení a vytváření sestav BI** |Vytvoření zobrazení SQL pro přímý přístup k kontejneru pro BI prostřednictvím neserverového fondu SQL |X| ✓ |
| **Spojování různorodých zdrojů dat spolu s Cosmos DBmi daty** | Uložení výsledků dotazu na čtení dat z Cosmos DB kontejnerů spolu s daty v Azure Blob Storage nebo Azure Data Lake Storage pomocí CETAS |X| ✓ |

## <a name="next-steps"></a>Další kroky

* [Azure Cosmos DB odkazu pro připojení k synapse](../quickstart-connect-synapse-link-cosmos-db.md)
* [Naučte se, jak zadávat dotazy na Cosmos DB analytické úložiště pomocí Sparku.](how-to-query-analytical-store-spark.md)