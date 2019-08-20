---
title: Výběr cenové úrovně nebo SKU pro službu Azure Search Service – Azure Search
description: 'Azure Search lze zřídit v těchto SKU: Bezplatné, základní a standardní a standardní jsou dostupné v různých konfiguracích prostředků a úrovních kapacity.'
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: a874c8a1fe2e8a81e2f42b2c88447fd52b47f3ad
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611962"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Vyberte cenovou úroveň pro Azure Search

Když vytvoříte službu Azure Search, vytvoří [se prostředek](search-create-service-portal.md) na cenové úrovni (nebo SKU), která se pro dobu života služby vydává. Mezi vrstvy patří optimalizace Free, Basic, Standard a Storage. Optimalizované úložiště Standard a Storage jsou k dispozici v několika konfiguracích a kapacitě.

Většina zákazníků začíná na bezplatné úrovni, takže může službu vyhodnotit. Po vyhodnocení je běžné vytvořit druhou službu na jedné z vyšších úrovní vývoje a produkčních nasazení.

I když všechny úrovně, včetně úrovně Free, všeobecně nabízejí paritu funkcí, větší úlohy můžou vymezit potřebu vyšších úrovní. Například [rozšíření AI s Cognitive Services](cognitive-search-concept-intro.md) má dlouhodobě běžící dovednosti, které vyprší časový limit bezplatné služby, pokud není datová sada malá.

> [!NOTE] 
> Výjimkou parity funkcí jsou [indexery](search-indexer-overview.md), které nejsou k dispozici na standardu S3 HD.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Dostupné úrovně

Úrovně odrážejí charakteristiky hardwaru, který hostuje službu (spíše než funkce) a které jsou odlišené:

+ Množství indexů a indexerů, které můžete vytvořit
+ Velikost a rychlost oddílů (fyzické úložiště)

Vybraná úroveň určuje fakturovatelnou sazbu. Následující snímek obrazovky z Azure Portal zobrazuje dostupné úrovně minus ceny (které najdete na portálu a na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/). Nejběžnějšími úrovněmi jsou **bezplatné**, **základní**a **standardní** úrovně.

**Free** vytvoří v clusteru omezený vyhledávací službu, která je sdílená s ostatními předplatiteli. Můžete dokončit malé projekty, včetně rychlých startů a kurzů, ale nemůžete škálovat službu ani provádět významné úlohy. **Základní** a **standardní** jsou nejčastěji používané Fakturovatelné úrovně s výchozím nastavením **Standard** .

![Cenové úrovně Azure Search](media/search-sku-tier/tiers.png "Cenové úrovně Azure Search")

Některé úrovně jsou optimalizované pro určité typy práce. Například **Standard 3 vysoká hustota (S3 HD)** je *hostující režim* pro S3, kde je základní hardware optimalizovaný pro velký počet menších indexů a je určený pro víceklientské scénáře. S3 HD má stejné poplatky za jednotku jako S3, ale hardware je optimalizovaný pro rychlé čtení souborů na velkém počtu menších indexů.

Vrstvy optimalizované pro **úložiště** nabízejí větší kapacitu úložiště s nižší cenou za TB než úrovně Standard. Primární kompromis je vyšší latence dotazů, které byste měli ověřit pro konkrétní požadavky na aplikaci.  Další informace o požadavcích na výkon této úrovně najdete v tématu věnovaném důležitým informacím o [výkonu a optimalizaci](search-performance-optimization.md).

Další informace o různých úrovních najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/), v části [omezení služby v Azure Search](search-limits-quotas-capacity.md) článku a na stránce portálu při zřizování služby.

## <a name="billable-events"></a>Fakturovatelné události

K řešení postavenému na Azure Search může doplatit následující možnosti:

+ Základní náklady na službu s minimální konfigurací
+ Přírůstkové náklady při vertikálním navýšení kapacity (přidávání replik nebo oddílů)
+ Náklady na šířku pásma pro přenos odchozích dat
+ Vyhledávání rozpoznávání využití prostředků Cognitive Services

### <a name="service-costs"></a>Náklady na službu

Na rozdíl od virtuálních počítačů nebo jiných prostředků, které mohou být pozastavené, aby se předešlo poplatkům, je služba Azure Search vždy k dispozici na hardwaru vyhrazeném pro výhradní použití. Vytvoření služby je fakturovatelná událost, která se spustí při vytvoření služby a končí při odstranění služby. 

