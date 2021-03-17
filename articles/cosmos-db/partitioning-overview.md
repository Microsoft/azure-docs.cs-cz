---
title: Dělení a horizontální škálování ve službě Azure Cosmos DB
description: Přečtěte si o dělení, logických, fyzických oddílech v Azure Cosmos DB, osvědčených postupech při volbě klíče oddílu a způsobu správy logických oddílů.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: b91c846b5a79125c1cee9c36ce81b5c3d3229ba9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627762"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Dělení a horizontální škálování ve službě Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB používá dělení ke škálování jednotlivých kontejnerů v databázi tak, aby splňovaly požadavky vaší aplikace na výkon. Při dělení jsou položky v kontejneru rozděleny do samostatných dílčích množin nazývaných *logické oddíly*. Logické oddíly se vytvoří na základě hodnoty *klíče oddílu* , který je spojený s každou položkou v kontejneru. Všechny položky v logickém oddílu mají stejnou hodnotu klíče oddílu.

Například kontejner obsahuje položky. Každá položka má jedinečnou hodnotu pro `UserID` vlastnost. Pokud `UserID` slouží jako klíč oddílu pro položky v kontejneru a jsou-li k dispozici jedinečné `UserID` hodnoty 1 000, jsou pro kontejner vytvořeny logické oddíly 1 000.

