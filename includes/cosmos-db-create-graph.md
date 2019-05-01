---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: rockboyfor
ms.service: cosmos-db
ms.topic: include
origin.date: 04/13/2018
ms.date: 03/18/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9800848b24523a0eb5992e64ce1580cc53b0e3a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60891455"
---
<!--Verify sucessfully-->
Teď můžete pomocí nástroje Průzkumník dat na webu Azure Portal vytvořit databázi grafu. 

1. Klikněte na **Průzkumník dat** > **Nový graf**.

    Úplně vpravo se zobrazí oblast **Přidat graf**. Pokud ji nevidíte, možná se budete muset posunout doprava.

    ![Průzkumník dat na portálu Azure – stránka Přidat graf](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. Na stránce **Přidat graf** zadejte nastavení pro nový graf.

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID databáze|sample-database|Jako název nové databáze zadejte *sample-database*. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky `/ \ # ?` ani koncové mezery.
    ID grafu|sample-graph|Jako název nové kolekce zadejte *sample-graph*. Názvy grafů mají stejné požadavky na znaky jako ID databází.
    Kapacita úložiště|Pevná (10 GB)|Nechte zadanou výchozí hodnotu **Pevná (10 GB)**. Tato hodnota je kapacita úložiště databáze.
    Propustnost|400 RU/s|Změňte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.

3. Po vyplnění formuláře klikněte na **OK**.

<!--Verify sucessfully-->
<!--Update_Description: new articles on  -->
<!--ms.date: 03/18/2019-->