---
title: Vyberte cenové úrovně nebo SKU pro službu Azure Search – Azure Search
description: 'Služba Azure Search je možné zřídit tyto skladové položky: Je k dispozici v různých konfigurace prostředků a kapacity úrovně Free, Basic a Standard a Standard.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539275"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Zvolte cenovou úroveň pro službu Azure Search

Při vytváření služby Azure Search [je prostředek vytvořený](search-create-service-portal.md) na cenové úrovně nebo SKU, který je pevně po dobu životnosti služby. Zahrnuje Free, Basic, Standard a optimalizované pro úložiště. Standardní a optimalizované pro úložiště jsou k dispozici v několika konfiguracích a kapacity.

Většina zákazníků začíná na úrovni Free, mohou vyhodnotit službu. Potom upgradovat na jednu z vyšší úrovně pro vývoj a provoz nasazení. Všechny rychlých startů a kurzů můžete dokončit pomocí na úrovni Free, včetně těch pro náročné kognitivního vyhledávání.

> [!NOTE]
> Společnost Microsoft aktuálně poskytuje optimalizované pro úložiště úrovně služeb ve verzi preview za zlevněné ceny pro účely testování a experimentování, s cílem shromažďování zpětné vazby. Chcete zjistit koncové ceny oznámíme později při těchto úrovních jsou obecně dostupné. Nedoporučujeme tyto úrovně používají, aplikacích v produkčním prostředí.

Vrstev odrážejí charakteristiky hardwaru, hostování služby (spíše než funkce) a jsou rozlišené pomocí:

+ Počet indexů, které lze vytvořit.
+ Velikost a rychlost oddílů (fyzické úložiště).

Přestože všechny úrovně, včetně na úrovni Free, obecně nabízí paritu funkcí, větší úlohy může diktovat potřebu vyšší úrovně. Například [indexování AI pomocí služeb Cognitive Services](cognitive-search-concept-intro.md) je dlouho běžící dovednosti tento časový limit na bezplatné služby Pokud datová sada je malá.

> [!NOTE] 
> Výjimka, která má paritu funkcí je [indexery](search-indexer-overview.md), které nejsou k dispozici na S3 HD, High Density.
>