Kromě klíče oddílu, který určuje logický oddíl položky, každá položka v kontejneru má *ID položky* (jedinečné v rámci logického oddílu). Kombinování klíče oddílu a *ID položky* vytvoří *index* položky, který položku jednoznačně identifikuje. [Výběr klíče oddílu](#choose-partitionkey) je důležité rozhodnutí, které bude mít vliv na výkon vaší aplikace.

Tento článek vysvětluje vztah mezi logickými a fyzickými oddíly. Popisuje také osvědčené postupy pro dělení a poskytuje podrobné zobrazení, jak horizontální škálování funguje v Azure Cosmos DB. Není nutné porozumět těmto interním podrobnostem, abyste mohli vybrat klíč oddílu, ale my jsme je pokryli, abyste měli jasnou představu o tom, jak Azure Cosmos DB funguje.

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl obsahuje sadu položek, které mají stejný klíč oddílu. Například v kontejneru, který obsahuje data o výživě potravin, všechny položky obsahují `foodGroup` vlastnost. Můžete použít `foodGroup` jako klíč oddílu pro kontejner. Skupiny položek, které mají specifické hodnoty pro `foodGroup` , například, `Beef Products` `Baked Products` a `Sausages and Luncheon Meats` , tvoří rozdílné logické oddíly. Nemusíte si dělat starosti s odstraněním logického oddílu, pokud jsou podkladová data odstraněna.

Logický oddíl také definuje rozsah databázových transakcí. Položky v rámci logického oddílu lze aktualizovat pomocí [transakce s izolací snímku](database-transactions-optimistic-concurrency.md). Při přidání nových položek do kontejneru jsou nové logické oddíly transparentně vytvořeny systémem.

Počet logických oddílů ve vašem kontejneru není nijak omezený. Každý logický oddíl může ukládat až 20 GB dat. Dobrá volba klíče oddílu má široké spektrum možných hodnot. Například v kontejneru, kde všechny položky obsahují `foodGroup` vlastnost, může data v rámci `Beef Products` logického oddílu růst až 20 GB. [Výběr klíče oddílu](#choose-partitionkey) s široké škálou možných hodnot zajistí, že kontejner bude schopný škálovat.

## <a name="physical-partitions"></a>Fyzické oddíly

Kontejner se škáluje distribucí dat a propustnosti mezi fyzickými oddíly. Interně je jeden nebo více logických oddílů namapovaných na jeden fyzický oddíl. Obvykle menší kontejnery mají mnoho logických oddílů, ale vyžadují jenom jeden fyzický oddíl. Na rozdíl od logických oddílů jsou fyzické oddíly interní implementací systému a jsou výhradně spravovány Azure Cosmos DB.

Počet fyzických oddílů ve vašem kontejneru závisí na následujících:

* Stanovený počet propustnosti (každý jednotlivý fyzický oddíl může poskytovat propustnost až 10 000 jednotek žádostí za sekundu).
* Celkové úložiště dat (každý jednotlivý fyzický oddíl může ukládat až 50 GB data).

> [!NOTE]
> Fyzické oddíly jsou interní implementace systému a jsou výhradně spravované pomocí Azure Cosmos DB. Při vývoji řešení se nemusíte soustředit na fyzické oddíly, protože je nemůžete ovládat. Místo toho se zaměřte na klíče oddílů. Pokud zvolíte klíč oddílu, který rovnoměrně distribuuje spotřebu propustnosti napříč logickými oddíly, zajistíte rovnováhu propustnosti mezi fyzickými oddíly.

Celkový počet fyzických oddílů ve vašem kontejneru není nijak omezený. Jak vaše zřízená propustnost nebo velikost dat roste, Azure Cosmos DB automaticky vytvoří nové fyzické oddíly tím, že se rozdělí stávající. Rozdělení fyzického oddílu nemá vliv na dostupnost vaší aplikace. Po rozdělení fyzického oddílu budou všechna data v jednom logickém oddílu stále uložena ve stejném fyzickém oddílu. Rozdělení fyzického oddílu jednoduše vytvoří nové mapování logických oddílů na fyzické oddíly.

Propustnost zřízená pro kontejner je rozdělená rovnoměrně mezi fyzické oddíly. Návrh klíče oddílu, který nedistribuuje požadavky rovnoměrně, může mít za následek příliš mnoho požadavků směrovaných na malou podmnožinu oddílů, které se stanou "horkou". Hot partitions vede k neefektivnímu využití zřízené propustnosti, což může vést k omezení rychlosti a vyššímu počtu nákladů.

Fyzické oddíly kontejneru můžete zobrazit v části **úložiště** v okně **metriky** Azure Portal:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Zobrazení počtu fyzických oddílů" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Na výše uvedeném snímku obrazovky má kontejner `/foodGroup` jako klíč oddílu. Každý ze tří pruhů v grafu představuje fyzický oddíl. V imagi je **Rozsah klíčů oddílu** stejný jako fyzický oddíl. Vybraný fyzický oddíl obsahuje horní 3 nejvýznamnější velikosti logických oddílů:, a `Beef Products` `Vegetable and Vegetable Products` `Soups, Sauces, and Gravies` .

Pokud zřizujete propustnost 18 000 jednotek žádostí za sekundu (RU/s), pak každý ze tří fyzických oddílů může využít 1/3 celkové zřízené propustnosti. V rámci vybraného fyzického oddílu klíče logických oddílů `Beef Products` , `Vegetable and Vegetable Products` a `Soups, Sauces, and Gravies` mohou společně shromažďovat, využijte ru/s fyzického oddílu 6 000. Vzhledem k tomu, že zajištěná propustnost je rovnoměrně rozdělená napříč fyzickými oddíly kontejneru, je důležité zvolit klíč oddílu, který rovnoměrně distribuuje spotřebu propustnosti, a to [výběrem správného logického klíče oddílu](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Správa logických oddílů

Azure Cosmos DB transparentně a automaticky spravuje umístění logických oddílů na fyzických oddílech, aby bylo možné efektivně splnit požadavky na škálovatelnost a výkon kontejneru. V rámci zvýšení propustnosti a požadavků na úložiště se Azure Cosmos DB přesune logické oddíly, aby se zatížení automaticky rozšířilo do většího počtu fyzických oddílů. Můžete se dozvědět víc o [fyzických oddílech](partitioning-overview.md#physical-partitions).

Azure Cosmos DB pro rozprostření logických oddílů mezi fyzickými oddíly používá dělení na základě hodnoty hash. Azure Cosmos DB hodnota hash hodnot klíče oddílu položky. Výsledek s hodnotou hash Určuje fyzický oddíl. Pak Azure Cosmos DB přiděluje klíčové místo hodnot hash klíče oddílu rovnoměrně napříč fyzickými oddíly.

Transakce (v uložených procedurách nebo triggerech) jsou povoleny pouze proti položkám v jednom logickém oddílu.

Další informace o tom, [jak Azure Cosmos DB spravuje oddíly](partitioning-overview.md), najdete v tématu. (Není nutné porozumět interním podrobnostem pro sestavování a spouštění aplikací, ale zde jste přidaní pro čtečku zajímá.)

## <a name="replica-sets"></a>Sady replik

Každý fyzický oddíl se skládá ze sady replik, které se také označují jako [*sada replik*](global-dist-under-the-hood.md). Každá sada replik je hostitelem instance databázového stroje. Sada replik vytvoří data uložená v rámci fyzického oddílu odolného, vysoce dostupného a konzistentního. Každá replika, která tvoří fyzický oddíl, zdědí kvótu úložiště oddílu. Všechny repliky fyzického oddílu společně podporují propustnost, která je přidělena fyzickému oddílu. Azure Cosmos DB automaticky spravuje sady replik.

Menší kontejnery obvykle vyžadují jenom jeden fyzický oddíl, ale budou mít pořád aspoň 4 repliky.

Následující obrázek ukazuje, jak jsou logické oddíly namapovány na fyzické oddíly distribuované globálně:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Obrázek, který ukazuje Azure Cosmos DB dělení" border="false":::

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

*ID položky* systémové vlastnosti existuje v každé položce v kontejneru. Můžete mít další vlastnosti, které reprezentují logické ID vaší položky. V mnoha případech jsou to také skvělé volby klíče oddílu ze stejných důvodů jako *ID položky*.

*ID položky* je skvělý výběr klíče oddílu z následujících důvodů:

* Existuje celá řada možných hodnot (jedno jedinečné *ID položky* na jednu položku).
* Vzhledem k tomu, že existuje jedinečné *ID položky* na jednu položku, má *ID položky* skvělou úlohu s rovnoměrně vyvážením využití ru a úložiště dat.
* Můžete snadno provádět efektivní čtení bodů, protože poznáte jeho *ID*, vždy znát klíč oddílu položky.

Je potřeba vzít v úvahu několik věcí, které byste měli zvážit při výběru *ID položky* jako klíč oddílu:

* Pokud je *ID položky* klíč oddílu, stane se jedinečným identifikátorem v celém rámci celého kontejneru. Nebudete moct mít položky, které mají duplicitní *ID položky*.
* Pokud máte kontejner pro čtení s velkým množstvím [fyzických oddílů](partitioning-overview.md#physical-partitions), dotazy budou efektivnější, pokud mají filtr rovnosti s *ID položky*.
* Uložené procedury nebo triggery nemůžete spouštět v několika logických oddílech.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Přečtěte si o [globální distribuci v Azure Cosmos DB](distribute-data-globally.md).
* Naučte se [zřídit propustnost v kontejneru Azure Cosmos](how-to-provision-container-throughput.md).
* Naučte se [zřídit propustnost v databázi Azure Cosmos](how-to-provision-database-throughput.md).
