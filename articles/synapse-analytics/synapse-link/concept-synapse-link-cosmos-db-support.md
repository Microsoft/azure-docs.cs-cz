---
title: Odkaz na Azure synapse (Preview) pro Azure Cosmos DB podporované funkce
description: Seznamte se s aktuálním seznamem akcí, které podporuje Azure synapse Link pro Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 2e0b1ee5584420ab38fda8897ef610794b09c29a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658842"
---
# <a name="azure-synapse-link-preview-for-azure-cosmos-db-supported-features"></a>Odkaz na Azure synapse (Preview) pro Azure Cosmos DB podporované funkce

Tento článek popisuje funkce, které jsou aktuálně podporované v tématu Azure synapse Link pro Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Podpora Azure synapse

V Azure Cosmos DB existují dva typy kontejnerů:
* Kontejner HTAP-kontejner s povoleným odkazem synapse Tento kontejner obsahuje transakční úložiště i analytické úložiště. 
* Kontejner OLTP – kontejner s pouze úložištěm transakcí; Odkaz synapse není povolený. 

Můžete se připojit k kontejneru Azure Cosmos DB bez povolení odkazu synapse. v takovém případě můžete jen číst a zapisovat do transakčního úložiště. Co následuje seznam aktuálně podporovaných funkcí v rámci odkazu na synapse pro Azure Cosmos DB. 

| Kategorie              | Popis |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Bez SQL serveru](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Podpora Runtime** |Podpora pro čtení nebo zápis pomocí Azure synapse runtime| ✓ | [Kontaktujte nás](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Podpora rozhraní Azure Cosmos DB API** |Podpora rozhraní API jako synapse odkaz| SQL/MongoDB | SQL/MongoDB |
| **Předmětů**  |Objekty, jako je tabulka, která se dá vytvořit, ukazující přímo na Azure Cosmos DB kontejner| Zobrazit, tabulka | Zobrazit |
| **Oprávnění**    |Čtení dat z kontejneru Azure Cosmos DB| OLTP/HTAP | HTAP  |
| **Psal**   |Zápis dat z doby běhu do kontejneru Azure Cosmos DB| OLTP | Není k dispozici |

* Pokud zapisujete data do kontejneru Azure Cosmos DB ze Sparku, tento proces probíhá prostřednictvím transakčního úložiště Azure Cosmos DB a ovlivní transakční výkon Azure Cosmos DB tím, že spotřebovávají jednotky žádosti.
* Integrace fondů SQL prostřednictvím externích tabulek není v současné době podporovaná.

## <a name="supported-code-generated-actions-for-spark"></a>Podporované akce generované kódem pro Spark

| Gesto              | Popis |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Načíst do datového rámce** |Načtení a načtení dat do datového rámce Spark |×| ✓ |
| **Vytvořit tabulku Spark** |Vytvoření tabulky ukazující na kontejner Azure Cosmos DB|×| ✓ |
| **Zapsat datový rámec do kontejneru** |Zápis dat do kontejneru|✓| ✓ |
| **Načíst datový proud streamování z kontejneru** |Streamování dat pomocí Azure Cosmos DB změny kanálu|✓| ✓ |
| **Zápis datového proudu streamování do kontejneru** |Streamování dat pomocí Azure Cosmos DB změny kanálu|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Podporované akce generované kódem pro SQL Server bez serveru

| Gesto              | Popis |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Vyberte horní 100.** |Náhled prvních 100 položek z kontejneru|×| ✓ |
| **Vytvořit zobrazení** |Vytvoření zobrazení pro přímý přístup k aplikaci BI v kontejneru prostřednictvím synapse SQL|×| ✓ |

## <a name="next-steps"></a>Další kroky

* [Azure Cosmos DB odkazu pro připojení k synapse](../quickstart-connect-synapse-link-cosmos-db.md)
* [Naučte se dotazovat analytické úložiště pomocí Sparku.](how-to-query-analytical-store-spark.md)