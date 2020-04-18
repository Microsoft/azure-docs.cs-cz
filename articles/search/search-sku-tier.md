---
title: Výběr cenové úrovně nebo skladové položky
titleSuffix: Azure Cognitive Search
description: 'Azure Cognitive Search lze zřídit v těchto sku: Free, Basic a Standard a Standard je k dispozici v různých konfiguracích prostředků a úrovně kapacity.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: eb11a5cc2deef372ca91c23a8b9c82e17143c85b
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617714"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Výběr cenové úrovně pro Azure Cognitive Search

Když vytvoříte službu Azure Cognitive Search, [prostředek se vytvoří](search-create-service-portal.md) na cenové úrovni (nebo Skladové ku), která je pevná po dobu životnosti služby. Úrovně zahrnují free, basic, standard a storage optimized. Standard a storage Optimized jsou k dispozici s několika konfiguracemi a kapacitami.

Většina zákazníků začíná s úrovní Free, aby mohli službu vyhodnotit. Po vyhodnocení je běžné vytvořit druhou službu na jedné z vyšších úrovní pro vývoj a produkční nasazení.

## <a name="feature-availability-by-tier"></a>Dostupnost funkcí podle úrovně

Téměř každá funkce je k dispozici na každé úrovni, včetně Free, ale funkce nebo pracovní postup, který je náročný na prostředky nemusí fungovat dobře, pokud jí nedáte dostatečnou kapacitu. Například [obohacení ai](cognitive-search-concept-intro.md) má dlouhotrvající dovednosti, které časový čas na bezplatnou službu, pokud je malá datová sada.

Následující tabulka popisuje omezení funkcí souvisejících s vrstvou.

| Funkce | Omezení |
|---------|-------------|
| [Indexování](search-indexer-overview.md) | Indexery nejsou k dispozici na S3 HD. |
| [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) | Není k dispozici na úrovni Free. |

## <a name="tiers-skus"></a>Úrovně (SKU)

Úrovně jsou rozlišeny podle:

+ Množství indexů a indexerů, které můžete vytvořit
+ Velikost a rychlost příček (fyzické úložiště)

Vybraná úroveň určuje fakturovatelnou sazbu. Následující snímek obrazovky z portálu Azure zobrazuje dostupné úrovně minus ceny (které najdete na portálu a na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/). **Nejběžnější**úrovně jsou free , **Basic**a **Standard.**

**Free** vytvoří omezenou vyhledávací službu pro menší projekty, včetně rychlých startů a kurzů. Interně repliky a oddíly sdílené mezi více předplatitelů. Nelze škálovat bezplatnou službu nebo spustit významné úlohy.

**Základní** a **standardní** jsou nejčastěji používané fakturovatelné úrovně, přičemž výchozí je **standard.** S vyhrazenými prostředky pod vaší kontrolou můžete nasadit větší projekty, optimalizovat výkon a nastavit kapacitu.

![Cenové úrovně Azure Cognitive Search](media/search-sku-tier/tiers.png "Cenové úrovně Azure Cognitive Search")

Některé úrovně jsou optimalizovány pro určité typy práce. Například **Standard 3 S High Density (S3 HD)** je *hostitelský režim* pro S3, kde je základní hardware optimalizován pro velký počet menších indexů a je určen pro scénáře víceklientské. S3 HD má stejné napoplatné za jednotku jako S3, ale hardware je optimalizován pro rychlé čtení souborů na velkém počtu menších indexů.

**Úrovně optimalizované pro úložiště** nabízejí větší úložnou kapacitu za nižší cenu za TB než úrovně Standard. Primární kompromis je vyšší latence dotazu, které byste měli ověřit pro vaše konkrétní požadavky na aplikaci.  Další informace o aspektech výkonu této vrstvy naleznete v [tématu Aspekty výkonu a optimalizace](search-performance-optimization.md).

Další informace o různých úrovních najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/), v [článku Limity služby v Azure Cognitive Search](search-limits-quotas-capacity.md) a na stránce portálu při zřizování služby.

## <a name="billable-events"></a>Fakturovatelné události

