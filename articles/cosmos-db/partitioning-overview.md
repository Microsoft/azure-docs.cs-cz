---
title: Dělení ve službě Azure Cosmos DB
description: Přečtěte si o dělení Azure Cosmos DB, osvědčených postupech při volbě klíče oddílu a o tom, jak spravovat logické oddíly.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481829"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Dělení ve službě Azure Cosmos DB

Azure Cosmos DB používá dělení ke škálování jednotlivých kontejnerů v databázi tak, aby splňovaly požadavky vaší aplikace na výkon. Při dělení jsou položky v kontejneru rozděleny do samostatných dílčích množin nazývaných *logické oddíly*. Logické oddíly se vytvoří na základě hodnoty *klíče oddílu* , který je spojený s každou položkou v kontejneru. Všechny položky v logickém oddílu mají stejnou hodnotu klíče oddílu.

Například kontejner obsahuje položky. Každá položka má jedinečnou hodnotu pro `UserID` vlastnost. Pokud `UserID` slouží jako klíč oddílu pro položky v kontejneru a jsou-li k dispozici jedinečné `UserID` hodnoty 1 000, jsou pro kontejner vytvořeny logické oddíly 1 000.

Kromě klíče oddílu, který určuje logický oddíl položky, každá položka v kontejneru má *ID položky* (jedinečné v rámci logického oddílu). Kombinování klíče oddílu a *ID položky* vytvoří *index*položky, který položku jednoznačně identifikuje.

