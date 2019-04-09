---
title: Vyberte cenové úrovně nebo SKU pro službu Azure Search – Azure Search
description: 'Služba Azure Search je možné zřídit za tyto skladové položky: Free, Basic a Standard, ve kterém Standard je k dispozici v různých konfigurace prostředků a kapacity úrovně.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: da8c8adacfead598a8dec6280cf3518fb7b31f49
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270936"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Zvolte cenovou úroveň pro službu Azure Search

Ve službě Azure Search [je prostředek vytvořený](search-create-service-portal.md) na cenové úrovně nebo SKU, který je pevně po dobu životnosti služby. Dostupné úrovně **Free**, **základní**, **standardní**, nebo **optimalizované pro úložiště**.  **Standardní** a **optimalizované pro úložiště** jsou k dispozici v několika konfiguracích a kapacity. 

Většina zákazníků začíná **Free** vrstvy pro vyhodnocení a potom jim dodejte na jednu z placených vyšší úrovně pro vývoj a provoz nasazení. Můžete na dokončení všech rychlých startů a kurzů **Free** úrovně, včetně těch, které pro náročné kognitivního vyhledávání.

> [!NOTE]
> Úrovně optimalizované pro úložiště služby jsou aktuálně k dispozici ve verzi preview za zlevněné ceny pro účely testování a experimentování s cílem shromažďování zpětné vazby. Chcete zjistit koncové ceny oznámíme později při těchto úrovních jsou obecně dostupné. Nedoporučujeme tyto úrovně používají, aplikacích v produkčním prostředí.

Vrstev odrážejí charakteristiky hardwaru, hostování služby (spíše než funkce) a jsou rozlišené pomocí:

+ Počet indexů, které lze vytvořit
+ Velikost a rychlost oddílů (fyzické úložiště)

Přestože všechny úrovně, včetně **Free** vrstvy, obecně nabízí paritu funkcí, větší úlohy může diktovat požadavky pro vyšší úrovně. Například [indexování AI pomocí služeb Cognitive Services](cognitive-search-concept-intro.md) má dlouhotrvající dovednosti tento časový limit na bezplatné služby není-li být malé datové sady se stane s.

> [!NOTE] 
> Výjimka, která má paritu funkcí je [indexery](search-indexer-overview.md), které nejsou k dispozici na S3HD.
>

