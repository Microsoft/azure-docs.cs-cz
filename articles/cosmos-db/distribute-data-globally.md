---
title: Distribuci dat globálně pomocí Azure Cosmos DB | Microsoft Docs
description: Další informace o škálování planetu geografická replikace, převzetí služeb při selhání a data obnovení pomocí globální databáze z databáze Cosmos Azure, služby globálně distribuované, podstoupí model databáze.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: b161fad822804ed0b2a6c7ad5315eca45984b19d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081485"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Jak se bude distribuovat dat globálně pomocí Azure Cosmos DB
Azure je všudypřítomný – má globální nároků přes 50 + zeměpisné oblasti a průběžně zvětšuje. S jeho globální přítomnosti jeden odlišné možnosti, které Azure nabízí pro jeho vývojáře je schopnost vytvářet, nasazovat a spravovat snadno globálně distribuované aplikace. 

[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje připraveného globální distribuční [elastické škálování propustnost a úložiště](../cosmos-db/partition-data.md) po celém světě, jednociferné milisekundu latence v 99th percentilu [pět dobře definovaný konzistence modely](consistency-levels.md)a zaručit vysoká dostupnost, všechny zálohovány pomocí [komplexní SLA špičkový](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje všechna vaše data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) bez nutnosti zabývat Správa schématu nebo index. Je více modelu služby a podporuje dokumentu, klíč hodnota, grafu a modely dat rodin sloupců. Jako nativně narození v rámci cloudové služby Azure Cosmos DB je pečlivě zkonstruován víceklientský a globální distribuci od základů nahoru.


![Azure Cosmos DB kolekce rozdělena na oddíly a distribuován do tří oblastí](./media/distribute-data-globally/global-apps.png)

**Jediný kontejner Azure Cosmos DB rozdělena na oddíly a distribuovaných nad několika oblastmi Azure**

Jak jsme se naučili při sestavování Azure Cosmos DB, přidání globální distribuční nelze chodím. Nemůže být "přišroubovány on" na "jedné lokality" databázový systém. Funkce nabízené globálně distribuované databáze nad rámec span, že tradiční zeměpisné katastrofě obnovení (Geo-DR) nabízí "jedné lokalitě" databází. Databáze jedné lokalitě nabídky Geo-zotavení po Havárii schopnosti jsou striktní podmnožinu globálně distribuované databáze. 

S připraveného globální distribuční databázi Cosmos Azure, nemají vývojářům vytvářet své vlastní generování uživatelského rozhraní replikace díky využití architektury buď vzoru Lambda (například [AWS DynamoDB replikace](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) přes protokol databáze nebo nástrojem provádění "dvojité zápisy" nad několika oblastmi. Provedeme *není* doporučujeme tyto přístupy, protože je to možné zkontrolujte správnost takové přístupů a zadejte zvukové SLA. 

V tomto článku poskytujeme přehled možností globální distribuční databázi Cosmos Azure. Můžeme také popisují Azure Cosmos DB jedinečný přístup k poskytování komplexní SLA. 

## <a id="EnableGlobalDistribution"></a>Povolení připraveného globální distribuční
Azure Cosmos DB poskytuje následující funkce, které vám umožní snadno zápisu globálně distribuované aplikace. Tyto možnosti jsou dostupné prostřednictvím založenou na poskytovateli prostředků Azure Cosmos DB [rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) a také na portálu Azure.

V následujícím videu zobrazíte funkce připraveného globální distribuce v Azure Cosmos DB v akci.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Všudypřítomná regionální přítomnosti 
Azure je neustále rostoucí jeho zeměpisné přítomnosti tak, že převedou [nové oblasti](https://azure.microsoft.com/regions/) online. Klasifikovaný Azure Cosmos DB *základní služby* v Azure a je k dispozici ve všech oblastech nové Azure ve výchozím nastavení. To umožňuje přidružit geografické oblasti databázový účet Azure Cosmos DB co nejrychleji Azure otevře novou oblast pro firmy.

### <a id="UnlimitedRegionsPerAccount"></a>Přidružení neomezený počet oblasti s vaším účtem Azure Cosmos DB databáze
Azure Cosmos DB umožňuje přidružit libovolný počet oblastí Azure databázový účet Azure Cosmos DB. Mimo omezení geografického vymezení (například Čína, Německo) neexistují žádná omezení počtu oblastí, které může být spojeno s vaším účtem databáze Azure Cosmos DB. Následující obrázek znázorňuje databázový účet nakonfigurován tak, aby přes 25 oblastech Azure.  

![Účet databáze Azure Cosmos DB pokrývání uzlů 25 oblastí Azure](./media/distribute-data-globally/spanning-regions.png)

**Klienta Azure Cosmos DB databáze účet STA 25 oblastí Azure**


### <a id="PolicyBasedGeoFencing"></a>Na základě zásad geografického vymezení
Azure Cosmos DB je navržen pro podporu geografického vymezení na základě zásad. Geografického vymezení je důležitou součástí zajistit vedení a dodržování předpisů omezení dat a může zabránit přidružení v určité oblasti s vaším účtem. Příklady geografického vymezení zahrnovat (ale nejsou omezeny) rozsahu globální distribuce do oblasti v rámci svrchovaných cloudu (například Čína a Německo) nebo hranici zdanění government (například Austrálie). Zásady jsou řízena pomocí metadat vašeho předplatného Azure.

### <a id="DynamicallyAddRegions"></a>Dynamicky přidávat a odebírat oblastí
Azure Cosmos DB umožňuje přidat (přidružení) nebo odebrat (zrušit přidružení) oblasti z vašeho účtu databáze v libovolném bodě v čase (viz [předchozí obrázek](#UnlimitedRegionsPerAccount)). Na základě paralelní replikaci dat mezi oddílů Azure Cosmos DB zajistí, pokud přidá novou oblast, není k dispozici pro operace do 30 minut kdekoliv na světě (za předpokladu, že vaše data je 100 TBs nebo méně). 

### <a id="FailoverPriorities"></a>Priorit převzetí služeb při selhání
Azure Cosmos DB k řízení přesné pořadí regionální převzetí služeb při selhání v případě výpadku, umožňuje přidružit *s prioritou* s různých oblastech přidružený k databázi účtu (viz následující obrázek). Azure Cosmos DB zajistí, že pořadí automatické převzetí služeb při selhání dojde v pořadí podle priority, kterou jste zadali. Další informace o místní převzetí služeb při selhání najdete v tématu [automatické regionální převzetí služeb při selhání pro kontinuitu podnikových procesů v Azure Cosmos DB](regional-failover.md).


![Konfigurace priorit převzetí služeb při selhání v Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

**Klient Azure Cosmos databáze můžete nakonfigurovat pořadí priorit převzetí služeb při selhání (pravé podokno) pro oblasti, které jsou spojené s účtem databáze**

### <a id="ConsistencyLevels"></a>Více, dobře definovaný konzistence modely pro globálně distribuované databáze
Podporuje Azure Cosmos DB [více modelů dobře definovaný, intuitivní a praktické konzistence](consistency-levels.md) zajišťoval SLA. Můžete vybrat konkrétní konzistence modelu (ze seznamu dostupných možností) v závislosti na zatížení/scénáře. 

### <a id="TunableConsistency"></a>Přizpůsobitelné konzistence pro globální replikované databáze
Azure Cosmos DB umožňuje prostřednictvím kódu programu přepsat a uvolnit výchozí konzistence volba na základě žádosti za běhu. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamicky konfigurovat pro čtení a zápisu oblastí
Azure Cosmos DB umožňuje nakonfigurovat oblastí (přidružený k databázi) pro "číst", "zápisu" nebo "pro čtení a zápis" oblasti. 

### <a id="ElasticallyScaleThroughput"></a>Elasticky škálování propustnost mezi oblastmi Azure
Je možné Elasticky škálovat kontejner Azure Cosmos DB podle zřizování propustnost prostřednictvím kódu programu. Propustnost se použije pro všechny oblasti, kterou kontejneru Azure Cosmos DB je distribuován v.

### <a id="GeoLocalReadsAndWrites"></a>Geograficky místní čte a zapisuje
Hlavní výhoda globálně distribuované databáze je, že nabízí nízkou latencí přístup k datům kdekoli v celém světě. Azure Cosmos DB nabízí nízkou latencí čte a zapisuje na 99th percentilu po celém světě. Zajišťuje, že se zpracovávají všechny operace čtení z oblasti nejbližší (místní). K obsluze požadavek čtení, se používá místní oblast, ve kterém se objeví čtení kvora. Totéž platí i pro zápis. Až po většinu repliky mít spolehlivě potvrzené zápis místně, ale bez se ověřované vrácení na vzdálené repliky potvrdit zápisů, potvrdí se zápis. Uvést odlišně, funguje protokol replikace databáze Cosmos Azure v části za předpokladu, že jsou vždy místní oblast, kde byl vydán požadavek kvor ke čtení a zápisu.

### <a id="ManualFailover"></a>Ruční převzetí služeb při selhání
Azure Cosmos DB umožňuje aktivovat převzetí služeb při selhání databázový účet ověřit *koncová* dostupnosti vlastnosti bude celá aplikace (mimo databázi). Vzhledem k tomu, že jsou zaručena bezpečnost a vlastnosti liveness selhání zjišťování a vedoucí volba, Azure Cosmos DB zaručuje *nulové ztráty dat* operace klienta iniciované ruční převzetí služeb při selhání.

### <a id="AutomaticFailover"></a>Automatické převzetí služeb při selhání
Azure Cosmos DB podporuje automatické převzetí služeb při selhání během jeden nebo více regionální výpadků. Při selhání regionální Azure Cosmos DB udržuje latenci pro čtení, dostupnosti provozu, konzistence a propustnost SLA. Azure Cosmos DB poskytuje horní mez pro dobu trvání na dokončení operace automatické převzetí služeb při selhání. Toto je okno potenciální ztrátě dat během výpadku místní.

### <a id="GranularFailover"></a>Určená pro členitostí v různých převzetí služeb při selhání
Možnosti automatického nebo ručního převzetí služeb při selhání jsou aktuálně umístěné na členitost databázového účtu. Poznámka: interně Azure DB Cosmos je určená k poskytování *automatické* převzetí služeb při selhání na podrobnější databáze, kontejneru nebo dokonce oddílu (kontejner vlastnící rozsah klíčů). 

### <a id="MultiHomingAPIs"></a>Více domovských stránek v Azure Cosmos DB
Azure Cosmos DB umožňuje komunikovat s databází pomocí *logické* (vázané na oblast) nebo *fyzické* koncových bodů (specifické pro oblast). Použití logické koncové body zajišťuje, že aplikace může transparentně byly vícedomé v případě selhání. K tomu koncový bod fyzické poskytuje jemně odstupňovanou kontrolu do aplikace pro přesměrování čte a zapisuje do určitých oblastí.

Můžete najít informace o tom, jak nakonfigurovat čtení předvoleb pro [rozhraní SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [tabulky API](../cosmos-db/tutorial-global-distribution-table.md), a [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) v těchto článcích.

### <a id="TransparentSchemaMigration"></a>Migrace databáze transparentní a konzistentní schéma a index 
Azure Cosmos DB je plně [vázané na schéma](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Jedinečný návrhu databázového stroje umožňuje Azure Cosmos databáze automaticky a synchronně všechna data při přijímání, indexu bez nutnosti žádné schéma nebo sekundárních indexů od uživatele. To umožňuje rychle iterovat globálně distribuované aplikace bez starostí o migraci databáze schéma a index nebo koordinace aplikace s více fáze zavedení změn schématu uživatelům. Azure Cosmos DB zaručuje, že všechny změny indexování zásady explicitně které jste udělali nezpůsobovalo neustálé snížení výkon nebo dostupnost.  

### <a id="ComprehensiveSLAs"></a>Komplexní SLA (kromě vysoké dostupnosti)
Jako služba globálně distribuovanou databázi, databázi Cosmos Azure nabízí dobře definovaný a komplexní smlouvy SLA pro **dostupnosti**, **latence**, **propustnost**a **konzistence** databáze spuštěné v globálním měřítku, bez ohledu na počet oblastí přidružený k databázi.  

## <a id="LatencyGuarantees"></a>Latence záruky
Hlavní výhoda globálně distribuované databáze služby jako databázi Cosmos Azure je na nabídku s nízkou latencí přístup k datům kdekoli v celém světě. Azure Cosmos DB nabízí zaručenou s nízkou latencí v 99th percentilu pro různé operace databáze. Replikace protokol, který využívá Azure Cosmos DB zajistí, že databázových operací (čtení a zápisy) jsou vždycky probíhá v oblasti místní pro tohoto klienta. Latence SLA databázi Cosmos Azure poskytuje záruky na 99th percentilu pro čtení, zápisu (synchronně) indexované a dotazů pro různé velikosti požadavku a odpovědi. Záruky latence pro zápis zahrnují trvanlivý většinu kvora potvrzení v rámci místní oblasti.

### <a id="LatencyAndConsistency"></a>Čekací doba na relaci s konzistence 
Globálně distribuované služby nabízí silnou konzistenci v globálně distribuované instalační program musí synchronně replikovat zápisů nebo synchronně provádět mezi oblastmi čtení. Rychlosti lehký a spolehlivost sítě WAN stanoví, že silnou konzistenci způsobí vyšší latence a databázové operace je omezená dostupnost. Proto aby bylo možné nabízejí zaručit nízkou latenci na 99th percentilu a 99,99 % dostupnost pro všechny účty jedné oblasti a všechny oblasti s více účty s volný konzistence a 99.999 % dostupnosti pro všechny účty databáze více oblast, služba musíte použít asynchronní replikaci. Tato naopak vyžaduje službu musí také nabízí [dobře definovaný, volný konzistence modely](consistency-levels.md) – slabší než silné (a nabídnout nízkou latenci a dostupnosti záruky) a v ideálním případě silnější než "případné" konzistence (s intuitivní programovací model).

Azure Cosmos DB zajistí, že operace čtení není potřeba kontaktovat nad několika oblastmi k poskytování úrovně záruku konkrétní konzistence repliky. Podobně zajišťuje, že operace zápisu nejsou zablokování při se replikuje data přes všechny oblasti tedy zápisy se asynchronně replikují přes oblasti). Pro účty databáze více oblasti obou silné a také více úrovní konzistence volný jsou k dispozici. 

### <a id="LatencyAndAvailability"></a>Čekací doba na relaci s dostupností 
Latence a dostupnost se sociálními stejné mince. Posuzování latence operace v stabilního stavu a dostupnost přítomnosti selhání a síťové oddíly. Z hlediska aplikace je pomalá běžící operace databáze lišit od databázi, která je k dispozici. 

K rozlišení vysokou latencí z nejsou dostupné, poskytuje Azure Cosmos DB absolutní horní mez pro latenci různé operace databáze. Pokud operace databáze trvá déle, než horní hranice k dokončení, vrátí Azure Cosmos DB vypršení časového limitu. SLA dostupnosti Azure Cosmos DB zajistí, že časové limity počítají proti smlouva SLA o dostupnosti. 

### <a id="LatencyAndThroughput"></a>Čekací doba na relaci s propustností
Azure Cosmos DB neprovede můžete zvolit latence a propustnosti. To ctí SLA pro obě latence v 99th percentilu a přináší, když máte zřízenou propustnost. 

## <a id="ConsistencyGuarantees"></a>Záruky konzistence
Když [silnou konzistenci modelu](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) je standard zlatý z dat programovatelnosti kompromisnímu zvládnutí cena vyšší latence (v stabilního stavu) a nižší dostupnost (při krátkodobém počet selhání). 

Azure Cosmos DB nabízí dobře definovaný programovací model pro vás důvod o konzistence replikovaná data. Pokud chcete povolit, můžete snadno vytvořit globálně distribuované aplikace s více funkci schopnosti, jsou navrženy se bez ohledu na oblast a nezávislé z oblasti z kde probíhá čtení a zápisy modely konzistence vystavené Azure Cosmos DB zpracovat. 

Azure Cosmos DB konzistence SLA zaručuje, že 100 % požadavků na čtení bude vyhovovat záruku konzistence pro model konzistence určeného můžete (buď na úrovni požadavku nebo databázového účtu). Požadavek čtení je považován za splnili konzistence SLA, pokud jsou splněny všechny konzistence záruky související s úrovní konzistence. V následující tabulce zaznamená konzistence záruky, které odpovídají určité konzistence modely, které nabízí Azure Cosmos DB.

<table>
    <tr>
        <td><strong>Konzistence modelu</strong></td>
        <td><strong>Vlastnosti konzistence</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
        <tr>
        <td>Silné</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Typu s ohraničenou prošlostí</td>
        <td>Monotónní čtení (v rámci oblasti)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konzistentní předpony</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Vázaný typu prošlostí &lt; tisíc, T</td>
        <td>100%</td>
    </tr>
<tr>
        <td rowspan="3">Relace</td>
        <td>Přečtěte si vlastní zápisu</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Monotónní pro čtení</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konzistentní předpony</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konzistentní předpony</td>
        <td>Konzistentní předpony</td>
        <td>100%</td>
    </tr>
</table>

**Konzistence typu zajišťuje přidružený k dané konzistence modelu v Azure Cosmos DB**


### <a id="ConsistencyAndAvailability"></a>Relace je konzistence s dostupností
[Nemožností výsledek](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) z [věta CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prokáže, že je skutečně znemožňuje, aby systému zůstanou dostupné, a nabízet linearizable konzistence při krátkodobém selhání. Služba databáze musíte zvolit prohlášení CP nebo Asie, kde CP systémy forgo dostupnosti považuje linearizable konzistence při systémy Asie forgo [linearizable konzistence](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) považuje dostupnosti. Azure Cosmos DB nikdy narušuje požadovaný konzistence modelu, který je oficiálně umožňuje CP systému. Nicméně v praxi konzistence není všechny nebo nic nabídky; nejsou k dispozici více modely dobře definovaný konzistence podél spektra konzistence mezi linearizable a případnou konzistence. V Azure Cosmos DB identifikuje několik volný konzistence modely, které se vztahují k skutečných scénářích a jsou intuitivní používat. Azure Cosmos DB přejde kompromisy konzistence dostupnosti prostřednictvím nabídky [více zmírnit ještě dobře definovaný konzistence modely](consistency-levels.md) a 99,99 % dostupnost pro všechny jednotné oblasti databáze účtů a 99.999 % čtení a zápis dostupnost pro všechny účty databáze více oblast. 

### <a id="ConsistencyAndAvailability"></a>Relace je konzistence s latencí
Je volána komplexnější varianta věta CAP [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), který také účty pro latenci a konzistence kompromisy v stabilního stavu. Uvádí, že v stabilního stavu musí databázový systém zvolte mezi konzistencí a latenci. S více modely volný konzistence (zálohován asynchronní replikaci a místní čtení a zápisu kvor) Azure Cosmos DB zajišťuje, že všechny čtení a zápisu jsou místní vzhledem k čtení a zápisu oblasti v uvedeném pořadí. To umožňuje Cosmos databáze Azure nabízí nízkou latencí zaručuje v rámci oblasti pro danou konzistence modely.  

### <a id="ConsistencyAndThroughput"></a>Relace je konzistence s propustností
Vzhledem k tomu, že implementace model konkrétní konzistence závisí na výběr [kvora typ](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), propustnost také se liší podle volba konzistence modelu. Například v Azure Cosmos DB, RU zdarma pro důrazně konzistentní čtení je přibližně *dvojité* , nakonec byl konzistentní čtení. V takovém případě bude muset zřídit double RUs k dosažení stejné propustnosti.


![Vztah mezi konzistencí a propustnosti](./media/distribute-data-globally/consistency-and-throughput.png)

**Vztah čtení kapacity pro konkrétní konzistence model v Azure Cosmos DB**

## <a id="ThroughputGuarantees"></a>Propustnost záruky 
Azure Cosmos DB umožňuje škálování propustnost (stejně jako, úložiště), Elasticky napříč libovolný počet oblastí v závislosti na vaší potřebám nebo vyžádání. 

![Azure Cosmos DB distribuované a kolekce rozdělena na oddíly](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Jediný kontejner Azure Cosmos DB vodorovně rozděleného (mezi tři oddíly prostředků v rámci oblasti) a pak globálně distribuován do tří oblastí Azure**

Získá kontejner Azure Cosmos DB distribuovány v dvěma rozměry (i) v oblasti a (ii) v oblastech. Zde je uveden postup: 

* **Místní distribuční**: V jedné oblasti, kontejner Azure Cosmos DB je horizontálně škálovat na více systémů z hlediska *prostředků oddíly*. Každý oddíl prostředků spravuje sady klíčů a důrazně konzistentní a vysokou dostupností se fyzicky reprezentována čtyři repliky také volat *sady replik* a stav počítače replikace mezi tyto repliky. Azure Cosmos DB je systém plně prostředek řídí, kde oddílu prostředků zodpovídá k poskytování svůj díl propustnost pro rozpočtu přidělených prostředků systému. Škálování kontejner Azure Cosmos DB je pro uživatele transparentní. Azure Cosmos DB spravuje oddíly prostředků a rozdělí a sloučí je podle potřeby jako úložiště a budou měnit požadavky na propustnost. 
* **Globální distribuční**: Pokud je databáze určené pro více oblastí, každou nadefinovaných oddílů prostředků je poté distribuován v těchto oblastech. Oddíly prostředků vlastnící stejnou sadu klíčů mezi různé oblasti formuláře *oddílu sadu* (najdete v části [předchozí obrázek](#ThroughputGuarantees)).  Oddíly prostředků v rámci sady oddílu jsou koordinované pomocí replikaci stavu počítače nad několika oblastmi přidružený k databázi. V závislosti na úroveň konzistence nakonfigurované se konfiguruje oddíly prostředků v rámci sady oddílu dynamicky pomocí topologiemi (například hvězdičky, sériově, stromu atd.). 

Na základě správy centry oddílu, Vyrovnávání zatížení a zásady správného řízení striktní prostředků Azure Cosmos DB umožňuje pružně škálování propustnosti nad několika oblastmi Azure přidružené databázi Azure Cosmos kontejner nebo databáze. Změna zřízená propustnost je operace runtime v Azure Cosmos DB. Podobně jako u jiných databázových operací, Azure Cosmos DB zaručuje absolutní horní mez na latenci pro vaši žádost o změnu zřízené propustnosti. Například následující obrázek znázorňuje pružně zajištěnou propustností (rámci dvou oblastí rozsahu od 1 milion - 10M počet požadavků za sekundu) v závislosti na kontejneru zákazníka.

![Azure Cosmos DB pružně zřízené propustnosti](./media/distribute-data-globally/elastic-throughput.png)

**Kontejner zákazníka pružně zajištěnou propustností (různých z 1 milion - 10M počet požadavků za sekundu)**

### <a id="ThroughputAndConsistency"></a>Propustnost je vztah s konzistence 
Je stejný jako v [konzistence na vztah s propustností](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Propustnost je vztah s dostupností
Azure Cosmos DB i nadále spravovat jeho vysokou dostupnost, provedení změn zřízené propustnosti. Azure Cosmos DB transparentně spravuje oddíly prostředků (a provede rozdělení, sloučení a klonování operations) a zajistí, že operace není snížit výkon nebo dostupnost, zatímco pružně zvyšuje nebo snižuje propustnost aplikace. 

## <a id="AvailabilityGuarantees"></a>Záruky dostupnosti
Azure Cosmos DB nabízí 99,99 % dostupnost SLA pro všechny databáze jedné oblasti a všechny oblasti s více účty s volný konzistence a 99.999 % dostupnosti pro všechny účty databáze více oblast. Jak je popsáno výše, zahrnují záruky dostupnosti Azure Cosmos DB absolutní horní mez pro latenci pro každé operace roviny dat a řízení. Záruky dostupnosti s počtem oblastí nebo zeměpisné vzdálenost mezi oblastmi neměňte. Záruky dostupnosti se použít s ohledem na ruční jak automatické převzetí služeb při selhání. Azure Cosmos DB nabízí transparentní více funkci rozhraní API, která zajistěte, aby vaše aplikace umožňuje práci s logické koncových bodů a může transparentně směrovat požadavky do nové oblasti v případě selhání. Aplikace nemusí být znovu nasazena v případě, že místní převzetí služeb při selhání a dostupnost SLA zachovány za všech okolností.

### <a id="AvailabilityAndConsistency"></a>Relace na dostupnosti s konzistence, latence a propustnosti
Relace na dostupnosti s konzistence, latence a propustnosti je popsané v částech [konzistence na vztah s dostupností](#ConsistencyAndAvailability), [na latenci vztah s dostupností](#LatencyAndAvailability) a [Propustnosti na vztah s dostupností](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Metriky SLA zákazníkem
Azure Cosmos DB transparentně zpřístupní metriky propustnosti, latenci, konzistence a dostupnosti. Tyto metriky jsou přístupné prostřednictvím kódu programu a prostřednictvím portálu Azure (viz následující obrázek). Můžete také nastavit výstrahy na různé prahové hodnoty pomocí služby Azure Application Insights.
 

![Azure Cosmos DB zákazníka viditelné metrikách SLA](./media/distribute-data-globally/customer-slas.png)

**Metriky konzistence, latenci, propustnost a dostupnost se transparentně k dispozici pro každého klienta**

## <a id="Next Steps"></a>Další kroky
* K implementaci globální replikace na vašem účtu Azure Cosmos DB pomocí portálu Azure, najdete v části [postup replikace globální databáze Azure Cosmos DB pomocí webu Azure portal](tutorial-global-distribution-sql-api.md).
* Další informace o tom, jak implementovat více hlavních architektury s Azure Cosmos DB najdete v tématu [architektury více hlavní databázi s Azure Cosmos DB](multi-region-writers.md).
* Další informace o tom, jak automatickou a ruční převzetí služeb při selhání fungovat v Azure Cosmos DB najdete v tématu [regionální převzetí služeb při selhání v Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Odkazy
1. Erica Brewer. [Směrem robustní distribuovaných systémů](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Erica Brewer. [Zakončení později – 12 letech jak změnily pravidla](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Brewer&#39;s domněnek a vhodnosti konzistentní, k dispozici, Oddíl odolný vůči chybám webových služeb](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. ADAM Abadi. [Konzistence kompromisy v moderních distribuovaných systémů návrhu databáze](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Martin Kleppmann. [Zastavte volání databáze prohlášení CP nebo Asie a Tichomoří](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Petr Bailis a další. [Pravděpodobnosti typu s ohraničenou Prošlostí (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor a vlny. [Zatížení, kapacity a dostupnosti v systémech kvora](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy a výskytů. [Lineralizability: Správnost podmínku pro souběžné objekty](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