[Výběr klíče oddílu](partitioning-overview.md#choose-partitionkey) je důležité rozhodnutí, které bude mít vliv na výkon vaší aplikace.

## <a name="managing-logical-partitions"></a>Správa logických oddílů

Azure Cosmos DB transparentně a automaticky spravuje umístění logických oddílů na fyzických oddílech, aby bylo možné efektivně splnit požadavky na škálovatelnost a výkon kontejneru. V rámci zvýšení propustnosti a požadavků na úložiště se Azure Cosmos DB přesune logické oddíly, aby se zatížení automaticky rozšířilo do většího počtu fyzických oddílů. Můžete se dozvědět víc o [fyzických oddílech](partition-data.md#physical-partitions).

Azure Cosmos DB pro rozprostření logických oddílů mezi fyzickými oddíly používá dělení na základě hodnoty hash. Azure Cosmos DB hodnota hash hodnot klíče oddílu položky. Výsledek s hodnotou hash Určuje fyzický oddíl. Pak Azure Cosmos DB přiděluje klíčové místo hodnot hash klíče oddílu rovnoměrně napříč fyzickými oddíly.

Transakce (v uložených procedurách nebo triggerech) jsou povoleny pouze proti položkám v jednom logickém oddílu.

Další informace o tom, [jak Azure Cosmos DB spravuje oddíly](partition-data.md), najdete v tématu. (Není nutné porozumět interním podrobnostem pro sestavování a spouštění aplikací, ale zde jste přidaní pro čtečku zajímá.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Výběr klíče oddílu

Klíč oddílu má dvě komponenty: **cestu ke klíči oddílu** a **hodnotu klíče oddílu**. Představte si třeba položku {"userId": "Andrew", "worksFor": "Microsoft"} Pokud jako klíč oddílu zvolíte "userId", budou to tyto dvě komponenty klíče oddílu:

* Cesta ke klíči oddílu (například: "/userId"). Cesta ke klíči oddílu přijímá alfanumerické znaky a znaky podtržítka (_). Můžete také použít vnořené objekty pomocí standardního zápisu cesty (/).

* Hodnota klíče oddílu (například "Andrew"). Hodnota klíče oddílu může být typu String nebo numeric.

Další informace o limitech propustnosti, úložiště a délky klíče oddílu najdete v článku o [kvótách služby Azure Cosmos DB](concepts-limits.md) .

Výběr klíče oddílu je jednoduchá, ale důležitá volba návrhu v Azure Cosmos DB. Jakmile vyberete klíč oddílu, není možné ho změnit na místě. Pokud potřebujete změnit klíč oddílu, měli byste přesunout data do nového kontejneru s novým požadovaným klíčem oddílu.

Pro **všechny** kontejnery by měl klíč oddílu:

* Vlastnost, která má hodnotu, která se nemění. Pokud je vlastnost klíčem oddílu, nemůžete tuto hodnotu vlastnosti aktualizovat.
* Má vysokou kardinalitu. Jinými slovy, vlastnost by měla mít široké spektrum možných hodnot.
* Využití jednotky (RU) požadavků na rozprostření (RU) a úložiště dat rovnoměrně napříč všemi logickými oddíly. Tím se zajistí i využití a distribuce úložiště mezi fyzickými oddíly.

Pokud budete potřebovat [transakce s kyselými položkami](database-transactions-optimistic-concurrency.md#multi-item-transactions) v Azure Cosmos DB, bude nutné použít [uložené procedury nebo triggery](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Všechny uložené procedury a triggery založené na JavaScriptu jsou vymezeny na jeden logický oddíl.

## <a name="partition-keys-for-read-heavy-containers"></a>Klíče oddílů pro kontejnery pro čtení a těžký přístup

U většiny kontejnerů je výše uvedená kritéria při vybírání klíče oddílu všechno potřeba zvážit. U velkých kontejnerů pro čtení ale můžete chtít zvolit klíč oddílu, který se často objevuje jako filtr v dotazech. Dotazy mohou být [efektivně směrovány pouze na příslušné fyzické oddíly](how-to-query-container.md#in-partition-query) zahrnutím klíče oddílu do predikátu filtru.

Pokud jsou většina požadavků na vaše úlohy dotazy a většina vašich dotazů má pro stejnou vlastnost filtr rovnosti, může být tato vlastnost vhodnou volbou klíče oddílu. Pokud třeba často spustíte dotaz, který filtrujete `UserID` , pak výběrem možnosti `UserID` klíč oddílu snížíte počet [dotazů mezi oddíly](how-to-query-container.md#avoiding-cross-partition-queries).

Pokud je ale váš kontejner malý, pravděpodobně nemáte dostatek fyzických oddílů, abyste se museli starat o dopad na výkon dotazů mezi oddíly. Většina malých kontejnerů v Azure Cosmos DB vyžaduje pouze jeden nebo dva fyzické oddíly.

Pokud by váš kontejner mohl růst na více než několik fyzických oddílů, měli byste si vybrat klíč oddílu, který minimalizuje dotazy mezi oddíly. Váš kontejner bude vyžadovat více než několik fyzických oddílů, pokud je splněna jedna z následujících podmínek:

* Váš kontejner bude mít zajištěné více než 30 000 RU.
* Váš kontejner bude uchovávat více než 100 GB dat.

## <a name="using-item-id-as-the-partition-key"></a>Použití ID položky jako klíče oddílu

Pokud má váš kontejner vlastnost, která má široké spektrum možných hodnot, je pravděpodobné, že je vhodný klíč oddílu. Jedním z možných příkladů takové vlastnosti je *ID položky*. Pro malé kontejnery pro čtení nebo zapisovatelné kontejnery libovolné velikosti je *ID položky* přirozeně skvělým výběrem pro klíč oddílu.

*ID položky* systémové vlastnosti je zaručeno, že existuje v každé položce v kontejneru Cosmos. Můžete mít další vlastnosti, které reprezentují logické ID vaší položky. V mnoha případech jsou to také skvělé volby klíče oddílu ze stejných důvodů jako *ID položky*.

*ID položky* je skvělý výběr klíče oddílu z následujících důvodů:

* Existuje celá řada možných hodnot (jedno jedinečné *ID položky* na jednu položku).
* Vzhledem k tomu, že existuje jedinečné *ID položky* na jednu položku, má *ID položky* skvělou úlohu s rovnoměrně vyvážením využití ru a úložiště dat.
* Můžete snadno provádět efektivní čtení bodů, protože poznáte jeho *ID*, vždy znát klíč oddílu položky.

Je potřeba vzít v úvahu několik věcí, které byste měli zvážit při výběru *ID položky* jako klíč oddílu:

* Pokud je *ID položky* klíč oddílu, stane se jedinečným identifikátorem v celém rámci celého kontejneru. Nebudete moct mít položky, které mají duplicitní *ID položky*.
* Pokud máte kontejner pro čtení s velkým množstvím [fyzických oddílů](partition-data.md#physical-partitions), dotazy budou efektivnější, pokud mají filtr rovnosti s *ID položky*.
* Uložené procedury nebo triggery nemůžete spouštět v několika logických oddílech.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [dělení a horizontálním škálování v Azure Cosmos DB](partition-data.md).
* Přečtěte si o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Přečtěte si o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