V rámci úrovně, je možné [upravit repliky a oddílu prostředků](search-capacity-planning.md) zvýšení nebo snížení škálování. Můžete začít s jednou nebo dvěma jednotlivých a dočasně zvýšit váš výpočetní výkon pro náročné úlohy indexování. Schopnost optimalizovat úrovně prostředků v rámci úrovně zvyšuje flexibilitu, ale také mírně komplikuje analýzy. Budete muset experimentovat, uvidíte, jestli nižší úrovně s vyšší prostředky/replik nabízí lepší výkon než vyšší úroveň s nižší prostředky a hodnotu. Další informace o kdy a proč by upravit kapacitu, najdete v článku [aspekty týkající se výkonu a optimalizace](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Úrovně pro službu Azure Search

V následující tabulce jsou uvedeny dostupné úrovně. Zahrnout další zdroje informací úroveň [stránce s cenami](https://azure.microsoft.com/pricing/details/search/), [limity služby a data](search-limits-quotas-capacity.md)a stránka portálu při zřizování služby.

|Úroveň | Kapacita |
|-----|-------------|
|Free | Sdílenou s jinými odběrateli. Neškálovatelná, omezená na 3 indexy a přes 50 MB úložiště. |
|Basic | Vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku. Jeden oddíl 2 GB a až tři repliky. |
|Standard 1 (S1) | Z S1 na nahoru vyhrazených počítačů s další kapacitou úložiště a zpracování na všech úrovních. Velikost oddílu je 25 GB na oddíl (maximálně 300 GB na službu) pro S1. |
|Standard 2 (S2) | Podobně jako na S1, ale s 100 GB/oddíly (max 1,2 TB na službu) |
|Standard 3 (S3) | 200 GB na oddíl (max. 2,4 TB na službu) |
|Standardní 3 Vysoká hustota (S3 – HD, High Density) | S vysokou hustotou je *hostující režim* pro S3. Základního hardwaru je optimalizovaná pro velký počet menší indexy, které jsou určené pro scénáře víceklientské architektury. S3 HD má stejné poplatky za jednotky S3, ale hardware je optimalizovaný pro rychlejší čtení na spoustu menších indexy.|
|Optimalizované pro úložiště 1 (L1) | 1 TB na oddíl (max. 12 TB na službu) |
|Úložiště optimalizované 2 (L2) | 2 TB na oddíl (max. 24 TB na službu) |

> [!NOTE] 
> Úrovně optimalizované pro úložiště nabízejí větší kapacitu úložiště za nižší cenu za TB než úrovně Standard. Primární kompromis je vyšší latence dotazu, který by měl ověřit pro vaše konkrétní aplikační požadavky.  Další informace o aspektech týkajících se výkonu této vrstvy, naleznete v tématu [aspekty týkající se výkonu a optimalizace](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Jak funguje fakturace

Ve službě Azure Search existují tři způsoby, jak vynakládá ve službě Azure Search a existují proměnlivé a dlouhodobý komponent. Této části se probírají zase na jednotlivé součásti fakturace.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Náklady na službu Core (pevné a proměnné)

Pro samotnou službu minimální sazba je první částí vyhledávání (1 repliky x 1 oddílu) a tato velikost je konstantní po dobu životnosti služby, protože službu nelze spustit žádnou menší než tato konfigurace. 

Na následujícím snímku obrazovky za cenu jednotky je označen pro Free a Basic, S1 (S2, S3, L1 a L2 nezobrazují). Pokud jste vytvořili **základní**, **standardní**, nebo **optimalizované pro úložiště** služby, váš měsíční náklady by průměrná hodnota, která se zobrazí pro *cena-1*a *cena 2* v uvedeném pořadí. Náklady na jednotku zvýší pro každou vrstvu protože je větší na jednotlivých úrovních po sobě jdoucích výpočetní výkon a výpočetní kapacitu.

![Za cenu jednotky](./media/search-sku-tier/per-unit-pricing.png "za cenu jednotky")

Další repliky a oddíly, které jsou doplněk k počáteční náklady. Vyhledávací služba vyžaduje repliky a oddíl jednu roli od každého je minimální požadavky na konfiguraci. Nad rámec minimální je přidat repliky a oddíly, nezávisle na sobě. Například můžete přidat pouze repliky nebo pouze oddíly. 

Další repliky a oddíly, které se účtují na základě [vzorec](#search-units). Náklady na nejsou lineární (zvýší kapacitu více než zdvojnásobí náklady). Příklad toho, jak vzorec funguje, najdete v části ["Postup přidělení repliky a oddíly"](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="2-data-egress-charges-during-indexing"></a>2. Poplatky za odchozí přenos dat během indexování

Použití [indexerů Azure Search](search-indexer-overview.md) může vést k fakturaci dopad závislosti, kde jsou umístěny služby. Poplatky za odchozí přenos dat můžete eliminovat úplně, pokud vytvoříte službu Azure Search ve stejné oblasti jako vaše data.

+ Žádné poplatky za všechna příchozí data na jakoukoli službu v Azure.

+ Žádné poplatky za všechny odchozí data z Azure Search.

+ Žádné poplatky za data nebo soubory odchozí z SQL Database, Cosmos, Blob storage (vstupní do služby Azure Search), pokud jsou všechny služby ve stejné oblasti.

+ Poplatky za odchozí data nebo soubory Pokud storage a Azure Search v různých oblastech.

Při směrování dat v různých oblastech Azure, zobrazí se poplatky za šířku pásma na faktuře je uvedena pro tyto prostředky. Tyto poplatky, které nejsou součástí vyúčtování Azure Search, ale jsou vzhledem k tomu, že pokud o přijetí změn dat nebo souborů přenosu pomocí indexerů, zobrazí tento poplatek je ve vyúčtování celkové tady uvedené.

Pokud nepoužíváte indexery, nejsou žádné poplatky za šířku pásma. 

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. AI obohacené indexování s využitím služeb Cognitive Services

Pro [indexování AI pomocí služeb Cognitive Services](cognitive-search-concept-intro.md) pouze extrakce image během hádání dokumentu se účtuje na základě počtu imagí, které jsou extrahovány z vašich dokumentů. Extrakce textu je aktuálně zdarma. Další obohacení, jako je zpracování přirozeného jazyka, jsou založeny na [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) se účtují podle prostředku služeb Cognitive Services. Obohacení se účtují za stejnou sazbu jako kdyby jste provedli úloh přímo pomocí služeb Cognitive Services.

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>Fakturace podle jednotek vyhledávání

Pro operace Azure Search je nejdůležitější fakturační koncept pochopit *jednotka služby search* (SU). Protože Azure Search závisí na repliky a oddíly pro indexování a dotazy, nemá smysl pro fakturaci podle právě jeden z nich. Místo toho fakturace vychází složeného obou. 

SU je produkt *repliky* a *oddíly* používané službou: **`(R X P = SU)`**

Každá služba začíná u jedné SU (jednu repliku a jeden oddíl) jako minimální. Maximální počet pro libovolnou službu je 36 su, které můžete dosáhnout různými způsoby: 6 oddíly x 6 repliky, nebo replik pro 3 oddíly x 12 pár. Je běžné použití nižší, než celkové kapacity. Například repliky 3, 3 oddíly služby, účtovat jako 9 su. Můžete zkontrolovat [tento graf](search-capacity-planning.md#chart) zobrazíte platné kombinace na první pohled.

Fakturační sazba je **po hodinách za SU**, kde každá úroveň s postupně vyšší sazba. Vyšší úrovně součástí větší a zrychlit oddíly přispívající k celkové vyšší hodinové sazby za tuto úroveň. Sazby za pro každou vrstvu můžete najít na [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/). 

Většina zákazníků přinést jen část celkové kapacity online, uchovávající zbývající ve fondu. Z hlediska fakturace, je počet oddílů a replik uvést online, vypočítané pomocí vzorce SU, která určuje, co skutečně platíte po hodinách.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Fakturace pro extrahování bitové kopie v kognitivního vyhledávání

Když se extrakce obrázků ze souborů v kognitivního vyhledávání, indexování kanálu, bude vám účtována pro tuto operaci je ve vyúčtování Azure Search. Je parametr, který aktivuje image extrakce **imageAction** v [konfigurace indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Pokud **imageAction** je nastavena na hodnotu none (výchozí), neúčtují žádné poplatky pro extrakci bitové kopie.

Ceny se mohou změnit, ale je vždy popsané na [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/) stránku pro službu Azure Search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Fakturace za integrované znalosti kognitivního vyhledávání

Při nastavování kanál obohacování jakékoli [integrované znalosti](cognitive-search-predefined-skills.md) použít v kanálu jsou založeny na modely strojového učení. Tyto modely jsou k dispozici ve službách Cognitive Services. Použití těchto modelech během indexování se účtuje stejná sazba jako v případě, že můžete požadovaly prostředek přímo.

Předpokládejme například, kanál, který se skládá z optické rozpoznávání znaků (OCR) proti naskenovaný soubory JPEG, kde je výsledný text odesílají do indexu Azure Search pro volného tvaru vyhledávací dotazy. Váš kanál indexování bude zahrnovat indexer s [OCR dovednosti](cognitive-search-skill-ocr.md), a že by bylo dovednosti [připojené k prostředku služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Při spuštění indexeru se zobrazují na faktuře Cognitive prostředků pro spouštění technologie OCR poplatky.

## <a name="tips-for-reducing-costs"></a>Tipy pro snížení nákladů

Nelze vypnout službu na nižší faktury. Provozní 24 7 přidělené pro vaše výhradní použití po dobu životnosti vaší služby jsou vyhrazené prostředky. Jediný způsob, jak snížit účet se snížením repliky a oddíly, které na nízké úrovni, která stále poskytuje přijatelný výkon a [dodržování předpisů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Jedna úroveň mu pomáhá snižovat náklady je výběr úrovně s nižší hodinové sazby. S1 hodinové sazby jsou nižší než S2 nebo S3. Za předpokladu, že si zřídíte službu zaměřené na dolní hranice vaší zatížení projekce, je-li velký růst služby, může vytvořit druhý větší vrstvené službu, opětovné sestavení indexů na druhý služby a odstraňte první z nich. 

Pokud jste provedli plánování kapacity na místních serverech, víte, že je společné pro "koupit" tak, aby bylo možné zpracovat očekávané růstu. Ale s cloudovou službou, vám může pokračovat další úspory nákladů agresivně vzhledem k tomu, že nejsou uzamčeny konkrétní nákupu. Můžete vždy přejdete na službu vyšší vrstvenou pokud tu není dostatečná.

### <a name="capacity-drill-down"></a>Kapacita procházení

Ve službě Azure Search má strukturu kapacity *repliky* a *oddíly*. 

+ Repliky jsou instancemi služby vyhledávání, ve kterém každá replika hostuje jednu kopii tohoto indexu s vyrovnáváním zatížení. Služba s 6 replik má například 6 kopií každý index načíst služby. 

+ Oddíly ukládat indexy a automaticky rozdělí prohledávatelná data: dva oddíly rozdělit třetiny indexu v polovině, tři oddíly a tak dále. Z hlediska kapacity *oddílu velikost* je primární odlišující funkce napříč úrovněmi.

> [!NOTE]
> Všechny **standardní** a **optimalizované pro úložiště** úrovní podpory [flexibilní kombinace repliky a oddíly](search-capacity-planning.md#chart) , abyste mohli [váha systému pro rychlost nebo úložiště](search-performance-optimization.md) změnou zůstatek na účtu. **Základní** nabízí tři repliky vysokou dostupnost, ale má pouze jeden oddíl. **Bezplatné** úrovně se neposkytuje vyhrazené prostředky: výpočetní prostředky se sdílí několik předplatitelů.

### <a name="more-about-service-limits"></a>Další informace o omezení služby

Služby prostředky hostitele, jako jsou indexy, indexery a tak dále. Každá úroveň má [omezení služby](search-limits-quotas-capacity.md) na množství prostředků můžete vytvořit. Limit počtu indexy (a dalších objektů) v důsledku toho je druhá odlišující funkce napříč úrovněmi. Při kontrole jednotlivé možnosti na portálu, mějte na paměti omezený počet indexů. Jiné prostředky, jako jsou indexování zdrojů dat a dovednosti, je propojen s limity indexu.

## <a name="consumption-patterns"></a>Vzory využití

Většina zákazníků začíná **Free** služby, které se zachovat po neomezenou dobu a pak vyberte jednu z **standardní** nebo **optimalizované pro úložiště** úrovně pro vývoj závažné nebo úlohy v produkčním prostředí. 

![Služba Azure search úrovně](./media/search-sku-tier/tiers.png "cenových úrovní Azure search")

Na obou koncích **základní** a **S3 HD, High Density** pro jejich vzorce spotřeby důležitý, ale netypických neexistují. **Základní** je pro malé produkční: nabízí smlouvu SLA, vyhrazené prostředky, vysokou dostupnost, ale středně velká úložiště, přičemž navýšení kapacity na celkem 2 GB. Tato úroveň byla navržena pro zákazníky, kteří který konzistentně pod využívaných dostupné kapacity. Na konci úplně **S3 HD, High Density** je pro úlohy typické pro nezávislé výrobce softwaru, partnery, [víceklientské řešení](search-modeling-multitenant-saas-applications.md), nebo žádnou konfiguraci volání pro velký počet malých indexy. Často je samozřejmé při **základní** nebo **S3 HD, High Density** úroveň je správné přizpůsobit, ale pokud chcete, aby potvrzení můžete vytvořit [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [obraťte se na Azure Podpora](https://azure.microsoft.com/support/options/) další pokyny.

Posunutí fokus pro běžně používané úrovně standard **S1 na S3** jsou průběh zvýšit limity kapacity s důležitý body na velikost oddílu a maximální hodnoty na čísla indexů, indexerů a corollary prostředků:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Velikost oddílu|  25 GB | 100 GB | 200 GB |  |  |  |  |
| omezení indexu a indexeru| 50 | 200 | 200 |  |  |  |  |

**S1** je běžné volbou až vyhrazených prostředků a několik oddílů budou nezbytné. S oddíly 25 GB až 12 oddílů, omezení za jednoúčelovou **S1** je celkem 300 GB, pokud maximalizovat oddíly přes repliky (naleznete v tématu [přidělit oddílů a replik](search-capacity-planning.md#chart) pro více s vyrovnáváním sestavení.)

Stránky portálu a cenové zaměření na velikost oddílu a úložiště, ale pro každou vrstvu všechny výpočetní možnosti (kapacita disku, procesory rychlost) lineárně roste s cenou. **S2** replika je rychlejší než **S1**, a **S3** je rychlejší než **S2**. **S3** úrovně přerušit obecně lineární vzorku ceny za výpočetní prostředky se neúměrně rychlejší vstupně-výstupních operací. Pokud očekáváte, že vstupně-výstupní operace jako kritický bod, **S3** poskytuje mnohem více vstupně-výstupních operací než nižší úrovně.

**S3** a **S3 HD, High Density** jsou identické vysoké kapacity infrastruktury, ale každý jeden dosáhne svého maximálního limitu různými způsoby. **S3** cílí na menší počet velmi velké indexů. V důsledku toho jeho maximální limit je vázán na zdroj (pro každou službu 2,4 TB). **S3 HD, High Density** cílí na větší počet indexů velmi malý. Za 1000 indexů **S3 HD, High Density** dosaženo omezení v podobě omezení indexu. Pokud jste **S3 HD, High Density** zákazníka, který vyžaduje více než 1000 indexů, obraťte se na Microsoft Support informace o tom, aby bylo možné pokračovat.

> [!NOTE]
> Dříve limity pro dokumenty byly potřeba, ale již nejsou použitelné pro nové služby. Další informace o podmínky, za kterých se omezení dokumentů vztahuje stále používají, najdete v části [omezení služby: dokumentu limity](search-limits-quotas-capacity.md#document-limits).
>

Vrstvy úložiště služba optimalizovaný **L1 L2**, jsou ideální pro aplikace s požadavky velkých objemů dat, ale relativně nízký počet koncovým uživatelům minimalizace latence dotazu není-li nejvyšší prioritou.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Velikost oddílu|  1 TB | 2 TB |  |  |  |  |  |
| omezení indexu a indexeru| 10 | 10 |  |  |  |  |  |

*L2* nabízí dvakrát celkové kapacity úložiště do *L1*.  Volba úrovně podle maximální množství dat, která si myslíte, že potřebuje indexu.  *L1* úroveň oddíly škálování nahoru v 1 TB přírůstcích až 12 TB, zatímco *L2* se tak zvýší o 2 TB na oddíl až do maximálního počtu 24 TB.

## <a name="evaluate-capacity"></a>Vyhodnocení kapacity

Kapacitu a náklady na provoz služby přejděte ručně v rukou. Úrovně uplatňují omezení na dvou úrovních (úložiště a prostředků), takže by měl přemýšlíte o obou protože kteréhokoli z nich nejprve nedostanete je omezení účinný. 

Obchodní požadavky stanovují obvykle počet indexů, které budete potřebovat. Například globální index pro velké úložiště dokumentů nebo možná více indexů, podle oblasti, aplikace nebo volné místo na firmy.

Chcete-li určit velikost indexu, budete muset [vytvořit takovou](search-create-index-portal.md). Struktura dat ve službě Azure Search je primárně [obrácený index](https://en.wikipedia.org/wiki/Inverted_index), který má jiné vlastnosti než zdrojová data. Pro obrácenou index velikost a složitost jsou určeny obsah, nemusí nutně jít množství dat, která kanál do něj. Zdroj velkých objemů dat s masivní redundance by mohlo způsobit index na menší než menší datové sady s velmi proměnlivá obsahem. V důsledku toho je zřídka možné odvodit velikost indexu na základě velikosti původní datové sady.

> [!NOTE] 
> I když odhad budoucích potřeb pro indexy a úložiště může být třeba izolovat, je vhodné to. Pokud kapacita na úrovni se ukázalo příliš nízké, budete muset zřizovat nové služby vyšší úrovně a poté [znovu načíst indexů](search-howto-reindex.md). Neexistuje žádné místní upgrade stejné služby z jedné skladové položky do jiného.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Krok 1: Vývoj hrubé odhady pomocí na úrovni Free

Jedním z přístupů k odhadování kapacity je začít s **Free** vrstvy. Vzpomeňte si, že **Free** služba nabízí až 3 indexy, 50 MB úložiště a indexování čas 2 minut. Může být náročné odhadnout velikost předpokládané indexu se těmto omezením, ale následující příklad ukazuje postup:

+ [Vytvoření bezplatné služby](search-create-service-portal.md)
+ Příprava malé, reprezentativní datové sady (Předpokládejme monitorovalo dokumentů a velikost vzorku 10 %)
+ [Vytvoření počátečního indexu](search-create-index-portal.md) a poznamenejte si jeho velikost na portálu (Předpokládejme 30 MB)

Předpokládáme, že byla ukázka zástupce a 10 % celý zdroj dat. a 30 MB indexu bude přibližně 300 MB Pokud všechny dokumenty se indexují. Ozbrojené s tímto číslem předběžné, může dvojnásobek velikosti s rozpočtem pro dvě indexy (vývoj a produkčním prostředí), a cena celkem 600 MB v požadavky na úložiště. To je snadno splněno **základní** vrstvy, takže byste začali existuje.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Krok 2: Vývoj pomocí fakturovatelné úrovni kontrast odhady

Zákazníci, kteří dávají přednost začínat vyhrazených prostředcích, které může pojmout větší vzorkování a časy zpracování a pak vytvářet realistické odhady množství index, velikost a svazků dotazu během vývoje. Zpočátku služba se zřizuje podle odhad nejlepší odhad a pak zrání vývojového projektu, týmy obvykle zjistit, zda existující službu nad nebo pod kapacity pro předpokládané produkční úlohy. 

1. [Zkontrolujte limity pro služby v jednotlivých vrstvách](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) můžete určit, zda nižší úrovně můžou podporovat množství indexy, které potřebujete. Mezi **základní**-**S1**-**S2** úrovně, omezení indexu jsou 15-50-200, v uvedeném pořadí.  **Optimalizované pro úložiště** úroveň má limit 10 indexy, protože jde o návrháře pro podporu nízký počet velmi velké indexy.

1. [Vytvoření služby na fakturovatelné úrovni](search-create-service-portal.md):

    + Začněte s nízkou, na **základní** nebo **S1** Pokud jste na začátku učení křivky.
    + Spustit na vysokou **S2** nebo dokonce **S3**, pokud ve velkém měřítku indexování a dotazování načítají samozřejmé.
    + Optimalizované pro úložiště na **L1** nebo **L2**, pokud se indexování velké množství dat a zatížení dotazu je relativně nízký, jako je například interní obchodní aplikace.

1. [Vytvoření počátečního indexu](search-create-index-portal.md) k určení, jak přeloží zdroje dat do indexu. Toto je jediný způsob, jak odhadnout velikost indexu.

1. [Monitorování úložiště, limity pro služby, množství dotazů a latence](search-monitor-usage.md) na portálu. Na portálu se zobrazí dotazy za druhé, omezené dotazy a latence hledání; všechny z nich může pomoci při rozhodování, pokud jste vybrali správné úrovně. Kromě portálu metriky, můžete nakonfigurovat hloubkového monitorování, jako je například interaktivní analýza povolením [Analýza provozu vyhledávání](search-traffic-analytics.md). 

Index počtu a velikosti jsou stejně důležité pro analýzu, protože je dosaženo omezení maximální prostřednictvím plného využití úložiště (oddíly) nebo maximálními limity prostředků (indexy, indexery a tak dále), podle toho, co nastane dřív. Na portálu, vám pomůže sledovat obě, zobrazuje aktuální využití a omezení maximální vedle sebe na stránce Přehled.

> [!NOTE]
> Požadavky na úložiště může být cokoli navíc zvýšeným, pokud dokumenty obsahují nadbytečná data. V ideálním případě dokumenty obsahují pouze data, které potřebujete pro vyhledávání. Binární data bez lze prohledávat a je třeba uložit samostatně (třeba ve službě Azure tabulkou nebo objektem blob storage) s pole v indexu na odkaz URL na externí data. Maximální velikost jednotlivý dokument je 16 MB (nebo méně if jsou hromadné nahrávání v jedné žádosti více dokumentů). [Omezení služby Azure Search](search-limits-quotas-capacity.md) obsahuje další informace.
>

**Důležité informace o svazku dotazu**

Dotazů za sekundu (QPS) je metriku, která získá význačnost během optimalizace výkonu, ale není obvykle potřeba úroveň Pokud očekáváte, že svazek vysokou dotaz na začátku.

Úrovně standard mohla doručovat rovnováhu replik oddílů, podpora obsloužit dotaz prostřednictvím další repliky pro načítání vyrovnávání a další oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

Zákazníci, kteří očekávají svazky silné trvalý dotaz od samého počátku by měli zvážit vyšší **standardní** úrovně se opírá o výkonnější hardware. Můžete pak proveďte oddílů a replik v režimu offline nebo dokonce přepnout na nižší úroveň služby, pokud tyto svazky dotaz sloučit. Další informace o tom, jak vypočítat propustnost dotazů, najdete v části [Azure Search výkon a optimalizace](search-performance-optimization.md).

Úložiště optimalizované úrovně Štíhlá směrem k úlohy velkých objemů dat, podpora více celkový úložiště indexů k dispozici, kde jsou požadavky na latenci dotazů poněkud volný.  Další repliky by stále využít pro načítání vyrovnávání a další oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

**Smlouvy o úrovni služeb**

**Free** funkce vrstvy a preview nezahrnují [smlouvami o úrovni (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro všechny úrovně fakturovatelná smlouvy o úrovni služeb se projeví při zřizování dostatečná redundanci pro vaši službu. Dva nebo více replik jsou požadovány pro smlouvu SLA dotazování (čtení). Tři nebo více replik jsou požadovány pro dotazy a indexování SLA (čtení a zápis). Počet oddílů není k posouzení smlouvu SLA. 

## <a name="tips-for-tier-evaluation"></a>Tipy pro úroveň hodnocení

+ Zjistěte, jak vytvářet efektivní indexy a které metodologie aktualizace jsou nejnižší impactful. Doporučujeme [Analýza provozu vyhledávání](search-traffic-analytics.md) pro poznatky získané v aktivitě dotazu.

+ Povolit metriky pro sestavení kolem dotazů a shromažďování dat celého vzorce používání (dotazy během pracovní doby, indexování během hodin mimo špičku) a tato data použít k informování budoucími rozhodnutí o zřízení. Zatímco v hodinových nebo denních tempo není praktické, můžete dynamicky upravit oddíly a prostředky, abyste plánované změny ve svazcích dotazu, nebo neplánované ale trvalé změny Pokud úrovně dostatečně dlouhá, aby zaručujete akce.

+ Mějte na paměti, že pouze nevýhodou zřizování je, že bude pravděpodobně nutné dovolí služby, pokud jsou větší než na kolik máte odhadované skutečné požadavky. Pokud chcete zabránit přerušení služby, by vytvořit novou službu v rámci stejného předplatného na vyšší úrovni a spustit ji vedle sebe, dokud všechny aplikace a žádosti o cílit na nový koncový bod.

## <a name="next-steps"></a>Další postup

Začněte **Free** vrstvy a vytvoření počátečního indexu pomocí některé podsady z vašich dat lépe pochopit jeho vlastnosti. Struktury dat ve službě Azure Search je obrácenou index, kde je velikost a složitost obrácenou indexu se určují podle obsahu. Mějte na paměti, že vysoce redundantní obsah obvykle za následek menší index než vysoce nestandardní obsah. V důsledku toho je obsah vlastnosti spíše než velikost datové sady, která určuje požadavky na úložiště indexů.

Jakmile budete mít představu počáteční velikost indexu [zřízení vám začne fakturovat služba](search-create-service-portal.md) na jednu z úrovní popisovaných v tomto článku, buď **základní**, **standardní**, nebo **Optimalizované pro úložiště** vrstvy. Zmírnit všechny umělých omezení na velikosti dat a [opětovné sestavení indexu](search-howto-reindex.md) zahrnout všechna data ve skutečnosti chcete umožnit prohledávání.

[Přidělit oddílů a replik](search-capacity-planning.md) podle potřeby můžete získat výkon a škálování, budete potřebovat.

Pokud výkon a kapacitu bez problémů, budete hotovi. V opačném případě znovu vytvořte službu vyhledávání na jinou úroveň, která více přesně odpovídá vašim potřebám.

> [!NOTE]
> Další pomoc s vašimi dotazy účtovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [obraťte se na podporu Azure](https://azure.microsoft.com/support/options/).