Minimální poplatek je první jednotka vyhledávání (jedna replika x jeden oddíl) s fakturovatelnou sazbou. Toto minimum je pevně nastavené pro dobu života služby, protože služba nemůže běžet bez jakýchkoli těchto konfigurací. Kromě minima můžete přidat repliky a oddíly nezávisle na sobě. Přírůstkové zvýšení kapacity prostřednictvím replik a oddílů zvýší vaše vyúčtování na základě následujícího vzorce: [(repliky × oddíly x sazba)](#search-units), kde se sazba vám bude účtovat, záleží na cenové úrovni, kterou vyberete.

Při odhadování nákladů na řešení hledání mějte na paměti, že ceny a kapacita nejsou lineární. (Dvojnásobná kapacita větší než dvojnásobek nákladů.) Příklad toho, jak vzorec funguje, najdete v tématu [jak přidělit repliky a oddíly](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Poplatky za šířku pásma

Použití [Azure Search indexerů](search-indexer-overview.md) může ovlivnit fakturaci v závislosti na umístění vašich služeb. Pokud vytvoříte službu Azure Search ve stejné oblasti, ve které jsou vaše data, můžete poplatky za odchozí přenosy dat zcela eliminovat. Tady jsou některé informace na [stránce s cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft neúčtuje žádná příchozí data do žádné služby v Azure ani pro žádná odchozí data z Azure Search.
+ V řešeních s více službami se za přenos dat mezi všemi službami ve stejné oblasti neúčtují žádné poplatky.

Poplatky platí pro odchozí data, pokud jsou služby v různých oblastech. Tyto poplatky nejsou ve skutečnosti součástí Azure Search faktury. Jsou tady uvedené, protože pokud používáte data nebo indexovací indexery AI k vyžádání dat z různých oblastí, uvidíte v celkové faktuře náklady.

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>Obohacení vyhledávání AI pomocí Cognitive Services

Pro [obohacení AI pomocí Cognitive Services](cognitive-search-concept-intro.md)byste měli naplánovat [připojení Fakturovatelné služby Azure Cognitive Services](cognitive-search-attach-cognitive-services.md)ve stejné oblasti jako Azure Search na cenové úrovni S0 pro zpracování průběžných plateb. K připojení Cognitive Services nejsou žádné pevné náklady. Platíte jenom za zpracování, které potřebujete.

| Operace | Dopad fakturace |
|-----------|----------------|
| Trhliny dokumentů, extrakce textu | Zdarma |
| Trhliny dokumentů, extrakce obrázků | Účtuje se podle počtu imagí extrahovaných z vašich dokumentů. V [konfiguraci indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)je **imageAction** parametr, který aktivuje extrakci imagí. Pokud je **imageAction** nastavené na None (výchozí nastavení), nebudete se vám za extrakci imagí účtovat. Sazba za extrakci obrázku je popsána na stránce s podrobnostmi o [cenách](https://azure.microsoft.com/pricing/details/search/) pro Azure Search.|
| [Předem připravené schopnosti rozpoznávání](cognitive-search-predefined-skills.md) | Účtuje se se stejnou sazbou, jako kdyby jste provedli úlohu přímo pomocí Cognitive Services. |
| Vlastní dovednosti | Vlastní dovednost je funkce, kterou zadáte. Náklady na používání vlastní dovednosti závisí výhradně na tom, jestli vlastní kód volá jiné měřené služby. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Vzorec fakturace (R × P = SU)

Nejdůležitější koncept fakturace pro pochopení Azure Search operací je *jednotka hledání* (SU). Vzhledem k tomu, že Azure Search závisí na obou replikách a oddílech pro indexování a dotazy, neznamená to, že se účtuje jenom pomocí jedné nebo druhé. Místo toho je fakturace založena na složeném z obou.

SU je produktem *replik* a *oddílů* používaných službou: **(R × P = SU)** .

Každá služba začíná jednou SU (jedna replika vynásobená jedním oddílem) jako minimální. Maximum pro jakoukoli službu je 36 SUs. Toto maximum je možné dosáhnout několika způsoby: 6 oddílů je 6 – repliky x 6 nebo 3 oddíly x 12 replik, například. Je běžné použít méně než celkovou kapacitu (například 3-replika, 3-partition Service se účtuje jako 9 SUs). Platné kombinace najdete v grafu [kombinací oddílů a repliky](search-capacity-planning.md#chart) .

Fakturační sazba je každou hodinu. Každá úroveň má postupně vyšší míru. Vyšší úrovně se dodávají s většími a rychlými oddíly a to přispívá k celkové hodinové sazbě za tuto úroveň. Sazby za jednotlivé úrovně můžete zobrazit na stránce s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) .

Většina zákazníků přinese jenom část celkové kapacity online, která uchovává zbývající rezervu. Pro účely fakturace určuje počet oddílů a replik, které přivedete do režimu online, vypočítané vzorcem SU za každou hodinu.

## <a name="how-to-manage-and-reduce-costs"></a>Jak spravovat a snižovat náklady

Kromě následujících návrhů přejděte na téma [fakturace a Správa nákladů](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Vytvořte všechny prostředky ve stejné oblasti nebo v několika oblastech, abyste minimalizovali nebo vyloučili poplatky za šířku pásma.

- Konsolidujte všechny služby do jedné skupiny prostředků, jako je Azure Search, Cognitive Services a všechny další služby Azure používané ve vašem řešení. V Azure Portal Najděte skupinu prostředků a pomocí Cost Managementch příkazů si Projděte informace o skutečné a předpokládané útratě.

- Zvažte webovou aplikaci Azure pro front-end aplikaci, aby žádosti a odpovědi zůstaly v rámci hranice datového centra.

- Nahorizontální navýšení operací náročných na prostředky, jako je indexování, a následná změna nastavení pro běžné úlohy dotazů. Začněte s minimální konfigurací pro Azure Search (jeden SU tvořený jedním oddílem a jednou replikou) a pak sledujte aktivitu uživatelů a Identifikujte vzorce používání, které by znamenaly nutnost větší kapacity. Pokud existuje předvídatelný vzor, může být možné synchronizovat škálování s aktivitou (pro automatizaci musíte napsat kód).

Službu vyhledávání nemůžete vypnout, aby se snížila vaše vyúčtování. Vyhrazené prostředky jsou vždycky v provozu a jsou vyhrazené pro vaše výhradní použití po dobu života vaší služby. V rámci samotné služby je jediným způsobem, jak snížit vaše vyúčtování, je snížit počet replik a oddílů na úroveň, která stále poskytuje přijatelný výkon a [dodržování předpisů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), nebo vytvořit službu na nižší úrovni (hodinové sazby S1 jsou nižší než ceny S2 a S3). Za předpokladu, že jste službu zařídili na konci vašich projekce zatížení, můžete při zvětšování služby vytvořit druhou větší službu, znovu sestavit indexy na druhou službu a pak odstranit první z nich.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak vyhodnotit požadavky na kapacitu

V Azure Search je kapacita strukturovaná jako *repliky* a *oddíly*.

+ Repliky jsou instance služby Search Service. Každá replika hostuje jednu kopii indexu s vyrovnáváním zatížení. Například služba se šesti replikami má šest kopií každého indexu načteného ve službě.

+ Oddíly ukládají indexy a automaticky rozdělí hledaná data. Dva oddíly rozdělí rejstřík na polovinu, tři oddíly se rozdělí na třetí a tak dále. V souvislosti s kapacitou je *velikost oddílu* primární odlišná funkce mezi vrstvami.

> [!NOTE]
> Všechny úrovně optimalizované pro Standard a úložiště podporují [flexibilní kombinace replik a oddílů](search-capacity-planning.md#chart) , takže můžete [optimalizovat systém pro rychlost nebo úložiště](search-performance-optimization.md) změnou zůstatku. Úroveň Basic nabízí až tři repliky pro vysokou dostupnost, ale má jenom jeden oddíl. Bezplatné úrovně neposkytují vyhrazené prostředky: výpočetní prostředky sdílí více odběratelů.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Vyhodnocování kapacity

Kapacita a náklady na provoz služby se dostanou rukou. Vrstvy ukládají omezení na dvou úrovních: úložiště a prostředky. Měli byste si myslet na oba, protože podle toho, co se vám limit dosahuje, je platný limit.

Obchodní požadavky obvykle určují počet indexů, které budete potřebovat. Například je možné, že budete potřebovat globální index pro velké úložiště dokumentů. Případně je možné, že budete potřebovat více indexů na základě oblastí, aplikací nebo Business mezery.

Chcete-li určit velikost indexu, je třeba [ho sestavit](search-create-index-portal.md). Struktura dat v Azure Search je primárně obrácená struktura [indexu](https://en.wikipedia.org/wiki/Inverted_index) , která má jiné charakteristiky než zdrojová data. U převráceného indexu se velikost a složitost určují podle obsahu, nikoli nutně podle množství dat, která do něj zadáte. Velký zdroj dat s vysokou redundancí může mít za následek menší index než menší datová sada, která obsahuje vysoce proměnlivý obsah. Je proto možné pouze odvodit velikost indexu na základě velikosti původní datové sady.

> [!NOTE] 
> I když odhaduje budoucí potřeby indexů a úložiště, může to vypadat stejně jako v případě vysokého odhadu. Pokud je kapacita vrstvy příliš nízká, budete muset zřídit novou službu na vyšší úrovni a pak [znovu načíst své indexy](search-howto-reindex.md). Neexistuje žádný místní upgrade služby z jedné SKU na jinou.
>

### <a name="estimate-with-the-free-tier"></a>Odhad s úrovní Free

Jedním z přístupů k odhadu kapacity je začít s úrovní Free. Mějte na paměti, že bezplatná služba nabízí až tři indexy, 50 MB úložiště a 2 minuty času indexování. Může být náročné odhadnout velikost předpokládaného indexu s těmito omezeními, ale postup je následující:

+ [Vytvořte bezplatnou službu](search-create-service-portal.md).
+ Připravte malou, reprezentativní datovou sadu.
+ [Sestavte počáteční index na portálu](search-create-index-portal.md) a poznamenejte si jeho velikost. Funkce a atributy mají dopad na úložiště. Například přidání návrhů (typeahead) bude zvyšovat požadavky na úložiště. Pomocí stejné datové sady můžete zkusit vytvořit několik verzí indexu s různými atributy každého pole, abyste viděli, jak se požadavky na úložiště liší. Další informace najdete v části ["dopady na úložiště" v tématu Vytvoření základního indexu](search-what-is-an-index.md#storage-implications).

V případě hrubého odhadu se může tato částka zdvojnásobit do rozpočtu pro dva indexy (vývoj a produkce) a odpovídajícím způsobem zvolit svou úroveň.

### <a name="estimate-with-a-billable-tier"></a>Odhad s fakturovatelnou úrovní

Vyhrazené prostředky můžou sloužit k většímu počtu vzorkování a zpracování za účelem realističtějších odhadů množství indexu, velikosti a svazků dotazů během vývoje. Někteří zákazníci najdou přímo v rámci Fakturovatelné úrovně a pak se znovu vyhodnotí jako vývoj projektu vývoje.

1. [Zkontrolujte omezení služeb na jednotlivých úrovních](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) , abyste zjistili, jestli nižší úrovně můžou podporovat potřebný počet indexů. V úrovních Basic, S1 a S2 jsou limity indexu 15, 50 a 200, v uvedeném pořadí. Optimalizovaná úroveň úložiště má omezení 10 indexů, protože je navržena tak, aby podporovala nízký počet velmi rozsáhlých indexů.

1. [Vytvoření služby v Fakturovatelné úrovni](search-create-service-portal.md):

    + Pokud si nejste jisti plánovaným zatížením, začněte na stránce Basic nebo S1 nízká.
    + Pokud víte, že budete mít rozsáhlé indexování a načítání dotazů, začněte vysokou, v S2 nebo dokonce S3.
    + Pokud indexuje velké množství dat a zatížení dotazů je relativně nízké, stejně jako u interní obchodní aplikace, začněte s optimalizovaným úložištěm, v L1 nebo L2.

1. [Sestavte počáteční index](search-create-index-portal.md) pro určení způsobu, jakým zdrojová data přecházejí do indexu. Toto je jediný způsob, jak odhadnout velikost indexu.

1. Na portálu [monitorujte úložiště, omezení služeb, svazek dotazů a latenci](search-monitor-usage.md) . Na portálu se zobrazí dotazy za sekundu, omezené dotazy a latence hledání. Všechny tyto hodnoty vám pomohou při rozhodování, zda jste vybrali správnou úroveň. Můžete také nakonfigurovat hloubkové monitorování hodnot, jako jsou například interaktivní analýzy, a povolit tak [vyhledávání analýz provozu](search-traffic-analytics.md).

Číslo a velikost indexu jsou stejně důležité pro vaši analýzu. Důvodem je to, že se dosáhne maximálního využití úložiště (oddílů) nebo podle maximálního počtu prostředků (indexy, indexery a tak dále), podle toho, co nastane dřív. Portál vám pomůže sledovat obojí, zobrazuje aktuální využití a maximální limity vedle sebe na stránce Přehled.

> [!NOTE]
> Požadavky na úložiště můžou být neploché, pokud dokumenty obsahují nadbytečné údaje. V ideálním případě dokumenty obsahují pouze data, která potřebujete pro vyhledávání. Binární data se nedají prohledávat a měla by se ukládat samostatně (možná v tabulce Azure nebo v úložišti objektů BLOB). Pole by pak mělo být přidáno do indexu, aby obsahovalo odkaz na adresu URL externích dat. Maximální velikost jednotlivého dokumentu je 16 MB (nebo méně, pokud hromadně nahráváte více dokumentů v jednom požadavku). Další informace najdete v tématu [omezení služby v Azure Search](search-limits-quotas-capacity.md).
>

**Požadavky na svazky dotazů**

Dotazy za sekundu (QPS) jsou důležitou metrikou při vyladění výkonu, ale obecně platí pouze pro zvážení vrstvy, pokud očekáváte, že na začátku budete očekávat velký objem dotazů.

Úrovně Standard můžou poskytovat rovnováhu mezi replikami a oddíly. Můžete zvýšit vyřízení dotazu přidáním replik pro vyrovnávání zatížení nebo přidat oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

Pokud na začátku očekáváte velké svazky dotazů, měli byste zvážit vyšší úrovně Standard, která je zajištěná výkonnějším hardwarem. Pak můžete převzít oddíly a repliky offline, nebo dokonce přepnout na službu nižší úrovně, pokud k těmto svazkům dotazu nedochází. Další informace o tom, jak vypočítat propustnost dotazů, najdete v tématu [Azure Search výkon a optimalizace](search-performance-optimization.md).

Vrstvy optimalizované pro úložiště jsou užitečné pro úlohy s velkým objemem dat, které podporují komplexnější úložiště indexů pro dobu, kdy jsou požadavky na latenci dotazů méně důležité. Stále byste měli používat další repliky pro vyrovnávání zatížení a další oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

**Smlouvy o úrovni služeb**

Funkce bezplatné úrovně a verze Preview neposkytují [smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro všechny Fakturovatelné úrovně se SLA projeví, když zřizujete dostatečnou redundanci pro vaši službu. Pro dotaz (čtení) SLA musíte mít dvě nebo víc replik. Musíte mít tři nebo víc replik pro dotazování a indexování (pro čtení i zápis) SLA. Počet oddílů nemá vliv na SLA.

## <a name="tips-for-tier-evaluation"></a>Tipy pro vyhodnocování vrstev

+ Naučte se vytvářet efektivní indexy a zjistit, které metody aktualizace mají nejnižší dopad. K získání přehledu o aktivitě dotazů použijte [vyhledávání analýz provozu](search-traffic-analytics.md) .

+ Umožňuje, aby metriky sestavily dotazy a shromažďovat data o vzorech používání (dotazy během pracovní doby, indexování v době mimo špičku). Tato data slouží k informování o rozhodnutích zajišťování služeb. I když to není praktické při hodinovém nebo denním tempo, můžete dynamicky upravovat oddíly a prostředky, aby se na svazcích dotazů vešly plánované změny. Můžete také přizpůsobit neplánované, ale trvalé změny, pokud úrovně dostatečně dostatečně dostatečně neopravňují k zajištění, aby bylo možné provádět akce.

+ Mějte na paměti, že jediným nevýhodoum zajišťováním je, že pokud jsou skutečné požadavky větší než vaše předpovědi, možná budete muset odtrhnout službu. Aby nedošlo k přerušení služeb, vytvořte novou službu ve stejném předplatném na vyšší úrovni a spusťte ji vedle sebe, dokud všechny aplikace a požadavky necílí na nový koncový bod.

## <a name="next-steps"></a>Další kroky

Začněte s bezplatnou úrovní a sestavte počáteční index pomocí podmnožiny vašich dat, abyste pochopili jeho charakteristiky. Struktura dat v Azure Search je obrácená struktura indexu. Velikost a složitost obráceného indexu se určují podle obsahu. Mějte na paměti, že vysoce redundantní obsah obvykle vede k menšímu indexu než vysoce nepravidelný obsah. Takže charakteristiky obsahu, a ne velikost datové sady, určují požadavky na úložiště indexu.

Až budete mít počáteční odhad velikosti indexu, [zřiďte službu Fakturovatelné služby](search-create-service-portal.md) na jedné z vrstev popsaných v tomto článku: Optimalizované pro Basic, Standard nebo Storage. Uvolněte veškerá umělá omezení velikosti dat a [znovu sestavte index](search-howto-reindex.md) tak, aby zahrnoval všechna data, která chcete prohledávat.

Podle potřeby [přidělte oddíly a repliky](search-capacity-planning.md) , abyste získali výkon a škálování, které požadujete.

Pokud máte výkon a kapacitu v pořádku, budete hotovi. V opačném případě znovu vytvořte vyhledávací službu na jiné úrovni, která bude lépe zarovnána podle vašich potřeb.

> [!NOTE]
> Pokud máte otázky, pošlete příspěvek na [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [kontaktujte podporu Azure](https://azure.microsoft.com/support/options/).
