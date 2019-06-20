---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 127d67cc3b5dcd0ddd585470821eb1baa08c2388
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175553"
---
Teď můžete do nové kolekce přidávat data pomocí Průzkumníka dat.

1. V Průzkumníku dat se nová databáze zobrazí v podokně Kolekce. Rozbalte databázi **Tasks**, rozbalte kolekci **Items**, klikněte na **Dokumenty** a potom klikněte na **Nové dokumenty**. 

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Teď do kolekce přidejte dokument s následující strukturou.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Po přidání formátu json na kartu **Dokumenty** klikněte na **Uložit**.

    ![Zkopírujte data json a v Průzkumníku dat na webu Azure Portal klikněte na Uložit.](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Vytvořte a uložte ještě jeden dokument, ve kterém vložíte jedinečnou hodnotu pro vlastnost `id` a změníte ostatní vlastnosti podle svých potřeb. Nové dokumenty můžou mít jakoukoli strukturu, protože Azure Cosmos DB neuplatňuje pro data žádné schéma.