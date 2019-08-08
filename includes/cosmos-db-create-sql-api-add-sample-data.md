---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 6656c374ae0ff4aae7f99fd340e9e25e5cbc67d1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854150"
---
Nyní můžete přidat data do nového kontejneru pomocí Průzkumník dat.

1. Z **Průzkumník dat**rozbalte databázi Tasks , rozbalte kontejner **položky** . Vyberte **položky**a klikněte na **Nová položka**.

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Nyní do kontejneru přidejte dokument s následující strukturou.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Po přidání JSON na kartu **dokumenty** vyberte **Save (Uložit**).

    ![Zkopírujte data JSON a vyberte Uložit v Průzkumník dat Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Vytvořte a uložte ještě jeden dokument, ve kterém vložíte jedinečnou hodnotu pro vlastnost `id` a změníte ostatní vlastnosti podle svých potřeb. Nové dokumenty můžou mít jakoukoli strukturu, protože Azure Cosmos DB neuplatňuje pro data žádné schéma.