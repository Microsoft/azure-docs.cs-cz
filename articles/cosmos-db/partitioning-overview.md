---
title: Dělení ve službě Azure Cosmos DB
description: Přehled dělení ve službě Azure Cosmos DB
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d9bd1bec6d1df6fab634c5d929cb778abbd3d13d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478402"
---
# <a name="partitioning-in-azure-cosmos-db---overview"></a>Dělení ve službě Azure Cosmos DB – přehled

Dělení je technika používaná službou Cosmos DB škálování jednotlivých kontejnerů v databázi a potřebám výkonu vaší aplikace. Pomocí dělení položek v kontejneru dělí do různých podmnožiny, označované jako logické oddíly. Logické oddíly se vytvoří na základě hodnoty vlastnosti klíče oddílu spojené s každou položku.

Logický oddíl je jedinečnou podmnožinu položky v kontejneru. Položky v logického oddílu jsou označeny hodnotu klíče oddílu, tu potom sdílejí všechny položky v logického oddílu.  Představte si třeba kontejner obsahující dokumenty a má každý dokument `UserID` vlastnost.  Pokud `UserID` slouží jako klíč pro položky v kontejneru a existují 1000 jedinečný `UserID` hodnoty, 1000 logické oddíly se vytvoří pro kontejner.

Má každá položka v kontejneru **klíč oddílu** , který určuje položky **logický oddíl**, a každá položka má také **id položky** (který je jedinečný v rámci logické oddíl).  **Index** z položky jednoznačně identifikuje a je vytvořen pomocí kombinace klíče oddílu a id položky.

Volba klíče oddílu je důležité rozhodnutí, která bude mít vliv na výkon vaší aplikace.  Další informace najdete v tématu [zvolit klíč oddílu](partitioning-overview.md#choose-partitionkey) článek obsahuje podrobné pokyny.

## <a name="logical-partition-management"></a>Logický oddíl správy

Cosmos DB transparentně a automaticky spravuje umístění logické oddíly na fyzické oddíly (server infrastruktury) efektivně vyhovět potřebám škálovatelnost a výkon kontejneru. Požadavky na propustnost a úložiště aplikace zvýšit, Cosmos DB přesune logické oddílů, aby automaticky rozložit zatížení mezi větší počet serverů. Další informace o tom, jak služby Cosmos DB spravuje oddíly, naleznete v tématu [logické oddíly](partition-data.md) článku. Není nutné znát tyto podrobnosti k sestavení nebo spouštění aplikací.

Cosmos DB používá hodnotu hash na základě dělení šíření logické oddíly napříč fyzickými oddíly.  Se službou Cosmos DB hashuje hodnotu klíče oddílu položky a výslednou hodnotu hash určuje fyzický oddíl. Cosmos DB přiděluje prostoru klíče oddílu klíče hash rovnoměrně mezi všemi fyzickými oddíly n.

Dotazy, které přístup k datům v rámci jednoho oddílu jsou cenově výhodnější než dotazy, které přístup k více oddílů. Transakce (v uložených procedur a aktivačních událostí) jsou povolené jenom u položek v rámci jednoho logického oddílu.  

## <a id="choose-partitionkey"></a>Zvolit klíč oddílu

Při výběru klíče oddílu, zvažte následující podrobnosti:

* Jednoho logického oddílu je povolen maximální limit je 10 GB úložiště.  

* Dělené kontejnery jsou nakonfigurovány s minimální propustnost 400 RU/s. Požadavky na stejný klíč oddílu může mít maximálně propustnost přidělené k oddílu. Pokud překročí přidělené propustnost, bude požadavky míra časově omezené. Proto je důležité vyberte klíč oddílu, který není výsledkem "aktivní body" v rámci vaší aplikace.

* Zvolte klíč oddílu, který se šíří zatížení rovnoměrně mezi všechny oddíly a rovnoměrně v čase.  Výběr klíče oddílu by měl vyvážily potřeby efektivní oddílu dotazy a transakce s cílem distribuce položky napříč několika oddíly, abyste dosáhli škálovatelnosti.

* Zvolte klíč oddílu, který má široký rozsah hodnot a vzorce přístupu, které jsou rovnoměrně rozloženy logické oddíly. Základní myšlenka je dat a aktivity ve vašem kontejneru rozloženy sadu logické oddíly, aby prostředky úložiště dat a propustnost je možné distribuovat napříč logickými oddíly.

* Kandidáty pro klíče oddílů může obsahovat vlastnosti, které se často zobrazují jako filtr v dotazech. Dotazy můžete efektivně směrovat včetně klíče oddílu v predikátu filtru.

## <a name="next-steps"></a>Další postup

* Další informace o [oddílů](partition-data.md)
* Další informace o [zřízenou propustnost v databázi Azure Cosmos DB](request-units.md)
* Další informace o [globální distribuce ve službě Azure Cosmos DB](distribute-data-globally.md)
