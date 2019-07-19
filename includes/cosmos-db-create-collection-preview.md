---
title: zahrnout soubor
description: zahrnout soubor
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68002677"
---
Nyní můžete použít nástroj Průzkumník dat v Azure Portal k vytvoření databáze a kontejneru. 

1. Klikněte na **Průzkumník dat** > **Nový kontejner**. 
    
    Úplně vpravo se zobrazí oblast **Přidat kontejner** , možná se budete muset posunout doprava, aby se zobrazila.

    ![Okno Azure Portal Průzkumník dat, přidat kontejner](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Na stránce **Přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky /, \\, #, ? ani koncové mezery. Ověřte možnost **zřízení propustnosti databáze** , která umožňuje sdílení propustnosti zřízené do databáze napříč všemi kontejnery v rámci databáze. Tato možnost také pomáhá při úsporách nákladů. |
    |**Propustnost**|400|Nechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Položky|Jako název nového kontejneru zadejte *položky* . ID kontejnerů mají stejné požadavky na znaky jako názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázka popsaná v tomto článku používá jako klíč oddílu */Category* . Nastavení klíče oddílu umožňuje Azure Cosmos DB škálování kolekce tak, aby splňovala požadavky vaší aplikace na úložiště a propustnost. Obecně platí, že vhodná volba klíče oddílu je taková, která má širokou škálu různých hodnot a vede k rovnoměrné distribuci úložiště a objemu požadavků napříč úlohami. [Přečtěte si další informace o dělení.](../articles/cosmos-db/partitioning-overview.md)|
    
    Kromě předchozích nastavení můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením jedinečné zásady klíčů při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot na klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.

