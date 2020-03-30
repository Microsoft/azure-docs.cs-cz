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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927385"
---
Dotazy v Průzkumníkovi dat můžete použít k načtení a filtrování dat.

1. V horní části karty **Položky** v Průzkumníku `SELECT * FROM c`dat zkontrolujte výchozí dotaz . Tento dotaz načte a zobrazí všechny dokumenty v kolekci v pořadí ID. 
   
   ![Výchozí dotaz v Průzkumníku dat je „SELECT * FROM c“](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Chcete-li dotaz změnit, vyberte **upravit** `ORDER BY c._ts DESC`filtr , nahraďte výchozí dotaz a pak vyberte **Použít filtr**.
   
   ![Změna výchozího dotazu přidáním „ORDER BY c._ts DESC“ a kliknutím na Použít filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Upravený dotaz zobrazí dokumenty v sestupném pořadí na základě jejich časového razítka, takže nyní je druhý dokument uveden jako první. 
   
   ![Byl změněn dotaz na PŘÍKAZ C._ts DESC a klepněte na tlačítko Použít filtr.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Pokud jste obeznámeni se syntaxí SQL, můžete zadat všechny podporované [dotazy SQL](../articles/cosmos-db/sql-api-sql-query.md) do pole predikátu dotazu. Průzkumník dat můžete také použít k vytvoření uložených procedur, udf a aktivačních událostí pro obchodní logiku na straně serveru. 

Průzkumník dat poskytuje snadný přístup na portál Azure ke všem integrovaným funkcím přístupu k programovým datům dostupným v rozhraních API. Portál můžete také škálovat propustnost, získat klíče a připojovací řetězce a kontrolovat metriky a sla pro váš účet Azure Cosmos DB. 

