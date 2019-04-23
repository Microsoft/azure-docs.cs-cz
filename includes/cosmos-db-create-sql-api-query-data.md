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
ms.openlocfilehash: 9971b16da42cdf1de0464857291c74a947535735
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803751"
---
Dotazy v Průzkumníku dat slouží k načtení a filtrovat data.

1. V horní části **dokumenty** kartu v Průzkumníku dat, zkontrolujte výchozí dotaz `SELECT * FROM c`. Tento dotaz načte a zobrazí všechny dokumenty v kolekci v ID objednávky. 
   
   ![Výchozí dotaz v Průzkumníku dat je „SELECT * FROM c“](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Chcete-li změnit dotaz, vyberte **upravit filtr**, nahraďte výchozí dotaz s `ORDER BY c._ts DESC`a pak vyberte **použít filtr**.
   
   ![Změna výchozího dotazu přidáním „ORDER BY c._ts DESC“ a kliknutím na Použít filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Zobrazení upravený dotaz dokumenty v sestupném pořadí na základě jejich časového razítka, takže teď váš druhý dokument je uvedená jako první. 
   
   ![Změnit dotaz, který klauzule ORDER BY c._ts DESC a kliknutím na použít filtr](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Pokud jste obeznámeni se syntaxí SQL, můžete zadat libovolný nepodporuje [dotazy SQL](../articles/cosmos-db/sql-api-sql-query.md) do pole predikátu dotazu. Průzkumník dat můžete také použít k vytvoření uložených procedur, funkcí UDF a triggerů pro serverovou obchodní logiku. 

Průzkumník dat poskytuje snadný Azure portal přístup ke všem funkcím přístup integrovaná Programová data v rozhraních API. Můžete taky použít portál škálujte propustnost, získat klíče a připojovací řetězce a projděte si metriky a smlouvy o úrovni služeb pro váš účet Azure Cosmos DB. 

