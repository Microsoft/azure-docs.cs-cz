---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 408fb2c40e645d9a8b10f1e04d282e134c5489a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80501687"
---
K načtení a filtrování dat můžete použít dotazy v Průzkumník dat.

1. V horní části karty **položky** v Průzkumník dat zkontrolujte výchozí dotaz `SELECT * FROM c`. Tento dotaz načte a zobrazí všechny dokumenty z kontejneru seřazené podle ID. 
   
   ![Výchozí dotaz v Průzkumníku dat je „SELECT * FROM c“](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Chcete-li změnit dotaz, vyberte možnost **Upravit filtr**, nahraďte výchozí `ORDER BY c._ts DESC`dotaz hodnotou a potom vyberte možnost **použít filtr**.
   
   ![Změna výchozího dotazu přidáním „ORDER BY c._ts DESC“ a kliknutím na Použít filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Upravený dotaz zobrazuje dokumenty v sestupném pořadí podle jejich časového razítka, takže teď je druhý dokument uvedený jako první. 
   
   ![Dotaz se změnil na ORDER BY c. _ts DESC a klikněte na použít filtr.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Pokud jste obeznámeni se syntaxí SQL, můžete do pole predikátu dotazu zadat všechny podporované [dotazy SQL](../articles/cosmos-db/sql-api-sql-query.md) . Průzkumník dat můžete použít také k vytváření uložených procedur, UDF a triggerů pro serverovou obchodní logiku. 

Průzkumník dat poskytuje snadný Azure Portal přístup ke všem integrovaným funkcím pro přístup k programovým datům dostupným v rozhraních API. Portál se používá také ke škálování propustnosti, získání klíčů a připojovacích řetězců a ke kontrole metrik a SLA pro váš účet Azure Cosmos DB. 

