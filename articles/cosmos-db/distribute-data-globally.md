---
title: Distribuci dat globálně pomocí Azure Cosmos DB | Microsoft Docs
description: Další informace o škálování planetu geografická replikace, převzetí služeb při selhání a data obnovení pomocí globální databáze z databáze Cosmos Azure, služby globálně distribuované, podstoupí model databáze.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2018
ms.author: arramac
ms.openlocfilehash: 588eb4df9555f20fe4193e8ae34ad51c17921db2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Jak se bude distribuovat dat globálně pomocí Azure Cosmos DB
Azure je všudypřítomný – má globální nároků přes 30 + zeměpisné oblasti a průběžně zvětšuje. S jeho po celém světě přítomnosti jeden odlišné možnosti, které Azure nabízí pro jeho vývojáře je schopnost vytvářet, nasazovat a spravovat snadno globálně distribuované aplikace. 

[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje připraveného globální distribuční [elastické škálování propustnost a úložiště](../cosmos-db/partition-data.md) po celém světě, jednociferné milisekundu latence v 99th percentilu [pět dobře definované úrovně konzistence](consistency-levels.md)a zaručit vysoká dostupnost, všechny zálohovány pomocí [špičkový SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), aniž by vyžadovala zapojení správy schémat a indexů. Zahrnuje více modelů a podporuje modely dokumentů, klíčových hodnot, grafů a sloupcových dat. Jako služba narodila cloudu Azure Cosmos DB je pečlivě zkonstruován víceklientský a globální distribuci od základů nahoru.

**Jedinou kolekci Azure Cosmos DB rozdělena na oddíly a distribuovaných nad několika oblastmi Azure**

![Azure Cosmos DB kolekce rozdělena na oddíly a distribuován do tří oblastí](./media/distribute-data-globally/global-apps.png)

Jak jsme se naučili při sestavování Azure Cosmos DB, přidání globální distribuční nelze chodím – nemůže být "přišroubovány on" na "jedné lokality" databázový systém. Funkce nabízené globálně distribuované databáze nad rámec span, že tradiční zeměpisné katastrofě obnovení (Geo-DR) nabízí "jedné lokalitě" databází. Databáze jedné lokalitě nabídky Geo-zotavení po Havárii schopnosti jsou striktní podmnožinu globálně distribuované databáze. 

S připraveného globální distribuční databázi Cosmos Azure, nemají vývojářům vytvářet své vlastní generování uživatelského rozhraní replikace díky využití architektury buď vzoru Lambda (například [AWS DynamoDB replikace](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) přes protokol databáze nebo to " Double zapíše"nad několika oblastmi. Vzhledem k tomu, že není možné zajistit správnost takové přístupů a zadejte zvukové SLA nedoporučujeme těchto přístupů. 

V tomto článku poskytujeme přehled možností globální distribuční databázi Cosmos Azure. Můžeme také popisují Azure Cosmos DB jedinečný přístup k poskytování komplexní SLA. 

## <a id="EnableGlobalDistribution"></a>Povolení připraveného globální distribuční
Azure Cosmos DB poskytuje následující funkce, které vám umožní snadno psaní planetu škálování aplikací. Tyto možnosti jsou dostupné prostřednictvím založenou na poskytovateli prostředků Azure Cosmos DB [rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) a také na portálu Azure.

V následujícím videu demonstruje Azure manažer programu DB Cosmos Andrew Liu připraveného globální distribuční funkce.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Všudypřítomná regionální přítomnosti 
Azure je neustále rostoucí jeho zeměpisné přítomnosti tak, že převedou [nové oblasti](https://azure.microsoft.com/regions/) online. Azure Cosmos DB je k dispozici ve všech oblastech nové Azure ve výchozím nastavení. To umožňuje přidružit geografické oblasti databázový účet Azure Cosmos DB co nejrychleji Azure otevře novou oblast pro firmy.

### <a id="UnlimitedRegionsPerAccount"></a>Přidružení neomezený počet oblasti s vaším účtem Azure Cosmos DB databáze
Azure Cosmos DB umožňuje přidružit libovolný počet oblastí Azure databázový účet Azure Cosmos DB. Mimo omezení geografického vymezení (například Čína, Německo) neexistují žádná omezení počtu oblastí, které může být spojeno s vaším účtem databáze Azure Cosmos DB. Následující obrázek znázorňuje databázový účet nakonfigurován tak, aby přes 25 oblastech Azure.  

**Klienta Azure Cosmos DB databáze účet STA 25 oblastí Azure**

![Účet databáze Azure Cosmos DB pokrývání uzlů 25 oblastí Azure](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Na základě zásad geografického vymezení
Azure Cosmos DB je určena pro možnosti geografického vymezení na základě zásad. Geografického vymezení je důležitou součástí zajistit vedení a dodržování předpisů omezení dat a může zabránit přidružení v určité oblasti s vaším účtem. Příklady geografického vymezení zahrnují (ale nejsou omezeni), oborů globální distribuce do oblasti v rámci svrchovaných cloudu (například Čína a Německo), nebo hranici zdanění government (například Austrálie). Zásady jsou řízena pomocí metadat vašeho předplatného Azure.

### <a id="DynamicallyAddRegions"></a>Dynamicky přidávat a odebírat oblastí
Azure Cosmos DB umožňuje přidat (přidružení) nebo odebrat (zrušit přidružení) oblasti ke svému účtu databáze v libovolném bodě v čase (viz [předchozí obrázek](#UnlimitedRegionsPerAccount)). Na základě replikaci dat mezi oddílů souběžně, Azure Cosmos DB zajišťuje, že při přechodu do režimu online novou oblast, Azure Cosmos DB k dispozici do 30 minut odkudkoli na světě pro až 100 TBs. 

### <a id="FailoverPriorities"></a>Priorit převzetí služeb při selhání
K řízení přesné pořadí regionální převzetí služeb při selhání po výpadku více místní databázi Cosmos Azure umožňuje k přidružení prioritu do různých oblastech přidružený k databázi účtu (viz následující obrázek). Azure Cosmos DB zajistí, že pořadí automatické převzetí služeb při selhání dojde v pořadí podle priority, kterou jste zadali. Další informace o místní převzetí služeb při selhání najdete v tématu [automatické regionální převzetí služeb při selhání pro kontinuitu podnikových procesů v Azure Cosmos DB](regional-failover.md).

**Klient Azure Cosmos databáze můžete nakonfigurovat pořadí priorit převzetí služeb při selhání (pravé podokno) pro oblasti, které jsou spojené s účtem databáze**

![Konfigurace priorit převzetí služeb při selhání v Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Více, dobře definovaný konzistence modely pro globální replikované databáze
Zpřístupní Azure Cosmos DB [více dobře definované úrovně konzistence](consistency-levels.md) zajišťoval SLA. Můžete vybrat konkrétní konzistence modelu (ze seznamu dostupných možností) v závislosti na zatížení/scénáře. 

### <a id="TunableConsistency"></a>Přizpůsobitelné konzistence pro globální replikované databáze
Azure Cosmos DB umožňuje prostřednictvím kódu programu přepsat a uvolnit výchozí konzistence volba na základě žádosti za běhu. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamicky konfigurovat pro čtení a zápisu oblastí
Azure Cosmos DB umožňuje nakonfigurovat oblastí (přidružený k databázi) pro "číst", "zápisu" nebo "pro čtení a zápis" oblasti. 

### <a id="ElasticallyScaleThroughput"></a>Elasticky škálování propustnost mezi oblastmi Azure
Je možné Elasticky škálovat kolekci Azure Cosmos DB podle zřizování propustnost prostřednictvím kódu programu. Propustnost se použije pro všechny oblasti, kterou je kolekce distribuován.

### <a id="GeoLocalReadsAndWrites"></a>Geograficky místní čte a zapisuje
Hlavní výhoda globálně distribuované databáze je na nabídku s nízkou latencí přístup k datům kdekoli v celém světě. Azure Cosmos DB nabízí nízkou latencí záruky na P99 pro různé operace databáze. Zajišťuje, že všechny operace čtení jsou směrované na nejbližší místní oblast pro čtení. K obsluze požadavek čtení, se používá místní oblast, ve kterém se objeví čtení kvora; Totéž platí i pro zápisů. Až po většinu repliky spolehlivě potvrdil zápis místně, ale bez se ověřované vrácení na vzdálené repliky potvrdit zápisů, potvrdí se zápis. Jinak PUT, za předpokladu, že kvor ke čtení a zápisu jsou vždy místní pro čtení a zápis oblasti, v uvedeném pořadí, ve kterém je vydán požadavek funguje protokol replikace databáze Azure Cosmos.

### <a id="ManualFailover"></a>Ruční zahájení regionální převzetí služeb při selhání
Azure Cosmos DB umožňuje aktivovat převzetí služeb při selhání databázový účet ověřit *koncová* dostupnosti vlastnosti bude celá aplikace (mimo databázi). Vzhledem k tomu, že jsou zaručena bezpečnost a liveness vlastnosti selhání zjišťování a vedoucí volba, Azure Cosmos DB zaručuje *nulové ztráty dat* operace klienta iniciované ruční převzetí služeb při selhání.

### <a id="AutomaticFailover"></a>Automatické převzetí služeb při selhání
Azure Cosmos DB podporuje automatické převzetí služeb při selhání v případě jeden nebo více regionální výpadků. Při selhání regionální Azure Cosmos DB udržuje latenci pro čtení, dostupnosti provozu, konzistence a propustnost SLA. Azure Cosmos DB poskytuje horní mez doba na dokončení operace automatické převzetí služeb při selhání. Toto je okno potenciální ztrátě dat během výpadku místní.

### <a id="GranularFailover"></a>Určená pro členitostí v různých převzetí služeb při selhání
Možnosti automatického nebo ručního převzetí služeb při selhání jsou aktuálně umístěné na členitost databázového účtu. Poznámka: interně Azure DB Cosmos je určená k poskytování *automatické* převzetí služeb při selhání na podrobnější databáze, kolekce nebo dokonce oddílu (kolekce vlastnící rozsah klíčů). 

### <a id="MultiHomingAPIs"></a>Více funkci rozhraní API v Azure Cosmos DB
Azure Cosmos DB umožňuje pracovat s databází pomocí logických (bez ohledu na oblast) nebo fyzické koncových bodů (specifické pro oblast). Použití logické koncové body zajišťuje, že aplikace může transparentně byly vícedomé v případě převzetí služeb při selhání. Druhé fyzické koncových bodů, zadejte podrobnější řízení přístupu k aplikaci pro přesměrování čte a zapisuje do určitých oblastí.

Můžete najít informace o tom, jak nakonfigurovat čtení předvoleb pro [rozhraní SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [rozhraní Graph API](../cosmos-db/tutorial-global-distribution-graph.md), [tabulky API](../cosmos-db/tutorial-global-distribution-table.md), a [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) v jejich příslušné propojené články.

### <a id="TransparentSchemaMigration"></a>Migrace databáze transparentní a konzistentní schéma a index 
Azure Cosmos DB je plně [bez ohledu na schéma](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Jedinečný návrhu databázového stroje to umožňuje automaticky a synchronně indexu všechna data, která ho ingestuje bez nutnosti žádné schéma nebo sekundární indexy, které od vás. To umožňuje rychle iterovat globálně distribuované aplikace bez starostí o migraci databáze schéma a index nebo koordinace aplikace s více fáze zavedení změn schématu uživatelům. Azure Cosmos DB zaručuje, že všechny změny indexování zásady explicitně které jste udělali nepovedou do snížení výkonu výkon nebo dostupnost.  

### <a id="ComprehensiveSLAs"></a>Komplexní SLA (kromě stejně vysokou dostupnost)
Jako služba globálně distribuovanou databázi, databázi Cosmos Azure nabízí dobře definovaný SLA pro **ztráty dat**, **dostupnosti**, **latence v P99**, **propustnost**  a **konzistence** pro databázi jako celek, bez ohledu na počet oblastí přidružený k databázi.  

## <a id="LatencyGuarantees"></a>Latence záruky
Hlavní výhoda globálně distribuované databáze služby jako databázi Cosmos Azure je na nabídku s nízkou latencí přístup k datům kdekoli v celém světě. Azure Cosmos DB nabízí zaručenou nízkou latencí v P99 pro různé operace databáze. Protokol replikace, který využívá Azure Cosmos DB zajistí, že databázové operace (v ideálním případě jak čte a zapisuje) jsou vždycky probíhá v oblasti místní pro tohoto klienta. Latence smlouvy SLA systému Azure Cosmos DB zahrnuje P99 pro čtení, zápisu (synchronně) indexované a dotazy pro různé velikosti požadavku a odpovědi. Záruky latence pro zápis zahrnují potvrzení trvanlivý většinu kvora v místním datacentru.

### <a id="LatencyAndConsistency"></a>Čekací doba na relaci s konzistence 
Globálně distribuované služby nabízí silnou konzistenci v globálně distribuované instalační program, musí se replikovat synchronně zápisů nebo synchronní provádět mezi oblastmi čtení – rychlosti světla a spolehlivost sítě WAN stanovují, že silné konzistence má za následek vyšší latence a databázové operace je omezená dostupnost. Proto aby nabízejí zaručit volný konzistence, nízkou latenci v P99 a 99,99 % dostupnost pro všechny účty jedné oblasti a všechny oblasti s více účty a 99.999 % číst dostupnosti pro všechny účty databáze více oblast, musí využívat službu asynchronní replikaci. Tato naopak vyžaduje službu musí také nabízí [dobře definovaný, volný konzistence choice(s)](consistency-levels.md) – slabší než silné (a nabídnout nízkou latenci a dostupnosti záruky) a v ideálním případě silnější než "případné" konzistence (do nabízí intuitivní programovací model).

Azure Cosmos DB zajistí, že operace čtení není potřeba kontaktovat nad několika oblastmi k poskytování úrovně záruku konkrétní konzistence repliky. Podobně zajišťuje, že operace zápisu nejsou zablokování při data se replikuje přes všechny oblasti (tj. zápisy se asynchronně replikují přes oblasti). Pro účty databáze více oblasti jsou k dispozici více úrovních volný konzistence. 

### <a id="LatencyAndAvailability"></a>Čekací doba na relaci s dostupností 
Latence a dostupnost se sociálními stejné mince. V souvislosti s latencí operací v stabilního stavu a dostupnosti při krátkodobém selhání. Z hlediska aplikace je pomalá běžící operace databáze lišit od databázi, která je k dispozici. 

K rozlišení vysokou latencí z nejsou dostupné, poskytuje Azure Cosmos DB absolutní horní mez na latenci různé operace databáze. Pokud operace databáze trvá déle, než horní hranice k dokončení, vrátí Azure Cosmos DB vypršení časového limitu. SLA dostupnosti Azure Cosmos DB zajistí, že časové limity počítají proti smlouva SLA o dostupnosti. 

### <a id="LatencyAndThroughput"></a>Čekací doba na relaci s propustností
Azure Cosmos DB neprovede můžete zvolit latence a propustnosti. Ho ctí SLA pro obě latence v P99 a poskytnout, když máte zřízenou propustnost. 

## <a id="ConsistencyGuarantees"></a>Záruky konzistence
Když [silnou konzistenci modelu](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) je standard zlatý z programovatelnosti, kompromisnímu zvládnutí cenu vyšší latence (v stabilního stavu) a nižší dostupnost (při krátkodobém počet selhání). 

Azure Cosmos DB nabízí dobře definovaný programovací model pro vás důvod o konzistence replikovaná data. Pokud chcete povolit, můžete k vytváření aplikací s více adresami, modely konzistence vystavené Azure Cosmos DB slouží oblast na úlohách a není závislá na oblasti, ze které se zpracovávají čtení a zápisu. 

Azure Cosmos DB konzistence SLA zaručuje, že 100 % požadavků na čtení bude vyhovovat záruku konzistence pro úroveň konzistence požadoval buď (výchozí úroveň konzistence na databázového účtu) nebo přepsané hodnota v žádosti. Požadavek čtení je považován za splnili SLA konzistence, pokud jsou splněny všechny konzistence záruky související s úrovní konzistence. V následující tabulce zaznamená konzistence záruky, které odpovídají úrovním konkrétní konzistence, které nabízí Azure Cosmos DB.

**Konzistence záruky související s úrovní konzistence pro danou v Azure Cosmos DB**

<table>
    <tr>
        <td><strong>Úroveň konzistence</strong></td>
        <td><strong>Vlastnosti konzistence</strong></td>
        <td><strong>SLA</strong></td>
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
        <td>Konzistentní předpony</td>
        <td>Konzistentní předpony</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Silné</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Relace je konzistence s dostupností
[Nemožností výsledek](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) z [věta CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prokáže, že je skutečně znemožňuje, aby systém zůstanou dostupné, a nabízet linearizable konzistence při krátkodobém selhání. Služba databáze musíte zvolit prohlášení CP nebo Asie a Tichomoří – CP systémy forgo dostupnosti považuje linearizable konzistence při systémy Asie forgo [linearizable konzistence](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) považuje dostupnosti. Azure Cosmos DB nikdy porušuje úroveň požadovaný konzistence, takže oficiálně je CP systému. Ale v praxi není konzistence všech nebo nic nabídky – jsou více modely dobře definovaný konzistence podél spektra konzistence mezi linearizable a případnou konzistence. V Azure DB Cosmos Pokusili jsme se identifikovat několik modelů volný konzistence s skutečných použitelnosti a intuitivní programovací model. Azure Cosmos DB přejde kompromisy konzistence dostupnosti prostřednictvím nabídky [více zmírnit ještě dobře definované úrovně konzistence](consistency-levels.md) a 99,99 % dostupnost pro všechny účty jedné oblasti a všechny oblasti s více účty s zmírnit konzistence a 99.999 % číst dostupnosti pro všechny účty databáze více oblast. 

### <a id="ConsistencyAndAvailability"></a>Relace je konzistence s latencí
Komplexnější varianta CAP byl navržený Prof. ADAM Abadi a se nazývá [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), který také účty pro latenci a konzistence kompromisy v stabilního stavu. Uvádí, že v stabilního stavu, musí databáze systému zvolte mezi konzistencí a latenci. S více modely volný konzistence (zálohován asynchronní replikaci a místní pro čtení, zápisu kvor) Azure Cosmos DB zajišťuje, že všechny čtení a zápisu jsou místní vzhledem k čtení a zápisu oblasti v uvedeném pořadí.  To umožňuje Cosmos databáze Azure nabízí nízkou latencí zaručuje v rámci oblasti pro úrovně konzistence.  

### <a id="ConsistencyAndThroughput"></a>Relace je konzistence s propustností
Vzhledem k tomu, že implementace model konkrétní konzistence závisí na výběr [kvora typ](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), propustnost také se liší podle volba konzistence. Například v Azure Cosmos DB, je zdarma RU pro důrazně konzistentní čtení zhruba double, že z nakonec byl konzistentní čte. V takovém případě bude muset zřídit double RUs na kolekce k dosažení stejné propustnosti.
 
**Vztah čtení kapacity pro konkrétní konzistence úrovně v Azure Cosmos DB**

![Vztah mezi konzistencí a propustnosti](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Propustnost záruky 
Azure Cosmos DB umožňuje škálování propustnost (stejně jako, úložiště), Elasticky v různých oblastech v závislosti na vyžádání. 

**Jedinou kolekci Azure Cosmos DB rozděleného (mezi tři horizontálních oddílů) a poté distribuován do tří oblastí Azure**

![Azure Cosmos DB distribuované a kolekce rozdělena na oddíly](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Kolekci Azure Cosmos DB získá distribuované pomocí dvěma rozměry – v rámci oblasti a pak v oblastech. Zde je uveden postup: 

* V jedné oblasti kolekci Azure Cosmos DB škálovat na více systémů z hlediska prostředků oddíly. Každý oddíl prostředků spravuje sady klíčů a je důrazně konzistentní a vysokou dostupností na základě stavu počítače replikace mezi sadu replik. Azure Cosmos DB je prostředek řídí systém plně, kde oddílu prostředků zodpovídá za poskytování svůj díl propustnost pro rozpočtu přidělených prostředků systému. Škálování kolekci Azure Cosmos DB je zcela transparentní – Azure Cosmos DB spravuje oddíly prostředků a rozdělí a sloučí se podle potřeby. 
* Všechny oddíly prostředků je poté distribuován nad několika oblastmi. Oddíly prostředků vlastnící stejnou sadu klíče v různých oblastech tvoří sada oddílu (viz [předchozí obrázek](#ThroughputGuarantees)).  Oddíly prostředků v rámci sady oddílu jsou koordinované pomocí replikaci stavu počítače v několika oblastech. V závislosti na úroveň konzistence nakonfigurované se konfiguruje oddíly prostředků v rámci sady oddílu dynamicky pomocí topologiemi (například hvězdičky, sériově, stromu atd.). 

Na základě správy centry oddílu, Vyrovnávání zatížení a zásady správného řízení striktní prostředků Azure Cosmos DB umožňuje pružně škálování propustnosti nad několika oblastmi Azure v kolekci Azure Cosmos DB. Změna propustnosti na kolekci je operace runtime v Azure Cosmos DB - jako s dalšími operacemi databáze Azure Cosmos DB zaručuje absolutní horní mez na latenci pro vaši žádost o změnu propustnost. Jako příklad následující obrázek znázorňuje zákazníka kolekce pružně zajištěnou propustností (rámci dvou oblastí rozsahu od 1 milion - 10M počet požadavků za sekundu) v závislosti na.
 
**Kolekce zákazníka pružně zajištěnou propustností (1 milion - 10M požadavků za sekundu)**

![Azure Cosmos DB pružně zřízené propustnosti](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Propustnost je vztah s konzistence 
Stejné jako [konzistence na vztah s propustností](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Propustnost je vztah s dostupností
Azure Cosmos DB i nadále udržovat jeho dostupnost při provedení změn propustnosti. Azure Cosmos DB transparentně spravuje oddíly (například rozdělení, sloučení, operace klonování) a zajistí, že operace není snížit výkon nebo dostupnost, zatímco pružně zvyšuje nebo snižuje propustnost aplikace. 

## <a id="AvailabilityGuarantees"></a>Záruky dostupnosti
Azure Cosmos DB nabízí 99,99 % dostupnost SLA pro všechny účty jedné oblasti a všechny oblasti s více účty s volný konzistence a 99.999 % číst dostupnosti pro všechny účty databáze více oblast. Jak je popsáno výše, zahrnují záruky dostupnosti Azure Cosmos DB absolutní horní mez na latenci pro každé operace roviny dat a řízení. Záruky dostupnosti jsou steadfast a nemění se číslo oblasti nebo zeměpisné vzdálenost mezi oblastmi. Záruky dostupnosti použít s ruční jak, automatické převzetí služeb při selhání. Azure Cosmos DB nabízí transparentní více funkci rozhraní API, která zajistěte, aby vaše aplikace umožňuje práci s logické koncových bodů a může transparentně směrovat požadavky pro novou oblast v případě převzetí služeb při selhání. Jinak PUT, vaše aplikace není nutné znovu nasadit na místní převzetí služeb při selhání a jsou zachována dostupnost SLA.

### <a id="AvailabilityAndConsistency"></a>Relace na dostupnosti s konzistence, latence a propustnosti
Relace na dostupnosti s konzistence, latence a propustnosti je popsaná v [konzistence na vztah s dostupností](#ConsistencyAndAvailability), [na latenci vztah s dostupností](#LatencyAndAvailability) a [Propustnosti na vztah s dostupností](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Záruky a chování systému pro "ztrátě dat."
V Azure DB Cosmos každý oddíl (kolekce) je vysoké dostupnosti počtem replik, které jsou rozloženy domén selhání alespoň 10-20. Všech zápisů jsou synchronně a spolehlivě potvrdí podle většinu kvora replik předtím, než se potvrdí do klienta. Asynchronní replikaci se použije s spolupráce mezi oddílů rozloženy více oblastí. Azure Cosmos DB zaručuje, že nedochází ke ztrátě dat spustil klienta ruční převzetí služeb při selhání. Při automatické převzetí služeb při selhání zaručuje Azure Cosmos DB horní mez intervalu nakonfigurované typu s ohraničenou prošlostí na okno ztráty dat v rámci smlouvy SLA pro jeho.

## <a id="CustomerFacingSLAMetrics"></a>Metriky SLA zákazníkem
Azure Cosmos DB transparentně zpřístupní metriky propustnosti, latenci, konzistence a dostupnost. Tyto metriky jsou přístupné prostřednictvím kódu programu a prostřednictvím portálu Azure (viz následující obrázek). Můžete také nastavit výstrahy na různé prahové hodnoty pomocí služby Azure Application Insights.
 
**Metriky konzistence, latenci, propustnost a dostupnost se transparentně k dispozici pro každého klienta**

![Azure Cosmos DB zákazníka viditelné metrikách SLA](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Další kroky
* K implementaci globální replikace na vašem účtu Azure Cosmos DB pomocí portálu Azure, najdete v části [postup replikace globální databáze Azure Cosmos DB pomocí webu Azure portal](tutorial-global-distribution-sql-api.md).
* Další informace o tom, jak implementovat více hlavních architektury s Azure Cosmos DB najdete v tématu [architektury více hlavní databázi s Azure Cosmos DB](multi-region-writers.md).
* Další informace o tom, jak automatickou a ruční převzetí služeb při selhání fungovat v Azure Cosmos DB najdete v tématu [regionální převzetí služeb při selhání v Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Odkazy
1. Erica Brewer. [Směrem robustní distribuovaných systémů](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Erica Brewer. [Zakončení později – 12 letech jak změnily pravidla](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbert, Lynch. - [Brewer&#39;s domněnek a vhodnosti konzistentní, k dispozici, Oddíl odolný vůči chybám webových služeb](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [Konzistence kompromisy v moderních distribuovaných systémů návrhu databáze](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Martin Kleppmann. [Zastavte volání databáze prohlášení CP nebo Asie a Tichomoří](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Petr Bailis a další. [Pravděpodobnosti typu s ohraničenou Prošlostí (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor a vlny. [Zatížení, kapacity a dostupnosti v systémech kvora](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy a výskytů. [Lineralizability: Správnost podmínku pro souběžné objekty](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
