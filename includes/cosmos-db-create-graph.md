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
ms.openlocfilehash: 837231ca07abcfdbd6ce932bb24bd890d91506a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69541453"
---
Teď můžete pomocí nástroje Průzkumník dat na webu Azure Portal vytvořit databázi grafu. 

1. Vyberte **nový graf průzkumníka** > **New Graph**dat .

    Úplně vpravo se zobrazí oblast **Přidat graf**. Pokud ji nevidíte, možná se budete muset posunout doprava.

    ![Průzkumník dat na portálu Azure – stránka Přidat graf](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. Na stránce **Přidat graf** zadejte nastavení pro nový graf.

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID databáze|sample-database|Jako název nové databáze zadejte *sample-database*. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky `/ \ # ?` ani koncové mezery.
    Propustnost|400 RU/s|Změňte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.
    ID grafu|sample-graph|Jako název nové kolekce zadejte *sample-graph*. Názvy grafů mají stejné požadavky na znaky jako ID databází.
    Klíč oddílu| /pk |Všechny účty Cosmos DB potřebují klíč oddílu pro horizontální škálování. V článku Dělení dat v grafu se [dozvíte,](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)jak vybrat příslušný klíč oddílu .

3. Po vyplnění formuláře vyberte **možnost OK**.
