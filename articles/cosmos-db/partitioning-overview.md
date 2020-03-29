---
title: Dělení ve službě Azure Cosmos DB
description: Informace o dělení v Azure Cosmos DB, osvědčené postupy při výběru klíče oddílu a jak spravovat logické oddíly
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251866"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Dělení ve službě Azure Cosmos DB

Azure Cosmos DB používá dělení škálování jednotlivých kontejnerů v databázi tak, aby vyhovovaly potřebám výkonu vaší aplikace. Při dělení jsou položky v kontejneru rozděleny do odlišných podmnožiny *nazývaných logické oddíly*. Logické oddíly jsou vytvořeny na základě hodnoty *klíče oddílu,* který je přidružen ke každé položce v kontejneru. Všechny položky v logickém oddílu mají stejnou hodnotu klíče oddílu.

Kontejner například obsahuje položky. Každá položka má jedinečnou `UserID` hodnotu pro vlastnost. Pokud `UserID` slouží jako klíč oddílu pro položky v kontejneru `UserID` a existují 1 000 jedinečné hodnoty, 1 000 logické oddíly jsou vytvořeny pro kontejner.

Kromě klíče oddílu, který určuje logický oddíl položky, má každá položka v kontejneru *ID položky* (jedinečné v rámci logického oddílu). Kombinace klíče oddílu a ID položky vytvoří *index*položky , který jednoznačně identifikuje položku.

[Výběr klíče oddílu](partitioning-overview.md#choose-partitionkey) je důležité rozhodnutí, které ovlivní výkon vaší aplikace.

## <a name="managing-logical-partitions"></a>Správa logických oddílů

Azure Cosmos DB transparentně a automaticky spravuje umístění logických oddílů na fyzické oddíly efektivně uspokojit škálovatelnost a požadavky na výkon kontejneru. Jako propustnost a požadavky na úložiště aplikace zvýšit, Azure Cosmos DB přesune logické oddíly automaticky rozložit zatížení přes větší počet serverů. 

Azure Cosmos DB používá dělení založené na hash k rozložení logických oddílů mezi fyzické oddíly. Azure Cosmos DB hashes hodnota klíče oddílu položky. Výsledek hashed určuje fyzický oddíl. Azure Cosmos DB pak přiděluje klíčové místo klíče oddílu hashe rovnoměrně přes fyzické oddíly.

Dotazy, které přistupují k datům v rámci jednoho logického oddílu, jsou nákladově efektivnější než dotazy, které přistupují k více oddílům. Transakce (v uložených procedurách nebo aktivačních událostech) jsou povoleny pouze proti položkám v jednom logickém oddílu.

Další informace o tom, jak Azure Cosmos DB spravuje oddíly, najdete [v tématu logické oddíly](partition-data.md). (Není nutné pochopit vnitřní podrobnosti pro sestavení nebo spuštění aplikací, ale přidáno zde pro zvědavé čtenáře.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Výběr klíče oddílu

Následující je vhodné pokyny pro výběr klíče oddílu:

* Jeden logický oddíl má horní limit 20 GB úložiště.  

* Kontejnery Azure Cosmos mají minimální propustnost 400 jednotek požadavku za sekundu (RU/s). Když je zřízena propustnost v databázi, minimální ru na kontejner je 100 jednotek požadavku za sekundu (RU/s). Požadavky na stejný klíč oddílu nesmí překročit propustnost, která je přidělena oddílu. Pokud požadavky překročí přidělenou propustnost, požadavky jsou omezeny sazbou. Proto je důležité vybrat klíč oddílu, který nemá za následek "aktivní body" v rámci aplikace.

* Zvolte klíč oddílu, který má širokou škálu hodnot a vzory přístupu, které jsou rovnoměrně rozloženy mezi logické oddíly. To pomáhá šířit data a aktivity v kontejneru napříč sadou logických oddílů, takže prostředky pro ukládání dat a propustnost lze distribuovat mezi logické oddíly.

* Zvolte klíč oddílu, který rovnoměrně rozloží úlohu ve všech oddílech a rovnoměrně v průběhu času. Vaše volba klíče oddílu by měla vyvážit potřebu efektivní oddíl dotazy a transakce proti cíli distribuce položek mezi více oddílů k dosažení škálovatelnosti.

* Kandidáti pro klíče oddílů může obsahovat vlastnosti, které se často zobrazují jako filtr v dotazech. Dotazy lze efektivně směrovat zahrnutím klíče oddílu do predikátu filtru.

## <a name="next-steps"></a>Další kroky

* Další informace o [dělení a horizontální škálování v Azure Cosmos DB](partition-data.md).
* Informace o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
