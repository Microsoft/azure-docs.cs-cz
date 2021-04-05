---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560707"
---
Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření rozhraní API Azure Cosmos DB pro databázi a kontejner MongoDB. 

1. Vyberte **Průzkumník dat**  >  **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|inženýr|Jako název nové databáze zadejte *DB* . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit. Můžete také zvolit [režim automatického škálování](../articles/cosmos-db/provision-throughput-autoscale.md), který vám poskytne rozsah ru/s, který se podle potřeby dynamicky zvětšuje a zmenšuje.| 
    |**ID kolekce**|coll|`coll`Jako název nového kontejneru zadejte. Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Kapacita úložiště**|Pevná (10 GB)|Pro tuto aplikaci zadejte *pevná (10gb)* . Pokud vyberete možnost *neomezeno*, bude nutné vytvořit `Shard Key` , které položky budou vyžadovat.|
    |**Horizontálních oddílů klíč**| /_id| Ukázka popsaná v tomto článku nepoužívá klíč horizontálních oddílů, takže jeho nastavení na  */_ID* bude používat automaticky generované pole ID jako klíč horizontálních oddílů. Přečtěte si další informace o horizontálního dělení, označované taky jako dělení do oddílů, v [oddílu Azure Cosmos DB](../articles/cosmos-db/partitioning-overview.md)|
        
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.