Řešení postavené na Azure Cognitive Search může vzniknout náklady následujícími způsoby:

+ Pevné náklady na samotnou službu, spuštěné 24hodinová, při minimální konfiguraci (jeden oddíl a replika)
+ Přírůstkové náklady při škálování (přidání replik nebo oddílů)
+ Poplatky za šířku pásma (odchozí přenos dat) 
+ Kognitivní vyhledávání (připojení služeb Cognitive Services pro obohacení umělou iniciátoru nebo použití úložiště Azure pro úložiště znalostí)

### <a name="service-costs"></a>Servisní náklady

Na rozdíl od virtuálních počítačů nebo jiných prostředků, které lze "pozastavuje", aby se zabránilo poplatků, služba Azure Cognitive Search je vždy k dispozici na hardware vyhrazený pro vaše výhradní použití. Jako takové vytvoření služby je fakturovatelná událost, která se spustí při vytvoření služby a končí při odstranění služby. 

Minimální poplatek je první vyhledávací jednotka (jedna replika x jeden oddíl) s fakturovatelnou sazbou. Toto minimum je pevně stanoveno po dobu životnosti služby, protože služba nemůže být spuštěna na nic menšího než tato konfigurace. Nad minimální, můžete přidat repliky a oddíly nezávisle na sobě. Přírůstkové zvýšení kapacity prostřednictvím replik a oddílů zvýší váš účet na základě následujícího vzorce: [(repliky x oddíly x sazba)](#search-units), kde sazba, která je účtována, závisí na cenové úrovni, kterou vyberete.

Při odhadu nákladů na řešení vyhledávání mějte na paměti, že ceny a kapacita nejsou lineární. (Zdvojnásobení kapacity více než zdvojnásobuje náklady.) Příklad fungování vzorce naleznete v tématu [Jak přidělit repliky a oddíly](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Poplatky za šířku pásma

Použití [indexerů Azure Cognitive Search](search-indexer-overview.md) může ovlivnit fakturaci v závislosti na umístění vašich služeb. Poplatky za odchozí přenos dat můžete zcela eliminovat, pokud vytvoříte službu Azure Cognitive Search ve stejné oblasti jako vaše data. Zde jsou některé informace ze [stránky ceny šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft neúčtuje žádné příchozí data do žádné služby v Azure ani za odchozí data z Azure Cognitive Search.
+ V řešeních s více službami se neúčtují žádné poplatky za data přecházející drát, pokud jsou všechny služby ve stejné oblasti.

Poplatky se vztahují na odchozí data, pokud jsou služby v různých oblastech. Tyto poplatky ve skutečnosti nejsou součástí vašeho účtu Azure Cognitive Search. Jsou zde zmíněny, protože pokud používáte data nebo indexery obohacené umělou ai k vytahování dat z různých oblastí, uvidíte náklady, které se projeví v celkovém účtu.

### <a name="ai-enrichment-with-cognitive-services"></a>Obohacení umělou a vybavenou umělou ivybavencou službou

Pro [obohacení umělou aicí](cognitive-search-concept-intro.md)byste měli naplánovat [připojení fakturovatelného prostředku Azure Cognitive Services](cognitive-search-attach-cognitive-services.md)ve stejné oblasti jako Azure Cognitive Search na cenové úrovni S0 pro zpracování průběžných plateb. S připojením služeb Cognitive Services nejsou spojeny žádné fixní náklady. Platíte pouze za zpracování, které potřebujete.

| Operace | Dopad na fakturaci |
|-----------|----------------|
| Zalomení dokumentů, extrakce textu | Free |
| Praskání dokumentů, extrakce obrazu | Fakturováno podle počtu obrázků extrahovaných z vašich dokumentů. V [konfiguraci indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) **imageAction** je parametr, který aktivuje extrakci obrazu. Pokud **imageAction** je nastavena na "none" (výchozí), nebudou účtovány za extrakci obrazu. Rychlost extrakce image je zdokumentována na stránce [s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/search/) pro Azure Cognitive Search.|
| [Předdefinované kognitivní dovednosti](cognitive-search-predefined-skills.md) | Fakturováno stejným tempem, jako kdybyste úkol provedli přímo pomocí služeb Cognitive Services. |
| Vlastní dovednosti | Vlastní dovednost je funkce, které poskytujete. Náklady na použití vlastní dovednosti závisí zcela na tom, zda vlastní kód volá jiné služby účtované podle objemu dat. |

[Funkce přírůstkového obohacení (náhledu)](cognitive-search-incremental-indexing-conceptual.md) umožňuje poskytnout mezipaměť, která umožňuje indexeru efektivnější při spouštění pouze kognitivních dovedností, které jsou nezbytné, pokud v budoucnu změníte své dovednosti, což vám ušetří čas a peníze.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fakturační vzorec (R x P = SU)

Nejdůležitější koncept fakturace, který je třeba pochopit pro operace Azure Cognitive Search, je *vyhledávací jednotka* (SU). Vzhledem k tomu, že Azure Cognitive Search závisí na repliky a oddíly pro indexování a dotazy, nemá smysl účtovat pouze jeden nebo druhý. Místo toho je fakturace založena na složené mj.

SU je produktem *replik a* *oddílů používaných* službou: **(R x P = SU)**.

Každá služba začíná s jedním SU (jedna replika vynásobená jedním oddílem) jako minimum. Maximální hodnota pro všechny služby je 36 su. Toto maximum lze dosáhnout několika způsoby: 6 oddílů x 6 replik nebo 3 oddíly x 12 repliky, například. Je běžné používat méně než celkovou kapacitu (například 3 repliky, 3-partition služba fakturovaná jako 9 su). Platné kombinace naleznete v grafu [kombinace oddílů a replik.](search-capacity-planning.md#chart)

Fakturační sazba je hodinová za SU. Každá úroveň má postupně vyšší sazbu. Vyšší úrovně jsou dodávány s většími a rychlejšími oddíly, což přispívá k celkově vyšší hodinové sazbě pro tuto úroveň. Sazby pro každou úroveň můžete zobrazit na stránce [s podrobnostmi o cenách.](https://azure.microsoft.com/pricing/details/search/)

Většina zákazníků přináší jen část celkové kapacity online a zbytek drží v rezervě. Pro fakturaci, počet oddílů a replik, které přenesete do režimu online, vypočítaný podle vzorce SU, určuje, co platíte po hodinách.

## <a name="how-to-manage-costs"></a>Jak spravovat náklady

Následující návrhy vám mohou pomoci udržet náklady na minimu:

+ Vytvořte všechny prostředky ve stejné oblasti nebo v co nejmenším počtem oblastí, abyste minimalizovali nebo eliminovali poplatky za šířku pásma.

+ Konsolidujte všechny služby do jedné skupiny prostředků, jako je Azure Cognitive Search, Cognitive Services a všechny ostatní služby Azure používané ve vašem řešení. Na webu Azure Portal najděte skupinu prostředků a pomocí příkazů **řízení nákladů** najdete pro přehled o skutečných a plánovaných výdajích.

+ Zvažte Azure Web App pro vaši front-endovou aplikaci tak, aby požadavky a odpovědi zůstaly v rámci hranice datového centra.

+ Škálovat pro operace náročné na prostředky, jako je indexování a potom se seřazuje směrem dolů pro úlohy pravidelných dotazů. Začněte s minimální konfigurací pro Azure Cognitive Search (jeden SU složený z jednoho oddílu a jedné repliky) a pak sledujte aktivitu uživatelů k identifikaci vzorců využití, které by naznačovaly potřebu větší kapacity. Pokud existuje předvídatelný vzor, můžete být schopni synchronizovat škálování s aktivitou (budete muset napsat kód pro automatizaci tohoto).

Navštivte stránku [Fakturace a správa nákladů,](https://docs.microsoft.com/azure/billing/billing-getting-started) kde najdete integrované nástroje a funkce související s výdaji.

Dočasné vypnutí vyhledávací služby není možné. Vyhrazené prostředky jsou vždy funkční, přidělené pro vaše výhradní použití po celou dobu životnosti vaší služby. Odstranění služby je trvalé a také odstraní přidružená data.

Pokud jde o samotnou službu, jediným způsobem, jak snížit účet, je snížit repliky a oddíly na úroveň, která stále poskytuje přijatelný výkon a [dodržování sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/), nebo vytvořit službu na nižší úrovni (hodinové sazby S1 jsou nižší než sazby S2 nebo S3). Za předpokladu, že zřídíte službu na dolním konci projekce zatížení, pokud přerostete službu, můžete vytvořit druhou větší vrstvenou službu, znovu sestavit indexy na druhé službě a potom odstranit první.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak vyhodnotit požadavky na kapacitu

V Azure Cognitive Search kapacita je strukturována jako *repliky* a *oddíly*.

+ Repliky jsou instance vyhledávací služby. Každá replika je hostitelem jedné kopie indexu s vyrovnáváním zatížení. Například služba se šesti replikami má šest kopií každého indexu načteného ve službě.

+ Oddíly ukládají indexy a automaticky rozdělují prohledávatelná data. Dva oddíly rozdělit index na polovinu, tři oddíly rozdělit na třetiny a tak dále. Pokud jde o kapacitu, *velikost oddílu* je primární rozlišovací funkce mezi vrstvami.

> [!NOTE]
> Všechny úrovně standard a storage Optimized podporují [flexibilní kombinace replik a oddílů,](search-capacity-planning.md#chart) takže můžete optimalizovat systém pro rychlost nebo [úložiště](search-performance-optimization.md) změnou váhy. Úroveň Basic nabízí až tři repliky pro vysokou dostupnost, ale má pouze jeden oddíl. Volné úrovně neposkytují vyhrazené prostředky: výpočetní prostředky jsou sdíleny více předplatiteli.

### <a name="evaluating-capacity"></a>Vyhodnocení kapacity

Kapacita a náklady na provoz služby jdou ruku v ruce. Úrovně ukládají omezení na dvě úrovně: úložiště a prostředky. Měli byste přemýšlet o obou, protože podle toho, co limit dosáhnete jako první, je efektivní limit.

Obchodní požadavky obvykle určují počet indexů, které budete potřebovat. Například můžete potřebovat globální index pro velké úložiště dokumentů. Nebo můžete potřebovat více indexů na základě oblasti, aplikace nebo obchodní výklenek.

Chcete-li určit velikost indexu, je třeba [vytvořit jeden](search-create-index-portal.md). Jeho velikost bude založena na importovaných datech a konfiguraci indexu, například zda povolíte návrhy, filtrování a řazení. Další informace o dopadu konfigurace na velikost naleznete [v tématu Vytvoření základního indexu ](search-what-is-an-index.md).

Pro fulltextové vyhledávání je primární datová struktura [invertovaná indexová](https://en.wikipedia.org/wiki/Inverted_index) struktura, která má jiné charakteristiky než zdrojová data. U invertovaného indexu jsou velikost a složitost určeny obsahem, nikoli nutně množstvím dat, která do něj podáváte. Velký zdroj dat s vysokou redundancí může mít za následek menší index než menší datová sada, která obsahuje vysoce variabilní obsah. Proto je zřídka možné odvodit velikost indexu na základě velikosti původní datové sady.

> [!NOTE] 
> I když odhad budoucích potřeb indexů a úložiště se může cítit jako dohady, stojí za to. Pokud se kapacita úrovně ukáže jako příliš nízká, budete muset zřídit novou službu na vyšší úrovni a pak [znovu načíst indexy](search-howto-reindex.md). Neexistuje žádný upgrade na místě služby z jedné skladové položky do druhé.
>

### <a name="estimate-with-the-free-tier"></a>Odhad s úrovní Free

Jedním z přístupů pro odhad kapacity je začít s free vrstvou. Nezapomeňte, že služba Free nabízí až tři indexy, 50 MB úložiště a 2 minuty doby indexování. Odhad předpokládané velikosti indexu s těmito omezeními může být náročný, ale jedná se o tyto kroky:

+ [Vytvořte bezplatnou službu](search-create-service-portal.md).
+ Připravte malou reprezentativní datovou sadu.
+ [Vytvořte počáteční index na portálu](search-create-index-portal.md) a poznamenejte si jeho velikost. Funkce a atributy mají vliv na úložiště. Například přidání sugestium (dotazy typu vyhledávání) zvýší požadavky na úložiště. Pomocí stejné sady dat můžete zkusit vytvořit více verzí indexu s různými atributy v každém poli, abyste zjistili, jak se liší požadavky na úložiště. Další informace naleznete [v tématu "Důsledky úložiště" v tématu Vytvoření základního indexu](search-what-is-an-index.md#index-size).

S hrubým odhadem v ruce můžete tuto částku zdvojnásobit do rozpočtu pro dva indexy (vývoj a výroba) a podle toho zvolit úroveň.

### <a name="estimate-with-a-billable-tier"></a>Odhad s fakturovatelnou vrstvou

Vyhrazené prostředky mohou pojmout větší vzorkování a zpracování časy pro realističtější odhady množství indexu, velikost a objemy dotazů během vývoje. Někteří zákazníci skočit přímo s fakturovatelnou vrstvou a pak přehodnotit jako vývojový projekt zraje.

1. [Zkontrolujte limity služeb na každé úrovni](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) a zjistěte, zda nižší vrstvy mohou podporovat počet indexů, které potřebujete. Napříč úrovněmi Basic, S1 a S2 jsou limity indexu 15, 50 a 200. Úroveň Optimalizovaná pro úložiště má limit 10 indexů, protože je navržena pro podporu nízkého počtu velmi velkých indexů.

1. [Vytvoření služby na fakturovatelné úrovni](search-create-service-portal.md):

    + Začněte nízká, na základní nebo S1, pokud si nejste jisti, o předpokládané zatížení.
    + Začněte vysoko, na S2 nebo dokonce S3, pokud víte, že budete mít rozsáhlé indexování a načítání dotazů.
    + Začněte s optimalizovaným úložištěm na L1 nebo L2, pokud indexujete velké množství dat a zatížení dotazů je relativně nízké, jako u interní obchodní aplikace.

1. [Vytvořte počáteční index](search-create-index-portal.md) k určení, jak se zdrojová data přetaví do indexu. Toto je jediný způsob, jak odhadnout velikost indexu.

1. [Monitorujte úložiště, omezení služeb, objem dotazů a latenci](search-monitor-usage.md) na portálu. Portál zobrazuje dotazy za sekundu, omezené dotazy a latenci vyhledávání. Všechny tyto hodnoty vám mohou pomoci rozhodnout, zda jste vybrali správnou úroveň. 

Číslo a velikost indexu jsou stejně důležité pro vaši analýzu. Důvodem je, že maximální limity jsou dosaženy prostřednictvím úplné využití úložiště (oddíly) nebo maximální limity na prostředky (indexy, indexery a tak dále), podle toho, co nastane dříve. Portál vám pomůže sledovat obojí a zobrazuje aktuální využití a maximální limity vedle sebe na stránce Přehled.

> [!NOTE]
> Požadavky na úložiště lze nafouknout, pokud dokumenty obsahují nadbytečná data. V ideálním případě dokumenty obsahují pouze data, která potřebujete pro vyhledávání. Binární data nelze prohledávat a měla by být uložena samostatně (možná v tabulce Azure nebo úložiště objektů blob). Pole by pak měla být přidána do indexu pro uložení url odkaz na externí data. Maximální velikost jednotlivého dokumentu je 16 MB (nebo méně, pokud v jednom požadavku nahráváte více dokumentů). Další informace najdete [v tématu Omezení služeb v Azure Cognitive Search](search-limits-quotas-capacity.md).
>

**Důležité informace o objemu dotazu**

Dotazy za sekundu (QPS) je důležitá metrika během ladění výkonu, ale obecně je to pouze aspekt vrstvy, pokud očekáváte vysoký objem dotazu na začátku.

Úrovně Standard mohou poskytnout rovnováhu replik a oddílů. Můžete zvýšit obrat dotazu přidáním replikpro vyrovnávání zatížení nebo přidat oddíly pro paralelní zpracování. Potom můžete optimalizovat pro výkon po zřízení služby.

Pokud očekáváte vysoké trvalé objemy dotazů od začátku, měli byste zvážit vyšší úrovně Standard, podporované výkonnějším hardwarem. Potom můžete převést oddíly a repliky do režimu offline nebo dokonce přepnout na službu nižší vrstvy, pokud tyto svazky dotazů nenastanou. Další informace o tom, jak vypočítat propustnost dotazů, najdete [v tématu Výkon a optimalizace azure kognitivního vyhledávání](search-performance-optimization.md).

Úrovně Optimalizované pro úložiště jsou užitečné pro velké datové úlohy, které podporují více celkového dostupného úložiště indexu, když jsou požadavky na latenci dotazu méně důležité. Stále byste měli použít další repliky pro vyrovnávání zatížení a další oddíly pro paralelní zpracování. Potom můžete optimalizovat pro výkon po zřízení služby.

**Smlouvy o úrovni služeb**

Funkce úrovně Free a náhledne poskytují [smlouvy o úrovni služeb (SLA).](https://azure.microsoft.com/support/legal/sla/search/v1_0/) Pro všechny fakturovatelné úrovně se sla projeví, když pro vaši službu zřídíte dostatečnou redundanci. Musíte mít dvě nebo více replik pro dotaz (číst) SLA. Musíte mít tři nebo více replik pro dotaz a indexování (čtení a zápis) SLA. Počet oddílů nemá vliv na sla.

## <a name="tips-for-tier-evaluation"></a>Tipy pro hodnocení úrovně

+ Povolit metriky vytvářet kolem dotazů a shromažďovat data o vzorcích využití (dotazy během pracovní doby, indexování mimo špičku). Tyto údaje slouží k informování rozhodnutí o zřizování služeb. I když to není praktické na hodinové nebo denní kadence, můžete dynamicky upravit oddíly a prostředky tak, aby vyhovovaly plánované změny ve svazcích dotazů. Můžete také vyhovět neplánovaným, ale trvalým změnám, pokud úrovně vydrží dostatečně dlouho, aby ospravedlnily přijetí opatření.

+ Pamatujte si, že jedinou nevýhodou podle zřizování je, že budete muset strhnout službu, pokud jsou větší skutečné požadavky než vaše předpovědi. Chcete-li se vyhnout přerušení služby, vytvoříte novou službu na vyšší úrovni a spusťte ji vedle sebe, dokud všechny aplikace a požadavky nezacílí na nový koncový bod.

## <a name="next-steps"></a>Další kroky

Začněte s free vrstvou a vytvořte počáteční index pomocí podmnožiny dat, abyste pochopili její charakteristiky. Datová struktura v Azure Cognitive Search je obrácená struktura indexu. Velikost a složitost invertovaného indexu je určena obsahem. Nezapomeňte, že vysoce redundantní obsah má tendenci mít za následek menší index než vysoce nepravidelný obsah. Takže charakteristiky obsahu spíše než velikost datové sady určit požadavky na úložiště indexu.

Po počátečníodhad velikosti indexu [zřídit fakturovatelné služby](search-create-service-portal.md) na jedné z úrovní popsaných v tomto článku: Základní, Standardní nebo Úložiště optimalizované. Uvolněte všechna umělá omezení pro velikost dat a [znovu vytvořte index](search-howto-reindex.md) tak, aby zahrnoval všechna data, která chcete prohledávat.

[Přidělte oddíly a repliky](search-capacity-planning.md) podle potřeby získat výkon a škálování, které požadujete.

Pokud výkon a kapacita jsou v pořádku, jste hotovi. V opačném případě znovu vytvořte vyhledávací službu na jiné úrovni, která je více v souladu s vašimi potřebami.

> [!NOTE]
> Pokud máte dotazy, pošlete příspěvek na [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [kontaktujte podporu Azure](https://azure.microsoft.com/support/options/).
