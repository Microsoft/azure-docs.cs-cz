---
title: Vytváření oddílů v Azure Cosmos DB
description: Přečtěte si o dělení Azure Cosmos DB, osvědčených postupech při volbě klíče oddílu a o tom, jak spravovat logické oddíly.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251866"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Vytváření oddílů v Azure Cosmos DB

Azure Cosmos DB používá dělení ke škálování jednotlivých kontejnerů v databázi tak, aby splňovaly požadavky vaší aplikace na výkon. Při dělení jsou položky v kontejneru rozděleny do samostatných dílčích množin nazývaných *logické oddíly*. Logické oddíly se vytvoří na základě hodnoty *klíče oddílu* , který je spojený s každou položkou v kontejneru. Všechny položky v logickém oddílu mají stejnou hodnotu klíče oddílu.

Například kontejner obsahuje položky. Každá položka má jedinečnou hodnotu pro vlastnost `UserID`. Pokud `UserID` slouží jako klíč oddílu pro položky v kontejneru a je 1 000 jedinečných `UserID`ch hodnot, pro kontejner se vytvoří logické oddíly 1 000.

Kromě klíče oddílu, který určuje logický oddíl položky, každá položka v kontejneru má *ID položky* (jedinečné v rámci logického oddílu). Kombinování klíče oddílu a ID položky vytvoří *index*položky, který položku jednoznačně identifikuje.

[Výběr klíče oddílu](partitioning-overview.md#choose-partitionkey) je důležité rozhodnutí, které bude mít vliv na výkon vaší aplikace.

## <a name="managing-logical-partitions"></a>Správa logických oddílů

Azure Cosmos DB transparentně a automaticky spravuje umístění logických oddílů na fyzických oddílech, aby bylo možné efektivně splnit požadavky na škálovatelnost a výkon kontejneru. V rámci zvýšení propustnosti a požadavků na úložiště se Azure Cosmos DB přesune logické oddíly a automaticky se rozprostře zatížení mezi větším počtem serverů. 

Azure Cosmos DB pro rozprostření logických oddílů mezi fyzickými oddíly používá dělení na základě hodnoty hash. Azure Cosmos DB hodnota hash hodnot klíče oddílu položky. Výsledek s hodnotou hash Určuje fyzický oddíl. Pak Azure Cosmos DB přiděluje klíčové místo hodnot hash klíče oddílu rovnoměrně napříč fyzickými oddíly.

Dotazy, které přistupují k datům v rámci jednoho logického oddílu, jsou cenově výhodnější než dotazy, které přistupují k více oddílům. Transakce (v uložených procedurách nebo triggerech) jsou povoleny pouze proti položkám v jednom logickém oddílu.

Další informace o tom, jak Azure Cosmos DB spravuje oddíly, najdete v tématu [logické oddíly](partition-data.md). (Není nutné porozumět interním podrobnostem pro sestavování a spouštění aplikací, ale zde jste přidaní pro čtečku zajímá.)

## <a id="choose-partitionkey"></a>Výběr klíče oddílu

Následuje dobrý návod pro výběr klíče oddílu:

* Jeden logický oddíl má horní limit 20 GB úložiště.  

* Kontejnery Azure Cosmos mají minimální propustnost 400 jednotek žádostí za sekundu (RU/s). V případě, že je v databázi zajištěna propustnost, minimální počet ru na kontejner je 100 jednotek žádostí za sekundu (RU/s). Požadavky na stejný klíč oddílu nepřesahují propustnost, která je přidělena oddílu. Pokud žádosti překročí přidělenou propustnost, požadavky jsou omezené na rychlost. Proto je důležité vybrat klíč oddílu, který v rámci aplikace nevede k "aktivním skvrnám".

* Vyberte klíč oddílu, který má celou řadu hodnot a vzory přístupu, které jsou rovnoměrně rozloženy mezi logické oddíly. To pomáhá rozprostřít data a aktivity ve vašem kontejneru napříč sadou logických oddílů, aby bylo možné distribuovat prostředky pro ukládání a propustnost dat napříč logickými oddíly.

* Vyberte klíč oddílu, který rovnoměrně rozšíří úlohy napříč všemi oddíly a rovnoměrně v průběhu času. Váš výběr klíče oddílu by měl vyrovnávat nutnost efektivních dotazů na oddíly a transakcí s cílem distribuovat položky mezi více oddíly, aby se dosáhlo škálovatelnosti.

* Kandidáti na klíče oddílů mohou obsahovat vlastnosti, které se často zobrazují jako filtr v dotazech. Dotazy lze efektivně směrovat zahrnutím klíče oddílu do predikátu filtru.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [dělení a horizontálním škálování v Azure Cosmos DB](partition-data.md).
* Přečtěte si o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Přečtěte si o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
