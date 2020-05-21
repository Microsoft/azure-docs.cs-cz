---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 55236e3083ded0e8e07afd406e87f61f53451e9d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701718"
---
Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření rozhraní API Azure Cosmos DB pro databázi a kontejner MongoDB. 

1. Vyberte **Průzkumník dat**  >  **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    ![Průzkumník dat na portálu Azure Portal – podokno Přidat kontejner](./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png)

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|inženýr|Jako název nové databáze zadejte *DB* . Názvy databází musí obsahovat 1 až 255 znaků a nesmí obsahovat ani mezeru `/, \\, #, ?` . Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit. Můžete také zvolit [režim autopilot](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autopilot), který vám poskytne rozsah ru/s, který bude v případě potřeby dynamicky zvyšovat a snižovat.| 
    |**ID kolekce**|coll|Jako název nového kontejneru zadejte *coll* . Pro ID kontejnerů platí stejné požadavky týkající se použitých znaků jako pro názvy databází.|
    |**Kapacita úložiště**|Pevná (10 GB)|Pro tuto aplikaci zadejte *pevná (10gb)* . Pokud vyberete možnost *neomezeno*, bude nutné vytvořit `Shard Key` , které položky budou vyžadovat.|
    |**Horizontálních oddílů klíč**| /_id| Ukázka popsaná v tomto článku nepoužívá klíč horizontálních oddílů, takže jeho nastavení na */_ID* bude používat automaticky generované pole ID jako klíč horizontálních oddílů. Přečtěte si další informace o horizontálního dělení, označované taky jako dělení do oddílů, v [oddílu Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)|
        
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.