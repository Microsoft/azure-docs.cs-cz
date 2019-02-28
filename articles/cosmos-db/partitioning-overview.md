---
title: Dělení ve službě Azure Cosmos DB
description: Přehled dělení ve službě Azure Cosmos DB.
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: a8db510bea57fa3d6ee873571e586bcef7508b26
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961631"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Dělení ve službě Azure Cosmos DB

Azure Cosmos DB používá dělení škálování jednotlivých kontejnerů v databázi a potřebám výkonu vaší aplikace. Při dělení položek v kontejneru jsou rozdělené do různé podmnožiny volá *logické oddíly*. Logické oddíly jsou vytvářena závislosti na hodnotě *klíč oddílu* , který je asociován s každou položku v kontejneru. Všechny položky v logického oddílu mají stejnou hodnotu klíče oddílu.

Například kontejner obsahuje dokumenty. Má jedinečnou hodnotu pro každý dokument `UserID` vlastnost. Pokud `UserID` slouží jako klíč pro položky v kontejneru a jsou jedinečné 1 000 `UserID` hodnoty, 1 000 logické oddíly jsou vytvořeny pro kontejner.

Kromě klíč oddílu, který určuje položky logický oddíl má každá položka v kontejneru *ID položky* (jedinečný v rámci logického oddílu). Kombinace klíče oddílu a ID položky vytvoří položky *index*, který jednoznačně identifikuje položku.

[Zvolit klíč oddílu](partitioning-overview.md#choose-partitionkey) je důležité rozhodnutí, která bude mít vliv na výkon vaší aplikace.

## <a name="managing-logical-partitions"></a>Správa logickými oddíly

Azure Cosmos DB transparentně a automaticky spravuje umístění logické oddíly na fyzické oddíly (serverové infrastruktury) efektivně vyhovět potřebám škálovatelnost a výkon kontejneru. Požadavky na propustnost a úložiště aplikace zvýšit, Azure Cosmos DB přesune logické oddílů, aby automaticky rozložit zatížení mezi větší počet serverů. 

Azure Cosmos DB používá hodnotu hash na základě dělení šíření logické oddíly napříč fyzickými oddíly. Azure Cosmos DB hashuje hodnotu klíče oddílu položky. Výslednou hodnotu hash určuje fyzický oddíl. Potom služby Azure Cosmos DB přiděluje prostoru klíče oddílu hodnoty hash klíče rovnoměrně mezi všemi fyzickými oddíly.

Dotazy, které přístup k datům v rámci jednoho oddílu jsou cenově výhodnější než dotazy, které přístup k více oddílů. Transakce (v uložených procedur a aktivačních událostí) jsou povoleny pouze u položek do jednoho logického oddílu.

Další informace o tom, jak službu Azure Cosmos DB spravuje oddíly, naleznete v tématu [logické oddíly](partition-data.md). (Není nutné znát tyto podrobnosti k sestavení nebo spouštění aplikací.)

## <a id="choose-partitionkey"></a>Zvolit klíč oddílu

Při výběru klíče oddílu, zvažte následující podrobnosti:

* Jeden logický oddíl má maximální limit je 10 GB úložiště.  

* Dělené kontejnery mají minimální propustnost 400 jednotek žádostí za sekundu (RU/s). Požadavky na stejný klíč oddílu může mít maximálně propustnosti, který je přidělen k oddílu. Pokud požadavky překročí přidělené propustnost, žádosti se míra časově omezené. Proto je důležité vyberte klíč oddílu, který není výsledkem "aktivní body" v rámci vaší aplikace.

* Zvolte klíč oddílu, který se šíří zatížení rovnoměrně mezi všechny oddíly a rovnoměrně v čase. Výběr klíče oddílu by měl vyvážily potřeby efektivní oddílu dotazy a transakce s cílem distribuce položky napříč několika oddíly, abyste dosáhli škálovatelnosti.

* Zvolte klíč oddílu, který má široký rozsah hodnot a vzorce přístupu, které jsou rovnoměrně rozloženy logické oddíly. Díky tomu rozloženy sadu logické oddíly, dat a aktivity ve vašem kontejneru tak, aby prostředky úložiště dat a propustnost je možné distribuovat napříč logickými oddíly.

* Kandidáty pro klíče oddílů může obsahovat vlastnosti, které se často zobrazují jako filtr v dotazech. Dotazy můžete efektivně směrovat včetně klíče oddílu v predikátu filtru.

## <a name="next-steps"></a>Další postup

* Další informace o [oddíly](partition-data.md).
* Další informace o [zřízenou propustnost v databázi Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuce ve službě Azure Cosmos DB](distribute-data-globally.md).
