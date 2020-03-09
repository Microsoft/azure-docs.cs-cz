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
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927385"
---
K načtení a filtrování dat můžete použít dotazy v Průzkumník dat.

1. V horní části karty **položky** v Průzkumník dat zkontrolujte výchozí dotaz `SELECT * FROM c`. Tento dotaz načte a zobrazí všechny dokumenty v kolekci v pořadí podle ID. 
   
   ![Výchozí dotaz v Průzkumníku dat je „SELECT * FROM c“](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Chcete-li změnit dotaz, vyberte možnost **Upravit filtr**, nahraďte výchozí dotaz hodnotou `ORDER BY c._ts DESC`a pak vyberte možnost **použít filtr**.
   
   ![Změna výchozího dotazu přidáním „ORDER BY c._ts DESC“ a kliknutím na Použít filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Upravený dotaz zobrazuje dokumenty v sestupném pořadí podle jejich časového razítka, takže teď je druhý dokument uvedený jako první. 
   
   ![Dotaz se změnil na ORDER BY c. _ts DESC a klikněte na použít filtr.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Pokud jste obeznámeni se syntaxí SQL, můžete do pole predikátu dotazu zadat všechny podporované [dotazy SQL](../articles/cosmos-db/sql-api-sql-query.md) . Průzkumník dat můžete použít také k vytváření uložených procedur, UDF a triggerů pro serverovou obchodní logiku. 

Průzkumník dat poskytuje snadný Azure Portal přístup ke všem integrovaným funkcím pro přístup k programovým datům dostupným v rozhraních API. Portál se používá také ke škálování propustnosti, získání klíčů a připojovacích řetězců a ke kontrole metrik a SLA pro váš účet Azure Cosmos DB. 

