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
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754312"
---
Můžete teď pomocí nástroje Průzkumník dat na webu Azure Portal k vytvoření databáze a kontejneru. 

1. Klikněte na tlačítko **Průzkumník dat** > **nový kontejner**. 
    
    **Přidat kontejner** úplně vpravo se zobrazí oblast, budete možná muset posunout doprava a prohlédněte si ho.

    ![Na webu Azure portal Průzkumník dat, okno Přidat kontejner](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. V **přidat kontejner** zadejte nastavení pro nový kontejner.

    |Nastavení|Navrhovaná hodnota|Popis
    |---|---|---|
    |**ID databáze**|Úlohy|Jako název nové databáze zadejte *Tasks*. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky /, \\, #, ? ani koncové mezery. Zkontrolujte **propustnosti zřídit databáze** možnost, umožňuje sdílet propustnosti zřízené do databáze ve všech kontejnerů v databázi. Tato možnost pomáhá také úspory nákladů. |
    |**Propustnost**|400|Ponechte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.| 
    |**ID kontejneru**|Položky|Zadejte *položky* jako název pro nový kontejner. Identifikátory kontejnerů mají stejné požadavky na znaky jako názvy databází.|
    |**Klíč oddílu**| /kategorie| Ukázku popsanou v tomto článku používá */category* jako klíč oddílu. Nastavení klíče oddílu umožňuje službě Azure Cosmos DB škálovat vaši kolekci pro potřeby úložiště a propustnost vaší aplikace. Obecně je dobrou volbou klíč oddílu, pokud má širokou škálu různých hodnot a výsledky v rovnoměrná distribuce objemu úložiště a žádost o celé zatížení. [Další informace o dělení.](../articles/cosmos-db/partitioning-overview.md)|
    
    Kromě předchozích nastavení, můžete volitelně přidat **jedinečné klíče** pro kontejner. V tomto příkladu ponecháme toto pole prázdné. Jedinečné klíče umožňují vývojářům přidat do databáze vrstvu integrity dat. Vytvořením zásady jedinečného klíče při vytváření kontejneru zajistíte jedinečnost jedné nebo více hodnot pro klíč oddílu. Další informace najdete v článku [Jedinečné klíče ve službě Azure Cosmos DB](../articles/cosmos-db/unique-keys.md).
    
    Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner.

