---
title: Globální distribuce dat pomocí služby Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o globálním měřítku geografickou replikaci, převzetí služeb při selhání a data obnovení pomocí globálních databází ze služby Azure Cosmos DB, globálně distribuovanou databázovou vícenásobného modelu služby.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: dec981ad750a49646916dbef40a4cc632ab71da2
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856636"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Globální distribuce dat pomocí služby Azure Cosmos DB
Azure je všudypřítomná – to globální stopu napříč více než 50 geografických oblastí a pokus se neustále rozšiřuje. Globální přítomnost služeb jeden diferencované možnosti, které Azure nabízí svým vývojářům se schopnost vytvářet, nasazovat a spravovat snadno globálně distribuované aplikace. 

[Azure Cosmos DB](../cosmos-db/introduction.md) je globálně distribuovaná databázová služba Microsoftu s více modely pro klíčové aplikace. Azure Cosmos DB poskytuje globální distribuce na klíč, [elastické škálování propustnosti a úložiště](../cosmos-db/partition-data.md) po celém světě, řádu milisekund na 99. percentilu, [pět jasně definované modely konzistence](consistency-levels.md)a zaručenou vysokou dostupnost, vše zajištěné [špičkové komplexní smlouvy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [automaticky indexuje všechna data](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) aniž byste se museli starat o správu schématu nebo indexů. Je služba pro více modelů a podporuje dokumentu, klíč hodnota, graf a řada sloupců datových modelů. Jako nativně narození v cloudové službě je Azure Cosmos DB navržena s využitím více tenantů a globální distribuci od základů až pečlivě.


![Kontejner Azure Cosmos DB rozdělit na oddíly a distribuovat ve třech oblastech](./media/distribute-data-globally/global-apps.png)

**Jeden kontejner Azure Cosmos DB rozdělit na oddíly a distribuované napříč několika oblastmi Azure**

Protože jsme zjistili při vytváření služby Azure Cosmos DB, přidávání globální distribuce nemůže být opomíjet. Nemůže být "přišroubovány na" imitovaná databázový systém "jedna lokalita". Funkce nabízené globálně distribuovanou databázi span nad rámec, tradiční zeměpisné katastrofě obnovení (Geo-DR) nabízených databází "jednou lokalitou". Nabízí možnost Geo-DR databáze jedné lokality jsou striktní podmnožinou globálně distribuované databáze. 

S globální distribuce služby Azure Cosmos DB na klíč, vývojáři není nutné vytvářet své vlastní generování uživatelského rozhraní replikace buď když vzor Lambda (například [AWS DynamoDB replikace](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) přes protokol databáze nebo podle provádění "double zápisy" napříč několika oblastmi. Děláme *není* doporučujeme tyto přístupy, protože jde o neuskutečnitelnou zajistili její správnost tyto přístupy a poskytovat zvukové smlouvy o úrovni služeb. 

Tento článek poskytuje přehled o možnosti globální distribuce služby Azure Cosmos DB. Také popisujeme jedinečný přístup služby Azure Cosmos DB k poskytování komplexní smlouvy SLA. 

## <a id="EnableGlobalDistribution"></a>Povolením globální distribuce na klíč
Azure Cosmos DB poskytuje následující funkce, které vám umožní snadno vytvářet globálně distribuované aplikace. Tyto možnosti jsou dostupné prostřednictvím založenou na poskytovateli prostředků Azure Cosmos DB [rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) a také na webu Azure portal.

Podívejte se na následující video a zjistěte funkce globální distribuce na klíč ve službě Azure Cosmos DB v akci.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Všudypřítomná místní přítomnost 
Azure je neustále rostoucí jeho přítomnost zeměpisné přenesením [nové oblasti](https://azure.microsoft.com/regions/) online. Azure Cosmos DB je klasifikován tak *podkladovou službu* v Azure a je k dispozici ve všech nových oblastech Azure ve výchozím nastavení. To umožňuje přidružit geografické oblasti účtu databáze Azure Cosmos DB, poté, co Azure otevře nové oblasti pro firmy.

### <a id="UnlimitedRegionsPerAccount"></a>Neomezený počet oblastí přidružení účtu databáze Azure Cosmos DB
Azure Cosmos DB umožňuje přidružit k účtu databáze Azure Cosmos DB libovolného počtu oblastí Azure. Mimo omezení monitorování geografických zón geograficky (například Čína, Německo) neexistují žádná omezení počtu oblastí, které mohou být přiřazená k vašemu účtu databáze Azure Cosmos DB. Následující obrázek znázorňuje databázový účet nakonfigurovaný tak, aby pracovat nad více 25 oblastech Azure.  

![Azure Cosmos DB databázový účet pokrývající 25 oblastech Azure](./media/distribute-data-globally/spanning-regions.png)

**Databáze tenanta služby Azure Cosmos DB účet STA 25 oblastech Azure**


### <a id="PolicyBasedGeoFencing"></a>Na základě zásad geograficky monitorování geografických zón
Azure Cosmos DB je navržená pro podporu na základě zásad geograficky monitorování geografických zón. Geograficky monitorování geografických zón je jejich důležitou součástí k zajištění omezení zásad správného řízení a dodržování předpisů dat a může bránit přidružení účtu konkrétní oblasti. Příklady geograficky monitorování geografických zón zahrnují (ale nejsou omezeny) oborů globální distribuci do oblastí v rámci suverénních cloudů (například Číny a Německa) nebo v rámci hranice zdanění státní správy (například Austrálie). Zásady jsou řízeny pomocí metadat vašeho předplatného Azure.

### <a id="DynamicallyAddRegions"></a>Dynamické přidání nebo odebrání oblastí
Azure Cosmos DB umožňuje (přidružení) přidat nebo odebrat (zrušit přidružení) oblastech z vašeho účtu databáze v libovolném bodě v čase (viz [předchozí obrázek](#UnlimitedRegionsPerAccount)). Tím, že paralelní replikaci dat napříč oddíly, Azure Cosmos DB zajišťuje, že při přidání získá novou oblast, je k dispozici pro operace do 30 minut kdekoli na světě (za předpokladu, že vaše data se 100 TB nebo nižší). 

### <a id="FailoverPriorities"></a>Priority převzetí služeb při selhání
Řídí přesný sled regionální převzetí služeb při selhání v případě výpadku služby Azure Cosmos DB umožňuje přidružit *priority* s různými oblastmi spojenými s databází účtu (viz následující obrázek). Azure Cosmos DB zajišťuje, že pořadí automatické převzetí služeb při selhání dojde v pořadí podle priority, kterou jste zadali. Další informace o regionálních převzetí služeb při selhání najdete v tématu [automatické regionální převzetí služeb při selhání pro zajištění kontinuity ve službě Azure Cosmos DB](regional-failover.md).


![Konfigurace priorit převzetí služeb při selhání pomocí služby Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

**Tenant služby Azure Cosmos DB můžete nakonfigurovat pořadí podle priority převzetí služeb při selhání (pravé podokno) pro oblasti přidružené k účtu databáze**

### <a id="ConsistencyLevels"></a>Více, jasně definované modely konzistence pro globálně distribuované databáze
Azure Cosmos DB podporuje [různé modely konzistence jasně definované, intuitivní a praktické](consistency-levels.md) podložená smlouvami SLA. Můžete zvolit konkrétní konzistence modelu (ze seznamu dostupných možností) v závislosti na zatížení a scénáře. 

### <a id="TunableConsistency"></a>Konzistence s možností vyladění globálně replikovaného databází
Azure Cosmos DB umožňuje programově přepsat a zmírnit výchozí volba konzistence na základě požadavku za běhu. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamicky konfigurovat pro čtení a zápisu oblasti
Azure Cosmos DB umožňuje konfigurovat oblastech (přidružené k databázi) pro "čtení", "write" nebo "r/w" oblastech. 

### <a id="ElasticallyScaleThroughput"></a>Elastické škálování propustnosti napříč oblastmi Azure
Dá se pružně škálovat kontejneru Azure Cosmos DB pomocí zřizování propustnosti prostřednictvím kódu programu. Propustnost se použije u všech oblastech, které kontejner Azure Cosmos DB je distribuován v.

### <a id="GeoLocalReadsAndWrites"></a>GEO-local čtení a zápisy
Klíčovou výhodou globálně distribuovanou databázi je, že nabízí nízkou latenci přístupu pro data po celém světě. Azure Cosmos DB nabízí zajištění nízké latence čtení a zápisů v 99. percentilu po celém světě. Zajišťuje, že všechny operace čtení se obsluhují z nejbližší oblasti (místní). K poskytování žádost o čtení, se používá místní oblasti, ve kterém je vydaný čtení kvora. Totéž platí i pro zápis. Zápis se uznává jenom po většinu repliky mít odolném potvrzení zápisu místně, ale bez se gated na vzdálené repliky potvrdí zápisy. Chcete-li jinak řečeno, pracuje s protokolu replikace služby Azure Cosmos DB za předpokladu, že kvor čtení a zápis jsou vždy místní oblasti, ve kterém byl vydán požadavek.

### <a id="ManualFailover"></a>Ruční převzetí služeb při selhání
Azure Cosmos DB umožňuje aktivovat převzetí služeb při selhání na databázovém účtu k ověření *komplexní* vlastnosti dostupnost celé aplikace (nad rámec databáze). Protože je zaručena bezpečnost a vlastnosti aktivity selhání volby zjišťování a vedoucí instance, Azure Cosmos DB zaručuje *nulové ztráty dat* pro operace, která iniciovala tenanta ruční převzetí služeb při selhání.

### <a id="AutomaticFailover"></a>Automatické převzetí služeb při selhání
Azure Cosmos DB podporuje automatické převzetí služeb při selhání během jednoho nebo více místních výpadků. Během regionální převzetí služeb při selhání služby Azure Cosmos DB udržuje latence čtení, dostupnosti doby provozu, konzistence a propustnost smlouvy o úrovni služeb. Azure Cosmos DB poskytuje horní mez po dobu trvání na dokončení operace automatického převzetí služeb při selhání. To je okno potenciální ztráty dat během oblastního výpadku.

### <a id="GranularFailover"></a>Navržené pro přírůstcích různých převzetí služeb při selhání
Možnosti automatického a ručního převzetí služeb při selhání jsou aktuálně umístěné na členitosti databázový účet. Všimněte si, interně služby Azure Cosmos DB je určená k *automatické* převzetí služeb při selhání podrobnější rozlišovací schopnosti databáze, kontejner nebo dokonce rozdělení (kontejner vlastnící rozsah klíčů). 

### <a id="MultiHomingAPIs"></a>Vícenásobné navádění ve službě Azure Cosmos DB
Azure Cosmos DB umožňuje interakci s databází pomocí *logické* (nezávislých na oblasti) nebo *fyzické* koncových bodů (závislých na oblasti). Pomocí logických koncových bodů zajistí, že možné aplikaci s více adresami v případě selhání. Druhá možnost, fyzický koncový bod, zajišťuje přesnější kontrolu přesměrování čtení aplikace a zapíše do konkrétních oblastí.

Informace o tom, jak nakonfigurovat další předvolby pro [rozhraní SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [rozhraní Table API](../cosmos-db/tutorial-global-distribution-table.md), a [rozhraní MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) v těchto článcích.

### <a id="TransparentSchemaMigration"></a>Migrace schémat a indexů databáze transparentní a konzistentní vzhledem k aplikacím 
Azure Cosmos DB je plně [dogmaticky na schématu](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Jedinečné návrhu databázového stroje Azure Cosmos DB umožňuje automaticky a synchronně indexovat všechna data při příjmu, bez vyžadování jakéhokoli schématu nebo sekundárních indexů od uživatele. To umožňuje rychle iterovat globálně distribuované aplikace bez starostí o migraci schémat a indexů databáze nebo koordinací uvádění aplikací vícefázových změn schématu. Azure Cosmos DB zaručuje, že jakékoli změny explicitně provedli vy zásadám indexování za následek snížení výkonu nebo dostupnosti.  

### <a id="ComprehensiveSLAs"></a>Komplexní smlouvy SLA (nad rámec vysoká dostupnost)
Azure Cosmos DB jakožto globálně distribuovaná databázová služba, nabízí dobře definovaný a komplexní smlouvy SLA pro **dostupnosti**, **latence**, **propustnost**a **konzistence** pro databázi spuštěna v globálním měřítku, bez ohledu na to počtem oblastí přidružených k databázi.  

## <a id="LatencyGuarantees"></a>Záruky latence
Klíčovou výhodou globálně distribuovaná databázová služba, jako je Azure Cosmos DB je nabídnout přístup s nízkou latencí pro data po celém světě. Azure Cosmos DB nabízí garantovanou nízkou latencí na 99. percentilu pro různých databázových operací. Protokol replikace, která používá službu Azure Cosmos DB zajišťuje, že databázových operací (čtení a zápisy) se vždy provádějí v oblasti místní účet, který klienta. Latence smlouvy SLA Azure Cosmos DB poskytuje záruky na 99. percentilu pro čtení, (synchronní) indexovaných zápisů a dotazů pro různé velikosti požadavků a odpovědí. Záruky latence pro zápis zahrnují trvalý většinu kvora potvrzení změn v rámci místní oblasti.

### <a id="LatencyAndConsistency"></a>Čekací doba na relaci s konzistence 
U globálně Distribuovaná služba, která nabízí silnou konzistenci v globálně distribuované instalaci potřebuje synchronní replikace zápisy nebo křížová provádět synchronně. Rychlostí světla a spolehlivost sítě WAN určí, že zajišťuje velkou konzistenci způsobí vyšší latencí a databázové operace je omezená dostupnost. Proto aby nabízela zaručené nízké latence na 99. percentilu a 99,99 % dostupnosti pro všechny účty v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999 % dostupnost pro všechny účty databáze ve více oblastech, služby musíte použít asynchronní replikace. Tato naopak vyžaduje službu musí navíc nabízí [modely konzistence jasně definované, volný](consistency-levels.md) – nižší než silné (Chcete-li nabízí záruky nízké latence a dostupnosti) a v ideálním případě silnější než "konzistencí" (pomocí Výsledkem je intuitivní programovací model).

Azure Cosmos DB zajišťuje, že není nutné kontaktovat replik napříč několika oblastmi poskytovat záruku úrovně konzistence konkrétní operace čtení. Podobně, zajistí, že operace zápisu získat neblokován při se replikuje data napříč všemi oblastmi to znamená, zápisy se asynchronně replikuje napříč oblastmi). Pro účty databáze pro více oblastí jsou k dispozici obě silné a také více úrovní mírnější konzistencí. 

### <a id="LatencyAndAvailability"></a>Čekací doba na relaci s dostupností 
Obě strany tohoto stejného mince je latence a dostupnost. Mluví o latence operace do stabilního stavu a dostupnosti za přítomnosti chyb a rozdělení sítě. Z pohledu aplikace je pomalá běžící operaci databáze nerozeznatelná od databáze, která je k dispozici. 

Azure Cosmos DB pro odlišení od nedostupnost vysokou latencí, poskytuje absolutní horní mez pro latenci různých databázových operací. Pokud databázová operace trvá déle než horní mez pro dokončení, Azure Cosmos DB vrátí vypršení časového limitu. Smlouva SLA o dostupnosti služby Azure Cosmos DB zajišťuje, že vypršení časového limitu se započítávají i smlouva SLA o dostupnosti. 

### <a id="LatencyAndThroughput"></a>Čekací doba na relaci s propustností
Azure Cosmos DB není vás nutí vybrat si mezi latence a propustnosti. Respektuje smlouva SLA pro obě latence na 99. percentilu a zajišťuje propustnost, kterou jste zřídili. 

## <a id="ConsistencyGuarantees"></a>Záruky konzistence
Zatímco [model pro zajištění konzistence](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) je zlatým standardem z dat programovatelnosti dodává strmá cenu vyšší latence (v stabilního stavu) a nižší dostupnost (i v případě selhání). 

Azure Cosmos DB nabízí jasně definovaných programovací model pro vás o konzistence replikovaná data. Chcete-li povolit, můžete snadno vytvářet globálně distribuované aplikace s vícenásobné navádění funkcí, jsou navržena jako nezávislých na oblasti a nezávislé z oblasti, ve kterém se nebudou operací čtení a zápisů modelů konzistence vystavený službou Azure Cosmos DB obsluhuje. 

Azure Cosmos DB konzistence SLA zaručuje, že 100 % požadavků na čtení vyhoví záruce konzistence pro model pro zajištění konzistence zadané (buď na úrovni požadavku nebo účet databáze). Žádost o čtení se považuje za splnili konzistence smlouvou SLA, pokud jsou splněny všechny záruky konzistence spojené s úrovní konzistence. Následující tabulka udává záruky konzistence, které odpovídají konkrétní konzistence modely, které nabízí služba Azure Cosmos DB.

<table>
    <tr>
        <td><strong>Model pro zajištění konzistence</strong></td>
        <td><strong>Vlastnosti konzistence</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
        <tr>
        <td>Silné</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Omezená neaktuálnost</td>
        <td>Monotónní čtení (v rámci oblasti)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konzistentní předpona</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Neaktuálnost vázán &lt; K, T</td>
        <td>100%</td>
    </tr>
<tr>
        <td rowspan="3">Relace</td>
        <td>Čtení a zápis vlastní</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Monotónního čtení</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konzistentní předpona</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konzistentní předpona</td>
        <td>Konzistentní předpona</td>
        <td>100%</td>
    </tr>
</table>

**Záruky konzistence, které jsou přidružené k dané konzistence modelu ve službě Azure Cosmos DB**


### <a id="ConsistencyAndAvailability"></a>Konzistence v relaci s dostupností
[Nemožností výsledek](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) z [věty](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prokazuje, že je skutečně možné systému zůstat dostupný a nabízejí linearizable konzistence i v případě selhání. Databázové služby musíte zvolit prohlášení CP nebo Asie a Tichomoří, kde CP systémy forgo dostupnosti ve prospěch linearizable konzistence, zatímco forgo systémy Asie a Tichomoří [linearizable konzistence](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) ve prospěch dostupnosti. Azure Cosmos DB je v rozporu nikdy modelu požadovaný konzistence formálně umožňuje CP systému. Ale v praxi konzistence není všechno nebo nic návrh; Existují různé jasně definované modely konzistence podél spektra konzistence mezi linearizable a konečné konzistence. Ve službě Azure Cosmos DB identifikuje několik mírnější konzistencí modely, které se vztahují na reálných scénářů a jsou pro použití intuitivní. Azure Cosmos DB přejde kompromisy konzistence dostupnosti tím, že nabízí [více mírnější ještě jasně definované modely konzistence](consistency-levels.md) a 99,99 % dostupnosti pro všechny jednotlivé účty databáze v oblasti a 99,999 % čtení a zápis dostupnost pro všechny účty databáze ve více oblastech. 

### <a id="ConsistencyAndAvailability"></a>Konzistence v relaci s latencí
Komplexnější varianta věty se nazývá [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), což také účty pro latence a konzistence kompromisy do stabilního stavu. Uvádí, že do stabilního stavu, musíte databázový systém zvolit mezi konzistencí a latenci. S více modely mírnější konzistencí (využívající asynchronní replikace a místní čtení a zápisu kvor) Azure Cosmos DB zajišťuje, že všechny operace čtení a zápisu jsou místní pro čtení a zápis oblastí v uvedeném pořadí. To umožňuje službě Azure Cosmos DB nabízí záruky nízké latence v rámci oblasti pro modely konzistence dané.  

### <a id="ConsistencyAndThroughput"></a>Konzistence v relaci s propustností
Protože implementaci modelu konkrétní konzistence, závisí na výběr [typ kvora](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), propustnost také se liší v závislosti na výběru modelu konzistence. Například ve službě Azure Cosmos DB RU za silně konzistentních čtení je přibližně *double* s konečnou konzistencí čtení. V takovém případě je potřeba zřídit double ru k dosažení stejného propustnost.


![Vztah mezi konzistencí a propustnosti](./media/distribute-data-globally/consistency-and-throughput.png)

**Vztah mezi další kapacitu pro konkrétní konzistence modelu ve službě Azure Cosmos DB**

## <a id="ThroughputGuarantees"></a>Záruky propustnost 
Azure Cosmos DB umožňuje škálování propustnosti (stejně jako, úložiště), Elasticky napříč libovolným počtem oblastí v závislosti na vašim potřebám nebo vyžádání. 

![Azure Cosmos DB distribuované a rozdělit na oddíly kontejnery](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Jeden kontejner Azure Cosmos DB horizontálně dělené do oddílů (mezi tři oddíly prostředků v rámci oblasti) a globálně distribuované napříč tři oblasti Azure**

Získá kontejner služby Azure Cosmos DB distribuovat ve dvou dimenzích (i) v rámci oblasti a (ii) napříč oblastmi. Zde je uveden postup: 

* **Místní distribuční**: v rámci jedné oblasti a kontejner služby Azure Cosmos DB je horizontálně škálovat z hlediska *oddílů prostředků*. Každý oddíl prostředků spravuje sadu klíčů a je silně konzistentních a s vysokou dostupností fyzicky reprezentované čtyři repliky zkratka *sady replik* a stavu replikace počítačů mezi tyto repliky. Azure Cosmos DB je plně řídí prostředků systému, kde je zodpovědné doručování svůj podíl propustnost pro rozpočtu systémové prostředky přidělené oddíl prostředků. Škálování kontejneru Azure Cosmos DB je pro uživatele transparentní. Azure Cosmos DB spravuje oddíly prostředků a rozdělí a sloučí je podle potřeby jako úložiště a propustnost požadavky se změní. 
* **Globální distribuce**: Pokud je databáze ve více oblastech, všech oddílů prostředků je poté distribuován v těchto oblastech. Oddíly prostředků vlastnící stejnou sadu klíčů mezi různé oblasti formuláře *sadou oddílů* (naleznete v tématu [předchozí obrázek](#ThroughputGuarantees)).  Oddíly prostředků v rámci oddílu se koordinují pomocí replikace počítačů stav napříč několika oblastmi spojenými s databází. V závislosti na úrovně konzistence, nakonfigurovat jsou nakonfigurované oddílů prostředků v rámci oddílu dynamicky pomocí různých topologií (například star, sériově, stromu atd.). 

Tím, že o správu oddílu s velmi rychlou odezvou, Vyrovnávání zatížení a zásady správného řízení prostředků striktní Azure Cosmos DB umožňuje elastické škálování propustnosti ve víc oblastech Azure, které jsou přidružená ke kontejneru služby Azure Cosmos DB nebo databáze. Změna zřízená propustnost je běhová operace ve službě Azure Cosmos DB. Podobně jako u jiných databázových operací, Azure Cosmos DB zaručuje absolutní horní mez na latenci pro žádost o změnu zřízenou propustnost. Například následující obrázek znázorňuje zákazníka kontejneru se Elasticky zřízené propustnosti (v rozsahu 1 až 10 milionů požadavků za sekundu napříč dvěma oblastmi) podle požadavku.

![Azure Cosmos DB Elasticky zřízené propustnosti](./media/distribute-data-globally/elastic-throughput.png)

**Kontejner zákazníka se Elasticky zřízená propustnost (různé z 1 až 10 milionů požadavků za sekundu)**

### <a id="ThroughputAndConsistency"></a>Propustnost vaší relace s konzistencí 
Je stejný, jak je popsáno v [konzistence v relaci s propustností](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Propustnost vaší relace s dostupností
Azure Cosmos DB nadále spravovat jeho vysokou dostupnost při změně zřízenou propustnost. Azure Cosmos DB transparentně spravuje oddíly prostředků (a provádí rozdělení a sloučení a klonování operations) a zajišťuje, že operace nezhorší výkon nebo dostupnost, zatímco aplikace Elasticky zvýšení nebo snížení propustnosti. 

## <a id="AvailabilityGuarantees"></a>Záruky dostupnosti
Azure Cosmos DB nabízí 99,99 % dostupnosti smlouvy SLA pro všechny účty databáze v jedné oblasti a všechny účty ve více oblastech s mírnější konzistencí a 99,999 % dostupnost pro všechny účty databáze ve více oblastech. Jak je popsáno výše, zahrnují záruky dostupnosti služby Azure Cosmos DB absolutní horní mez pro latenci pro každé operace roviny dat a ovládací prvek. Záruky dostupnosti neměňte s počtem oblastí nebo geografické vzdálenosti mezi oblastmi. Záruky dostupnosti se dají použít s ohledem na ručního i automatického převzetí služeb při selhání. Azure Cosmos DB nabízí transparentní vícenásobné navádění rozhraní API, která zajistěte, aby vaše aplikace může pracovat s logických koncových bodů a transparentně směrovat požadavky do nové oblasti v případě selhání. Vaše aplikace není nutné znovu nasadit v případě, že regionální převzetí služeb při selhání a dostupnosti jsou zachovány smlouvy o úrovni služeb za všech okolností.

### <a id="AvailabilityAndConsistency"></a>Dostupnost vaší relace s konzistence, latence a propustnosti
Dostupnost vaší relace s konzistence, latence a propustnosti je popsána v částech [konzistence v relaci s dostupností](#ConsistencyAndAvailability), [čekací doba na relaci s dostupností](#LatencyAndAvailability) a [Propustnost vaší relace s dostupností](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Metriky SLA určenou pro zákazníky
Azure Cosmos DB transparentně zpřístupňuje metriky propustnosti, latence, konzistenci a dostupnost. Tyto metriky jsou k dispozici prostřednictvím kódu programu a prostřednictvím webu Azure portal (viz následující obrázek). Můžete také nastavit výstrahy na různé prahové hodnoty pomocí Azure Application Insights.
 

![Azure Cosmos DB zákazníky viditelné metrik SLA](./media/distribute-data-globally/customer-slas.png)

**Metriky konzistence, latence, propustnosti a dostupnosti jsou dostupná transparentně ke každému tenantovi**

## <a id="Next Steps"></a>Další kroky
* K implementaci globální replikace ve vašem účtu Azure Cosmos DB pomocí webu Azure portal, najdete v článku [provádění replikace globální databáze Azure Cosmos DB pomocí webu Azure portal](tutorial-global-distribution-sql-api.md).
* Další informace o tom, jak implementovat multimasterovou architektury pomocí služby Azure Cosmos DB najdete v tématu [architektury více hlavních databází pomocí služby Azure Cosmos DB](multi-region-writers.md).
* Další informace o tom, jak automatické a ruční převzetí služeb při selhání fungovat ve službě Azure Cosmos DB najdete v tématu [regionální převzetí služeb při selhání ve službě Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Odkazy
1. Eric Bureš. [Směrem k robustní distribuovaných systémů](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Bureš. [Zakončení 12 letech později – jak se mění pravidla](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Gilbertovy, Lynch. - [Bureš&#39;s domněnek a proveditelnosti konzistentní vzhledem k aplikacím, k dispozici, oddílu odolný vůči chybám webových služeb](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [Konzistence kompromisů v moderních distribuované systémy návrh databáze](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Martin Kleppmann. [Zastavte prosím volání databáze prohlášení CP nebo Asie a Tichomoří](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Petr Bailis kolektiv autorů [Omezená Neaktuálnost pravděpodobnostní (PBS) pro praktické částečné kvor](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor a vlny. [Zatížení, kapacity a dostupnosti v systémech kvora](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy a výskytů. [Lineralizability: Správnosti podmínku pro souběžné objekty](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [SLA služby Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
