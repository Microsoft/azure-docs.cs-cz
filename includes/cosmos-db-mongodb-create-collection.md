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
ms.openlocfilehash: 8d12645bd302a7ea808f13c07e2a72f66846bad7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85121576"
---
Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření rozhraní API Azure Cosmos DB pro databázi a kontejner MongoDB. 

1. Vyberte **Průzkumník dat**  >  **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="Průzkumník dat na webu Azure Portal s podoknem Přidat kontejner":::

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|inženýr|Jako název nové databáze zadejte *DB* . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit. Můžete také zvolit [režim automatického škálování](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale), který vám poskytne rozsah ru/s, který se podle potřeby dynamicky zvětšuje a zmenšuje.| 
    |**ID kolekce**|coll|`coll`Jako název nového kontejneru zadejte. Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Kapacita úložiště**|Pevná (10 GB)|Pro tuto aplikaci zadejte *pevná (10gb)* . Pokud vyberete možnost *neomezeno*, bude nutné vytvořit `Shard Key` , které položky budou vyžadovat.|
    |**Horizontálních oddílů klíč**| /_id| Ukázka popsaná v tomto článku nepoužívá klíč horizontálních oddílů, takže jeho nastavení na  */_ID* bude používat automaticky generované pole ID jako klíč horizontálních oddílů. Přečtěte si další informace o horizontálního dělení, označované taky jako dělení do oddílů, v [oddílu Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.
