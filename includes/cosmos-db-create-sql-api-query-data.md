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
ms.openlocfilehash: 448c445f49fb4baa300ce6636288a39080da3baf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027961"
---
K načtení a filtrování dat můžete použít dotazy v Průzkumník dat.

1. V horní části karty **položky** v Průzkumník dat zkontrolujte výchozí dotaz `SELECT * FROM c` . Tento dotaz načte a zobrazí všechny dokumenty z kontejneru seřazené podle ID. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Výchozí dotaz v Průzkumník dat je SELECT * FROM c":::
   
1. Chcete-li změnit dotaz, vyberte možnost **Upravit filtr**, nahraďte výchozí dotaz hodnotou `ORDER BY c._ts DESC` a potom vyberte možnost **použít filtr**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Změna výchozího dotazu přidáním „ORDER BY c._ts DESC“ a kliknutím na Použít filtr":::

   Upravený dotaz zobrazuje dokumenty v sestupném pořadí podle jejich časového razítka, takže teď je druhý dokument uvedený jako první. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Změněný dotaz na řazení podle c._ts DESC a kliknutí na použít filtr":::

Pokud jste obeznámeni se syntaxí SQL, můžete do pole predikátu dotazu zadat všechny podporované [dotazy SQL](../articles/cosmos-db/sql-query-getting-started.md) . Průzkumník dat můžete použít také k vytváření uložených procedur, UDF a triggerů pro serverovou obchodní logiku. 

Průzkumník dat poskytuje snadný Azure Portal přístup ke všem integrovaným funkcím pro přístup k programovým datům dostupným v rozhraních API. Portál se používá také ke škálování propustnosti, získání klíčů a připojovacích řetězců a ke kontrole metrik a SLA pro váš účet Azure Cosmos DB.