V rámci úrovně, je možné [upravit repliky a oddílu prostředků](search-capacity-planning.md) zvýšení nebo snížení škálování. Můžete začít s jednou nebo dvěma jednotlivých a dočasně zvýšit váš výpočetní výkon pro náročné úlohy indexování. Schopnost optimalizovat úrovně prostředků v rámci úrovně zvyšuje flexibilitu, ale také mírně komplikuje analýzy. Budete muset experimentovat, uvidíte, jestli nižší úrovně s více prostředky/replik nabízí lepší výkon než vyšší úroveň s méně prostředků a hodnotu. Další informace o kdy a proč by upravit kapacitu, najdete v článku [aspekty týkající se výkonu a optimalizace](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Úrovně pro službu Azure Search

V následující tabulce jsou uvedeny dostupné úrovně. Můžete najít další informace o různých úrovních na [stránce s cenami](https://azure.microsoft.com/pricing/details/search/)v [omezení služby Azure Search](search-limits-quotas-capacity.md) článku a na portálu stránce, pokud zřizujete služby.

|Úroveň | Kapacita |
|-----|-------------|
|Free | Sdílenou s jinými odběrateli. Škálovatelné. Je omezená na tři indexy a přes 50 MB úložiště. |
|Basic | Vyhrazené výpočetní prostředky pro produkční úlohy v menším měřítku. Jeden oddíl 2 GB a až tři repliky. |
|Standard 1 (S1) | S1 a vyšší, vyhrazených počítačů s další kapacitou úložiště a zpracování na všech úrovních. Velikost oddílu pro S1, je 25 GB na oddíl (s délkou maximálně 300 GB na službu). |
|Standard 2 (S2) | Podobně jako na S1, ale s oddíly 100 GB (a maximálně 1,2 TB na službu). |
|Standard 3 (S3) | 200 GB oddíly (s délkou maximálně 2,4 TB na službu). |
|Standardní 3 Vysoká hustota (S3 HD, High Density) | S vysokou hustotou je *hostující režim* pro S3. Základního hardwaru je optimalizovaná pro větší počet indexů menší a je určené pro scénáře víceklientské architektury. S3 HD má stejné sazba za jednotku jako S3, ale hardware je optimalizovaný pro rychlejší čtení na spoustu menších indexy.|
|Optimalizované pro úložiště 1 (L1) | 1 TB oddíly (s délkou maximálně 12 TB na službu). |
|Úložiště optimalizované 2 (L2) | Oddíly 2 TB (s délkou maximálně 24 TB na službu). |

> [!NOTE] 
> Úrovně optimalizované pro úložiště nabízejí větší kapacitu úložiště za nižší cenu za TB než úrovně Standard. Primární kompromis je vyšší latence dotazu, který by měl ověřit pro vaše konkrétní aplikační požadavky.  Další informace o aspektech týkajících se výkonu této úrovni najdete v tématu [aspekty týkající se výkonu a optimalizace](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Jak funguje fakturace

Existují tři způsoby, jak vynakládá ve službě Azure Search a existují proměnlivé a dlouhodobý komponent. Tato část popisuje tři komponenty fakturace: základní náklady na službu, poplatky za odchozí přenos dat a AI obohacené indexování.

### <a name="core-service-costs-fixed-and-variable"></a>Náklady na službu Core (pevné a proměnné)

Pro samotnou službu minimální sazba je první částí vyhledávání (1 repliky x 1 oddíl). Tohle minimum je pevně daná po dobu životnosti služby, protože službu nelze spustit žádnou menší než tato konfigurace.

Nad rámec minimum můžete přidat repliky a oddíly, nezávisle na sobě. Například můžete přidat pouze repliky nebo pouze oddíly. Zvyšování kapacity prostřednictvím repliky a oddíly, které tvoří součást náklady na proměnnou.

Účtování vychází [vzorec (repliky x oddíly x rychlost)](#search-units). Sazba se účtuje závisí na cenové úrovni, které vyberete.

Na následujícím snímku obrazovky ceny za jednotku se udává Free a Basic, S1. (S2, S3, L1 a L2 nejsou zobrazeny.) Pokud jste vytvořili základní služby, bude měsíční náklady průměrná hodnota, která se zobrazí pro *cena 1*. Pro standardní služby, bude vaše měsíční náklady průměrná hodnota, která se zobrazí pro *cena 2*. Náklady na jednotku zvýšit pro každou vrstvu, protože výpočetní kapacitu výkon a úložiště je větší na jednotlivých úrovních po sobě jdoucích. Kurzy pro službu Azure Search jsou k dispozici na [stránce s cenami Azure Search](https://azure.microsoft.com/pricing/details/search/).

![Ceny za jednotku](./media/search-sku-tier/per-unit-pricing.png "ceny za jednotku")

Když jste odhadnout náklady na řešení pro hledání, zachovat v paměti, která ceny a kapacity není lineární. (Možnost zdvojnásobení kapacity více než zdvojnásobuje náklady.) Příklad toho, jak vzorec funguje, najdete v části [postup přidělení repliky a oddíly](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Fakturace podle jednotek vyhledávání

Nejdůležitější fakturační koncept pochopit pro operace Azure Search je *jednotka služby search* (SU). Protože Azure Search závisí na repliky a oddíly pro indexování a dotazy, nemá smysl pro fakturaci podle právě jeden z nich. Místo toho fakturace vychází složeného obou.

Produkt je SU *repliky* a *oddíly* používané službou: **(R x P = SU)** .

Každá služba začíná u jedné SU (jednu repliku a jeden oddíl) jako minimální. Maximální počet pro libovolnou službu je 36 su. To se dá kontaktovat několika různými způsoby: 6 oddíly x 6 repliky, nebo 3 oddíly x 12 replik pro pro příklad. Je běžné použití nižší, než celkové kapacity (například 3 repliky se 3 oddíly služby účtovat jako 9 su). Zobrazit [kombinace oddílů a replik](search-capacity-planning.md#chart) graf pro platné kombinace.

Fakturační sazba je po hodinách za SU. Každá úroveň má postupné vyšší sazba. Vyšší úrovně, které jsou součástí větší a zrychlit oddíly, a to přispívá k celkové vyšší hodinové sazby za tuto úroveň. Kurzy pro každou vrstvu můžete zobrazit na [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/) stránky.

Většina zákazníků přinést jen část celkové kapacity online, uchovávající zbývající ve fondu. Pro fakturaci, počet oddílů a replik, které můžete převést do režimu online, vypočte tak, SU, určuje vzorec platíte po hodinách.

### <a name="data-egress-charges-during-indexing"></a>Poplatky za odchozí přenos dat během indexování

Pomocí [indexerů Azure Search](search-indexer-overview.md) může mít vliv na fakturaci, v závislosti na umístění služby. Poplatky za odchozí přenos dat můžete eliminovat úplně, pokud vytvoříte službu Azure Search ve stejné oblasti jako vaše data. Zde jsou některé informace z [šířky pásma stránce s cenami](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft nebude účtovat žádná příchozí data na jakoukoli službu v Azure nebo pro všechna data, odchozí z Azure Search.

+ V případě multiservice řešení se neúčtuje za data, překročení přenosu, když jsou všechny služby ve stejné oblasti.

Poplatky za odchozí data Pokud služby jsou v různých oblastech. Tyto poplatky nejsou ve skutečnosti součástí vyúčtování Azure Search. Zde jsou už uvedených vzhledem k tomu, že pokud používáte data nebo AI obohacené indexování má načítat data z různých oblastí, zobrazí se projeví ve vyúčtování celkové náklady na.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>AI obohacené indexování pomocí služeb Cognitive Services

Pro [indexování AI pomocí služeb Cognitive Services](cognitive-search-concept-intro.md), měli byste naplánovat připojení účtovaných prostředků služeb Azure Cognitive Services, ve stejné oblasti jako Azure Search na cenovou úroveň S0 s průběžnými platbami zpracování. Neexistuje žádné pevné náklady spojené s připojením služby Cognitive Services. Platíte jenom za zpracování, které potřebujete.

Extrakce Image během hádání dokumentu je poplatek za Azure Search. Účtuje se podle počtu imagí, které jsou extrahovány z vašich dokumentů. Extrakce textu je aktuálně zdarma.

Další obohacení, jako je zpracování přirozeného jazyka, jsou založeny na [integrované kognitivní dovednosti](cognitive-search-predefined-skills.md) a účtovat na vrub prostředku služeb Cognitive Services. Se účtuje stejnou sazbou jako by měl provést úlohu přímo pomocí služeb Cognitive Services. Další informace najdete v tématu [připojit prostředek služeb Cognitive Services dovedností](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Fakturace pro extrahování bitové kopie v kognitivního vyhledávání

Pokud extrahujete obrázky ze souborů v kognitivního vyhledávání, indexování kanálu, vám bude účtovat pro tuto operaci je ve vyúčtování Azure Search. V [konfigurace indexeru](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** je parametr, který aktivuje image extrakce. Pokud **imageAction** je nastavena na "none" (výchozí), vám nebude účtovat image extrakce.

Ceny se můžou změnit. Je popsána v [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/) stránku pro službu Azure Search.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Fakturace za integrované znalosti kognitivního vyhledávání

Při nastavování kanál obohacování jakékoli [integrované znalosti](cognitive-search-predefined-skills.md) použít v kanálu jsou založeny na modely strojového učení. Tyto modely jsou k dispozici ve službách Cognitive Services. Pokud používáte tyto modely během indexování, vám budou účtovat stejná sazba jako by byl, pokud požadovaný prostředek přímo.

Řekněme například, že máte kanál, který používá optické rozpoznávání znaků (OCR) vůči prohledaná soubory JPEG a výsledný text pro volného tvaru vyhledávací dotazy odesílají do indexu Azure Search. Váš kanál indexování bude zahrnovat indexer s [OCR dovednosti](cognitive-search-skill-ocr.md), a že by bylo dovednosti [připojené k prostředku služeb Cognitive Services](cognitive-search-attach-cognitive-services.md). Při spuštění indexeru se poplatky za spuštění OCR uvádět na fakturách Cognitive prostředky.

## <a name="tips-for-reducing-costs"></a>Tipy pro snížení nákladů

Nelze vypnout službu snížit vyúčtování. Vyhrazené prostředky jsou vždy funkční, přidělené pro vaše výhradní použití po dobu životnosti vaší služby. Jediný způsob, jak snížit vaše faktura je snížit úroveň, která stále poskytuje přijatelný výkon repliky a oddíly a [dodržování předpisů SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Vybrat úroveň nižší hodinová sazba je jeden způsob, jak snížit náklady. S1 hodinové sazby jsou nižší než S2 nebo S3. Za předpokladu, že jste při zřizování služby nižší konci vaše projekce zatížení, pokud jste velký růst služby, můžete vytvořit druhý větší vrstvené službu, opětovné sestavení indexů na službu druhý a pak odstranit první z nich.

Pokud jste provedli, plánování kapacity pro místní servery, víte, že jsou společné pro "koupit" tak může zvládnout růst předpokládané. S cloudovou službou můžete pokračovat další úspory nákladů agresivně vzhledem k tomu, že nejsou uzamčeny konkrétní nákupu. Můžete vždy přejdete na službu vyšší vrstvenou pokud tu není dostatečná.

### <a name="capacity"></a>Kapacita

Ve službě Azure Search má strukturu kapacity *repliky* a *oddíly*.

+ Repliky jsou instance služby search. Každá replika hostitelem jednu kopii tohoto indexu s vyrovnáváním zatížení. Například služba se šesti replik má šest kopií každý index načíst služby.

+ Oddíly úložiště indexů a automaticky rozdělí prohledávatelná data. Rozdělit indexu na polovinu dva oddíly, tři oddíly ho rozdělte do třetiny a tak dále. Z hlediska kapacity *oddílu velikost* je primární funkce odlišující mezi úrovněmi.

> [!NOTE]
> Všechny úrovně Standard a optimalizované pro úložiště podporovat [flexibilní kombinací repliky a oddíly](search-capacity-planning.md#chart) tak, aby se [optimalizovat systém pro rychlost nebo úložiště](search-performance-optimization.md) změnou zůstatek na účtu. Úroveň Basic nabízí až tři repliky pro zajištění vysoké dostupnosti, ale má pouze jeden oddíl. Bezplatné úrovně neposkytují vyhrazené prostředky: výpočetní prostředky se sdílí několik předplatitelů.

### <a name="more-about-service-limits"></a>Další informace o omezení služby

Služba Hostitel prostředkům, jako jsou indexy a indexery. Každá úroveň má [omezení služby](search-limits-quotas-capacity.md) na počtu prostředků, můžete vytvořit. Maximální počet indexů (a dalších objektů) proto je druhý odlišující funkce mezi úrovněmi. Při kontrole jednotlivé možnosti na portálu, mějte na paměti omezený počet indexů. Další prostředky, jako jsou indexování zdrojů dat a dovednosti, jsou opatřit indexu omezení.

## <a name="consumption-patterns"></a>Vzory využití

Většina zákazníků začínat bezplatná služba, která se zachovat po neomezenou dobu, a pak vyberte jednu z úrovně Standard nebo optimalizované pro úložiště pro vážné vývojové nebo produkční úlohy.

![Služba Azure Search cenové úrovně](./media/search-sku-tier/tiers.png "cenových úrovní Azure Search")

Na koncích nízký klíč a vysoký, Basic a S3 HD, High Density se vztahují na jejich vzorce spotřeby důležitý, ale neobvyklé. Basic je pro malé produkční úlohy. Nabízí SLA, vyhrazené prostředky a vysokou dostupnost, ale přináší středně velká úložiště, přičemž navýšení kapacity na celkem 2 GB. Tato úroveň byla navržena pro zákazníky, kteří konzistentně zlozvycích dostupné kapacity. Na konci vysokou S3 HD je pro úlohy, které jsou typické pro nezávislé výrobce softwaru, partnery, [víceklientské řešení](search-modeling-multitenant-saas-applications.md), nebo všechny konfigurace, která volá pro velký počet malých indexy. Často je jasné, Basic nebo S3 HD je správné úrovně. Pokud chcete potvrzení, můžete vytvořit [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [požádejte podporu Azure](https://azure.microsoft.com/support/options/) pokyny.

Běžně používaných standardní úrovní S1 až S3, ujistěte se, až postup zvýšení úrovně kapacity. Existují důležitý bodů na velikost oddílu a omezení na čísla indexů, indexerů a corollary prostředků:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Velikost oddílu|  25 GB | 100 GB | 200 GB |  |  |  |  |
| omezení indexu a indexeru| 50 | 200 | 200 |  |  |  |  |

S1 je běžné volbou pro zákazníky, kteří potřebují vyhrazených prostředků a několik oddílů. S1 nabízí oddíly 25 GB až 12 oddíly, poskytuje maximálně 300 GB za služby, pokud maximalizovat oddíly přes repliky. (Viz [přidělit oddílů a replik](search-capacity-planning.md#chart) další skupinu s vyrovnáváním přidělení.)

Na stránkách portálu a cenové zaměření na velikost oddílu a úložiště, ale pro každou vrstvu všechny výpočetní možnosti (kapacita disku, procesory rychlost) obecně zvýšit lineárně s cenou. Je rychlejší než S1 repliku S2 a S3 je rychlejší než S2. Úrovně S3 zrušení volání ze smyčky lineární model ceny za výpočetní prostředky se neúměrně rychlejší vstupně-výstupních operací. Pokud očekáváte, že se kritické místo vstupně-výstupních operací, mějte na paměti, že vám přinese větší vstupně-výstupních operací u S3 než můžete začít s nižší úrovně.

S3 a S3 HD, High Density se zálohují na stejné infrastruktury vysoké kapacity, ale dosáhnou své maximální limity různými způsoby. S3 cílí na menší počet velmi velké indexů, takže jeho maximální limit je vázán na zdroj (pro každou službu 2,4 TB). S3 HD, High Density, zaměřuje větší počet indexů velmi malý. Za 1000 indexů S3 HD, High Density dosaženo omezení v podobě omezení indexu. Pokud jste zákazníkem S3 HD, High Density a vy potřebujete více než 1000 indexů, obraťte se na Microsoft Support informace o tom, aby bylo možné pokračovat.

> [!NOTE]
> Limity pro dokumenty byly potřeba najednou, ale jsou už pro nové služby. Informace o podmínkách, ve kterých se omezení dokumentů vztahuje stále použít, najdete v tématu [dokumentu limity](search-limits-quotas-capacity.md#document-limits).
>

Vrstvy úložiště optimalizovat L1 a L2, jsou ideální pro aplikace s požadavky velkých objemů dat, ale relativně nízký počet koncoví uživatelé při minimalizaci latence dotazu není s nejvyšší prioritou.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Velikost oddílu|  1 TB | 2 TB |  |  |  |  |  |
| omezení indexu a indexeru| 10 | 10 |  |  |  |  |  |

L2 nabízí dvakrát celkové kapacity úložiště L1.  Volba úrovně podle maximální množství dat, která si myslíte, že potřebuje indexu. Oddíly úroveň L1 škálovat v přírůstcích po 1 TB na nejvýše 12 TB. L2 oddílů zvýšit 2 TB na oddíl až do maximálního počtu 24 TB.

## <a name="evaluating-capacity"></a>Vyhodnocení kapacity

Kapacitu a náklady na provoz služby přímo souvisí. Úrovně uplatňují omezení na dvou úrovních: úložiště a prostředky. Můžete byste uvažovat o obou protože podle omezení dosáhnete nejprve je omezení účinný.

Obchodní požadavky stanovují obvykle počet indexů, které budete potřebovat. Můžete například potřebovat, aby globální index pro velké úložiště dokumentů. Nebo můžete potřebovat více indexů, podle oblasti, aplikace nebo volné místo na firmy.

Chcete-li určit velikost indexu, budete muset [vytvořit takovou](search-create-index-portal.md). Struktura dat ve službě Azure Search je primárně [obrácený index](https://en.wikipedia.org/wiki/Inverted_index) strukturu, která má jiné vlastnosti než zdrojová data. Pro obrácenou index velikost a složitost jsou určeny podle obsahu, ne tedy nutně množství dat, která kanál do něj. Zdroj velkých objemů dat s vysokou redundance by mohlo způsobit index na menší než menší datové sady, který obsahuje velmi proměnlivá obsah. Proto je zřídka možné odvodit velikost indexu na základě velikosti původní datové sady.

> [!NOTE] 
> I když odhad budoucích potřeb pro indexy a úložiště může být třeba izolovat, je vhodné to. Kapacita na úrovni se ukázalo příliš nízké, budete muset zřizovat nové služby na vyšší úroveň a potom [znovu načíst indexů](search-howto-reindex.md). Neexistuje žádné místní upgrade jedné skladovou Položku služby.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Krok 1: Vývoj s využitím služby na úrovni Free hrubé odhady

Jedním z přístupů k odhadování kapacity je začít u bezplatné úrovně. Mějte na paměti, že nabízí bezplatné služby až tři indexy, 50 MB úložiště a indexování čas 2 minut. Může být náročné odhadnout velikost předpokládané indexu s těmito omezeními. Tady je postup, který můžete využít:

+ [Vytvoření bezplatné služby](search-create-service-portal.md).
+ Příprava malé, reprezentativní datové sady (například 5 000 dokumentů a velikost vzorku 10 procent).
+ [Vytvoření počátečního indexu](search-create-index-portal.md) a poznamenejte si jeho velikost na portálu (například 30 MB).

Pokud vzorek je zástupce a 10 procent společností z žebříčku celý zdroj dat., stane 30 MB index Pokud všech dokumentů indexovaných přibližně 300 MB. Ozbrojené s tímto číslem předběžné, může dvojnásobek velikosti s rozpočtem pro dvě indexy (vývoj a produkci). To vám dává celkem 600 MB v požadavky na úložiště. Tento požadavek snadno splňují na úrovni Basic, takže byste začali existuje.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Krok 2: Vývoj s využitím fakturovatelné úrovni kontrast odhady

Zákazníci, kteří dávají přednost začínat vyhrazených prostředcích, které může pojmout větší vzorkování a časy zpracování a pak vytvářet realistické odhady množství index, velikost a svazků dotazu během vývoje. Zpočátku služba zřídí podle odhad nejlepší odhad. Potom během existence vývojového projektu, týmy obvykle zjistit, zda existující službu nad nebo pod kapacity pro předpokládané produkční úlohy.

1. [Zkontrolujte limity pro služby v jednotlivých vrstvách](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) můžete určit, zda nižší úrovně můžou podporovat počet indexů, které potřebujete. Napříč úrovněmi Basic, S1 a S2 indexu omezení jsou 15, 50 a 200, v uvedeném pořadí. Na úrovni optimalizované pro úložiště má omezení 10 indexů, protože je navržena pro podporu nízký počet velmi velké indexy.

1. [Vytvoření služby na fakturovatelné úrovni](search-create-service-portal.md):

    + Začněte s nízkou, Basic nebo S1, pokud jste na začátku učení křivky.
    + Začněte vysoké, S2 nebo S3 sudé, pokud víte, že budete mít rozsáhlé indexování a dotazování zatížení.
    + Začněte úložiště optimalizované do L1 nebo L2, pokud se indexování velké množství dat a dotazové zatížení je poměrně málo, jako s interní obchodní aplikace.

1. [Vytvoření počátečního indexu](search-create-index-portal.md) k určení, jak přeloží zdroje dat do indexu. Toto je jediný způsob, jak odhadnout velikost indexu.

1. [Monitorování úložiště, limity pro služby, množství dotazů a latence](search-monitor-usage.md) na portálu. Na portálu zobrazuje dotazů za sekundu, omezené dotazy a latence hledání. Všechny tyto hodnoty můžou pomoct při rozhodování, pokud jste vybrali správné úrovně. Můžete také konfigurovat hloubkového monitorování hodnoty jako interaktivní analýzy povolením [Analýza provozu vyhledávání](search-traffic-analytics.md).

Index počtu a velikosti jsou stejně důležité analýzy. Je to proto, že je dosaženo omezení maximální prostřednictvím plného využití úložiště (oddíly) nebo maximálními limity prostředků (indexy, indexery a tak dále), podle toho, co nastane dřív. Na portálu, vám pomůže sledovat obě, zobrazuje aktuální využití a omezení maximální vedle sebe na stránce Přehled.

> [!NOTE]
> Požadavky na úložiště můžete zvětšený, pokud dokumenty obsahují nadbytečná data. V ideálním případě dokumenty obsahují pouze data, která budete potřebovat pro vyhledávání. Binární data není prohledávatelné a by měla být uložena samostatně (možná ve službě Azure table nebo blob storage). Pole by měla být přidali v kroku index pro uložení adresu URL odkazu na externí data. Maximální velikost jednotlivý dokument je 16 MB (nebo méně Pokud jste hromadné nahrávání v jedné žádosti více dokumentů). Další informace najdete v tématu [omezení služby Azure Search](search-limits-quotas-capacity.md).
>

**Důležité informace o svazku dotazu**

Dotazů za sekundu (QPS), je důležitou metrikou při optimalizaci výkonu, ale je obecně jenom úroveň posouzení Pokud očekáváte, že svazek vysokou dotaz na začátku.

Úrovně Standard může poskytnout zůstatek repliky a oddíly. Můžete zvýšit vyřízení dotaz tak, že přidáte replik pro vyrovnávání zatížení nebo přidejte oddíly pro paralelní zpracování. Můžete pak optimalizaci výkonu po zřízení služby.

Pokud očekáváte, že svazky vysokou trvalý dotaz od samého počátku, měli byste zvážit vyšší úrovně Standard se opírá o výkonnější hardware. Můžete pak proveďte oddílů a replik v režimu offline nebo dokonce přepnout na nižší úroveň služby, pokud tyto svazky dotazu k událostem. Další informace o tom, jak vypočítat propustnost dotazů, najdete v části [Azure Search výkon a optimalizace](search-performance-optimization.md).

Úrovně optimalizované pro úložiště jsou užitečné pro úlohy velkých objemů dat a podporovat další index celkové dostupné úložiště pro kdy jsou požadavky na latenci dotazů méně důležité. Stále byste měli použít další repliky pro zatížení vyrovnávání a další oddíly pro paralelní zpracování. Můžete pak optimalizaci výkonu po zřízení služby.

**Smlouvy o úrovni služeb**

Bezplatná úroveň a ve verzi preview funkce neposkytují [smlouvách o úrovni služeb](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro všechny úrovně fakturovatelná smlouvy o úrovni služeb se projeví při zřizování dostatečná redundanci pro vaši službu. Musíte mít dvě nebo více replik pro smlouvy SLA dotazování (čtení). Musíte mít tři nebo více replikami dotazů a indexování smlouvy o úrovni služeb (čtení a zápis). Počet oddílů nemá vliv na smlouvy SLA.

## <a name="tips-for-tier-evaluation"></a>Tipy pro úroveň hodnocení

+ Zjistěte, jak vytvářet efektivní indexy a zjistěte, jaké metody aktualizace nejmenším dopadem. Použití [Analýza provozu vyhledávání](search-traffic-analytics.md) získat přehledy o dotaz aktivity.

+ Povolit metriky k sestavení kolem dotazů a shromažďování dat celého vzorce používání (dotazy během pracovní doby, indexování během hodin mimo špičku). Tato data použijte k podkladem pro rozhodnutí zřizovací služby. Když není praktické v hodinových nebo denních tempo, můžete dynamicky upravit oddíly a prostředky, abyste plánované změny ve svazcích dotazu. Neplánované ale trvalé změny může obsahovat také pokud úrovně dostatečně dlouhá, aby zaručujete akce.

+ Mějte na paměti, že pouze nevýhodou underprovisioning je, že bude pravděpodobně nutné dovolí služby, pokud jsou větší než vaše predikcí skutečné požadavky. Pokud chcete zabránit přerušení služby, by vytvořit novou službu v rámci stejného předplatného na vyšší úrovni a spustit ji vedle sebe, dokud všechny aplikace a žádosti o cílit na nový koncový bod.

## <a name="next-steps"></a>Další postup

Začněte s bezplatnou úroveň a vytvoření počátečního indexu pomocí podmnožina vašich dat lépe pochopit jeho vlastnosti. Struktura dat ve službě Azure Search je struktury obrácenou indexu. Velikost a složitost obrácenou indexu se určuje podle obsahu. Mějte na paměti, že vysoce redundantní obsah obvykle za následek menší index než vysoce nestandardní obsah. Takže obsahu charakteristiky, nikoli velikost datové sady, určení požadavků na úložiště indexů.

Až budete mít odhad počáteční velikost indexu [zřízení vám začne fakturovat služba](search-create-service-portal.md) na jednu z úrovní popisovaných v tomto článku: Basic, Standard nebo optimalizováno pro úložiště. Zmírnit všechny umělých omezení na velikosti dat a [opětovné sestavení indexu](search-howto-reindex.md) zahrnout všechna data, která mají být prohledávatelné.

[Přidělit oddílů a replik](search-capacity-planning.md) podle potřeby můžete získat výkon a škálování, budete potřebovat.

Pokud výkon a kapacitu, se dají, jste hotovi. V opačném případě znovu vytvořte službu vyhledávání na jinou úroveň, která více přesně odpovídá vašim potřebám.

> [!NOTE]
> Pokud máte nějaké dotazy, zveřejněte na [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) nebo [požádejte podporu Azure](https://azure.microsoft.com/support/options/).
