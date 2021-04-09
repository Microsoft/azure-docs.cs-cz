---
title: Odhad kapacity pro úlohy dotazů a indexů
titleSuffix: Azure Cognitive Search
description: V Azure Kognitivní hledání upravovat prostředky oddílů a počítačů repliky, kde se jednotlivé prostředky účtují jako Fakturovatelné jednotky hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99537217"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Odhad a správa kapacity služby Azure Kognitivní hledání

Než [zřídíte službu vyhledávání](search-create-service-portal.md) a zamknete ji v konkrétní cenové úrovni, pochopíte si, jak kapacita funguje a jak můžete upravovat repliky a oddíly, abyste vyhověli výkyvům úloh.

Kapacita je funkce [úrovně služby](search-sku-tier.md), která stanovuje maximální velikost úložiště na službu, na oddíl a maximální limit počtu objektů, které můžete vytvořit. Úroveň Basic je určená pro aplikace, které mají mírné požadavky na úložiště (jenom jeden oddíl), ale mají schopnost běžet v konfiguraci s vysokou dostupností (3 repliky). Jiné úrovně jsou navržené pro konkrétní úlohy nebo vzory, jako je například víceklientská architektura. Služby vytvořené na těchto úrovních využívají interně z hardwaru, který tyto scénáře pomáhá.

Architektura škálovatelnosti v Azure Kognitivní hledání je založená na flexibilních kombinacích replik a oddílů, takže můžete měnit kapacitu v závislosti na tom, jestli potřebujete další dotazy nebo indexování. Po vytvoření služby můžete zvýšit nebo snížit počet replik nebo oddílů nezávisle na sobě. Náklady se budou nacházet s každým dalším fyzickým prostředkem, ale po dokončení rozsáhlých úloh můžete snížit měřítko a snížit tak svůj účet. V závislosti na vrstvě a velikosti úpravy může přidání nebo snížení kapacity trvat až 15 minut až několik hodin.

Při úpravách přidělení replik a oddílů doporučujeme použít Azure Portal. Portál vynutil omezení povolených kombinací, které jsou nižší než maximální limity vrstvy. Nicméně pokud vyžadujete přístup založený na skriptech nebo způsobu zřizování na základě kódu, [Azure PowerShell](search-manage-powershell.md) nebo [REST API správy](/rest/api/searchmanagement/services) jsou alternativní řešení.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Koncepty: jednotky hledání, repliky, oddíly, horizontálních oddílů

Kapacita se vyjadřuje v *jednotkách vyhledávání* , které se dají přidělit v kombinaci *oddílů* a *replik*, a to pomocí základního mechanismu *horizontálního dělení* pro podporu flexibilních konfigurací:

| Koncepce  | Definice|
|----------|-----------|
|*Jednotka vyhledávání* | Jeden přírůstek celkové dostupné kapacity (jednotky 36). Je to také fakturační jednotka služby Azure Kognitivní hledání. Ke spuštění služby se vyžaduje minimálně jedna jednotka.|
|*Replika* | Instance vyhledávací služby, které se primárně používají k vyrovnávání zatížení operací dotazů. Každá replika hostuje jednu kopii indexu. Pokud přidělíte tři repliky, budete mít k dispozici tři kopie indexu pro obsluhu požadavků dotazů.|
|*Oddíl* | Fyzické úložiště a vstupně-výstupní operace pro operace čtení/zápisu (například při opakovaném sestavování nebo obnovování indexu). Každý oddíl má řez celkového indexu. Pokud přidělíte tři oddíly, váš index je rozdělen na třetiny. |
|*Horizontálních oddílů* | Blok indexu. Azure Kognitivní hledání rozdělí každý index na horizontálních oddílů, aby proces přidávání oddílů rychlejší (přesunutím horizontálních oddílů na nové jednotky pro hledání).|

Následující diagram znázorňuje vztah mezi replikami, oddíly, horizontálních oddílů a jednotkami pro hledání. Ukazuje příklad toho, jak je jeden index rozložen mezi čtyři jednotky vyhledávání ve službě se dvěma replikami a dvěma oddíly. Každá ze čtyř jednotek hledání uchovává pouze polovinu horizontálních oddílůy indexu. Jednotky vyhledávání v levém sloupci ukládají první polovinu horizontálních oddílů, včetně prvního oddílu, zatímco ta v pravém sloupci ukládají druhou polovinu horizontálních oddílů, která zahrnuje druhý oddíl. Vzhledem k tomu, že existují dvě repliky, existují dvě kopie každého indexu horizontálních oddílů. Jednotky vyhledávání v horním řádku ukládají jednu kopii, která se skládá z první repliky, zatímco ta v dolním řádku ukládá další kopii, která se skládá z druhé repliky.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Vyhledávací indexy se horizontálně dělené napříč oddíly.":::

Výše uvedený diagram je pouze jeden příklad. Je možné mnoha kombinací oddílů a replik, maximálně až 36 jednotek hledání (celkem až).

V Kognitivní hledání je Správa horizontálních oddílůa detailem implementace a nedá se konfigurovat, ale s vědomím, že index je horizontálně dělené, pomáhá pochopit občasné anomálie v pořadí a chování při automatickém dokončování:

+ Řazení anomálií: skóre hledání se nejprve vypočítávají na úrovni horizontálních oddílů a pak se agreguje do jedné sady výsledků. V závislosti na charakteristikách horizontálních oddílů obsahu může být shoda z jedné horizontálních oddílů seřazená na vyšší hodnotu než shoda v jiném typu. Pokud si všimnete, že se ve výsledcích hledání zobrazí intuitivní hodnocení, je nejpravděpodobnější vzhledem k efektům horizontálního dělení, zejména pokud jsou indexy malé. Těmto anomáliím se můžete vyhnout tak, že vyberete možnost [vypočítat skóre globálně napříč celým indexem](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), ale dojde k snížení výkonu.

+ Gramatiky automatického dokončování: Automatické dokončování dotazů, kde se shody provádějí na prvních několika znakech částečně zadaného výrazu, přijměte přibližný parametr, který forgives malé odchylky v pravopisu. Pro automatické dokončování je přibližné porovnání omezené na výrazy v rámci aktuální horizontálních oddílů. Například pokud horizontálních oddílů obsahuje "Microsoft" a částečný výraz "micor", vyhledávací web bude v tomto horizontálních oddílů odpovídat "Microsoft", ale ne v jiném horizontálních oddílů, které obsahují zbývající části indexu.

## <a name="how-to-evaluate-capacity-requirements"></a>Jak vyhodnotit požadavky na kapacitu

Kapacita a náklady na provoz služby se dostanou rukou. Vrstvy ukládají omezení na dvou úrovních: úložiště a obsah (například počet indexů ve službě). Je důležité vzít v úvahu, protože podle toho, co se vám limit dosahuje, je platný limit.

Množství indexů a jiných objektů se obvykle řídí obchodními a technickými požadavky. Například můžete mít více verzí stejného indexu pro aktivní vývoj, testování a produkci.

Požadavky na úložiště se určují podle velikosti indexů, které očekáváte k sestavení. Neexistují žádné tuhé heuristické a obecné informace, které vám pomůžou s odhady. Jediným způsobem, jak určit velikost indexu, je [sestavení jednoho](search-what-is-an-index.md). Jeho velikost bude založena na importovaných datech, analýze textu a konfiguraci indexu, například na tom, jestli povolíte moduly pro návrhy, filtrování a řazení.

Pro fulltextové vyhledávání je primární strukturou dat [obrácená struktura indexu](https://en.wikipedia.org/wiki/Inverted_index) , která má jiné charakteristiky než zdrojová data. U převráceného indexu se velikost a složitost určují podle obsahu, nikoli nutně podle množství dat, která do něj zadáte. Velký zdroj dat s vysokou redundancí může mít za následek menší index než menší datová sada, která obsahuje vysoce proměnlivý obsah. Je proto možné pouze odvodit velikost indexu na základě velikosti původní datové sady.

> [!NOTE] 
> I když odhaduje budoucí potřeby indexů a úložiště, může to vypadat stejně jako v případě vysokého odhadu. Pokud je kapacita vrstvy příliš nízká, budete muset zřídit novou službu na vyšší úrovni a pak [znovu načíst své indexy](search-howto-reindex.md). Neexistuje žádný místní upgrade služby z jedné úrovně na jinou.
>

### <a name="estimate-with-the-free-tier"></a>Odhad s úrovní Free

Jedním z přístupů k odhadu kapacity je začít s úrovní Free. Mějte na paměti, že bezplatná služba nabízí až tři indexy, 50 MB úložiště a 2 minuty času indexování. Může být náročné odhadnout velikost předpokládaného indexu s těmito omezeními, ale postup je následující:

+ [Vytvořte bezplatnou službu](search-create-service-portal.md).
+ Připravte malou, reprezentativní datovou sadu.
+ Vytvořte index a načtěte data. Pokud je možné datovou sadu hostovat ve zdroji dat Azure podporovaném indexery, můžete k vytvoření a načtení indexu použít [Průvodce importem dat na portálu](search-get-started-portal.md) . V opačném případě byste k vytvoření indexu a vložení dat měli použít REST a [post](search-get-started-rest.md) nebo [Visual Studio Code](search-get-started-vs-code.md) . Model nabízených oznámení vyžaduje, aby data byla ve formátu dokumentů JSON, kde pole v dokumentu odpovídají polím v indexu.
+ Shromažďování informací o indexu, například velikost Funkce a atributy mají dopad na úložiště. Například přidání návrhů (dotazy hledání podle typu) zvýší požadavky na úložiště. 

  Pomocí stejné datové sady můžete zkusit vytvořit několik verzí indexu s různými atributy každého pole, abyste viděli, jak se požadavky na úložiště liší. Další informace najdete v části ["dopady na úložiště" v tématu Vytvoření základního indexu](search-what-is-an-index.md#index-size).

V případě hrubého odhadu se může tato částka zdvojnásobit do rozpočtu pro dva indexy (vývoj a produkce) a odpovídajícím způsobem zvolit svou úroveň.

### <a name="estimate-with-a-billable-tier"></a>Odhad s fakturovatelnou úrovní

Vyhrazené prostředky můžou sloužit k většímu počtu vzorkování a zpracování za účelem realističtějších odhadů množství indexu, velikosti a svazků dotazů během vývoje. Někteří zákazníci najdou přímo v rámci Fakturovatelné úrovně a pak se znovu vyhodnotí jako vývoj projektu vývoje.

1. [Zkontrolujte omezení služeb na jednotlivých úrovních](./search-limits-quotas-capacity.md#index-limits) , abyste zjistili, jestli nižší úrovně můžou podporovat potřebný počet indexů. V úrovních Basic, S1 a S2 jsou limity indexu 15, 50 a 200, v uvedeném pořadí. Optimalizovaná úroveň úložiště má omezení 10 indexů, protože je navržena tak, aby podporovala nízký počet velmi rozsáhlých indexů.

1. [Vytvoření služby v Fakturovatelné úrovni](search-create-service-portal.md):

    + Pokud si nejste jisti plánovaným zatížením, začněte na stránce Basic nebo S1 nízká.
    + Začíná vysoká, v S2 nebo dokonce S3, pokud testování zahrnuje rozsáhlé indexování a načítání dotazů.
    + Pokud indexuje velké množství dat a zatížení dotazů je relativně nízké, stejně jako u interní obchodní aplikace, začněte s optimalizovaným úložištěm, v L1 nebo L2.

1. [Sestavte počáteční index](search-what-is-an-index.md) pro určení způsobu, jakým zdrojová data přecházejí do indexu. Toto je jediný způsob, jak odhadnout velikost indexu.

1. Na portálu [monitorujte úložiště, omezení služeb, svazek dotazů a latenci](search-monitor-usage.md) . Na portálu se zobrazí dotazy za sekundu, omezené dotazy a latence hledání. Všechny tyto hodnoty vám pomohou při rozhodování, zda jste vybrali správnou úroveň.

1. Přidejte repliky, pokud potřebujete vysokou dostupnost nebo pokud se setkáte s pomalým výkonem dotazů.

   Neexistují žádné pokyny k tomu, kolik replik je potřeba pro přizpůsobení dotazů. Výkon dotazů závisí na složitosti dotazování a konkurenčních úlohách. I když je přidání replik jasně výsledkem lepšího výkonu, výsledek není výhradně lineární: přidání tří replik nezaručuje trojnásobnou propustnost. Pokyny k odhadování QPS pro vaše řešení najdete v tématu [škálování pro výkon](search-performance-optimization.md)a [monitorování dotazů](search-monitor-queries.md).

> [!NOTE]
> Pokud zahrnete data, která nikdy nebudou prohledávána, je možné neploché požadavky na úložiště. V ideálním případě dokumenty obsahují pouze data, která potřebujete pro vyhledávání. Binární data se nedají prohledávat a měla by se ukládat samostatně (možná v tabulce Azure nebo v úložišti objektů BLOB). Pole by pak mělo být přidáno do indexu, aby obsahovalo odkaz na adresu URL externích dat. Maximální velikost jednotlivého vyhledávacího dokumentu je 16 MB (nebo méně, pokud hromadně nahráváte více dokumentů v jednom požadavku). Další informace najdete v tématu [omezení služby v Azure kognitivní hledání](search-limits-quotas-capacity.md).
>

**Požadavky na svazky dotazů**

Dotazy za sekundu (QPS) jsou důležitou metrikou při vyladění výkonu, ale obecně platí pouze pro zvážení vrstvy, pokud očekáváte, že na začátku budete očekávat velký objem dotazů.

Úrovně Standard můžou poskytovat rovnováhu mezi replikami a oddíly. Můžete zvýšit vyřízení dotazu přidáním replik pro vyrovnávání zatížení nebo přidat oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

Pokud na začátku očekáváte velké svazky dotazů, měli byste zvážit vyšší úrovně Standard, která je zajištěná výkonnějším hardwarem. Pak můžete převzít oddíly a repliky offline, nebo dokonce přepnout na službu nižší úrovně, pokud k těmto svazkům dotazu nedochází. Další informace o tom, jak vypočítat propustnost dotazů, najdete v článku o [výkonu a optimalizaci Azure kognitivní hledání](search-performance-optimization.md).

Vrstvy optimalizované pro úložiště jsou užitečné pro úlohy s velkým objemem dat, které podporují komplexnější úložiště indexů pro dobu, kdy jsou požadavky na latenci dotazů méně důležité. Stále byste měli používat další repliky pro vyrovnávání zatížení a další oddíly pro paralelní zpracování. Po zřízení služby můžete vyladit výkon.

**Smlouvy o úrovni služeb**

Funkce bezplatné úrovně a verze Preview neposkytují [smlouvy o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Pro všechny Fakturovatelné úrovně se SLA projeví, když zřizujete dostatečnou redundanci pro vaši službu. Pro dotaz (čtení) SLA musíte mít dvě nebo víc replik. Musíte mít tři nebo víc replik pro dotazování a indexování (pro čtení i zápis) SLA. Počet oddílů nemá vliv na SLA.

## <a name="tips-for-capacity-planning"></a>Tipy pro plánování kapacity

+ Umožňuje, aby metriky sestavily dotazy a shromažďovat data o vzorech používání (dotazy během pracovní doby, indexování v době mimo špičku). Tato data slouží k informování o rozhodnutích zajišťování služeb. I když to není praktické při hodinovém nebo denním tempo, můžete dynamicky upravovat oddíly a prostředky, aby se na svazcích dotazů vešly plánované změny. Můžete také přizpůsobit neplánované, ale trvalé změny, pokud úrovně dostatečně dostatečně dostatečně neopravňují k zajištění, aby bylo možné provádět akce.

+ Mějte na paměti, že jediným nevýhodouem zřizování je, že pokud jsou skutečné požadavky větší než vaše předpovědi, možná budete muset tuto službu vytrhnout. Aby nedošlo k přerušení služeb, vytvořili byste novou službu na vyšší úrovni a běžela vedle sebe, dokud všechny aplikace a požadavky necílí na nový koncový bod.

## <a name="when-to-add-partitions-and-replicas"></a>Kdy přidat oddíly a repliky

Zpočátku je služba přidělena minimální úroveň prostředků, které se skládají z jednoho oddílu a jedné repliky.

Jedna služba musí mít dostatek prostředků pro zpracování všech úloh (indexování a dotazů). Na pozadí se nespouští žádné úlohy. Indexování můžete naplánovat na časy, kdy jsou požadavky na dotazy přirozeně méně časté, ale služba nebude jinak určovat prioritu jednoho úkolu v jiném. Kromě toho určité množství redundance vyplynule výkon dotazů při interní aktualizaci služeb nebo uzlů.

V rámci obecného pravidla hledají aplikace za následek větší repliky než oddíly, zejména v případě, že operace služby jsou posunuty k úlohám dotazů. Oddíl o [vysoké dostupnosti](#HA) vysvětluje, proč.

[Úroveň, kterou zvolíte](search-sku-tier.md) , určuje velikost a rychlost oddílu a každá vrstva je optimalizovaná kolem sady vlastností, které odpovídají různým scénářům. Pokud zvolíte vyšší úroveň, budete možná potřebovat méně oddílů, než když provedete S1. Jedna z otázek, které budete potřebovat k zodpovězení na základě samy zaměřeného testování, je, jestli větší a levnější oddíl poskytuje lepší výkon než dvě levnější oddíly v rámci služby zřízené na nižší úrovni.

Prohledat aplikace, které vyžadují aktualizaci dat téměř v reálném čase, budou potřebovat rozčlenit více oddílů než repliky. Přidávání oddílů rozšíří operace čtení/zápisu v rámci většího počtu výpočetních prostředků. Nabízí také více místa na disku pro ukládání dalších indexů a dokumentů.

Větším indexům trvá dotaz déle. V takovém případě se může stát, že při každém přírůstkovém navýšení oddílů se v replikách vyžaduje menší, ale úměrný nárůst. Složitost vašich dotazů a svazků dotazů se projeví v tom, jak se rychle vykoná provádění dotazů.

> [!NOTE]
> Přidání dalších replik nebo oddílů zvyšuje náklady na provoz služby a může způsobit mírné variace při objednání výsledků. Nezapomeňte se podívat na [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) , abyste porozuměli důsledkům fakturace při přidávání dalších uzlů. [Následující graf](#chart) vám může pomáhat s křížovým odkazem na počet jednotek hledání potřebných pro konkrétní konfiguraci. Další informace o tom, jak další repliky ovlivňují zpracování dotazů, najdete v tématu [řazení výsledků](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Postup přidělení replik a oddílů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyberte vyhledávací službu.

1. V části **Nastavení** otevřete stránku **škálování** a upravte repliky a oddíly. 

   Následující snímek obrazovky ukazuje základní standard zřízený s jednou replikou a oddílem. Vzorec v dolní části indikuje, kolik jednotek hledání se používá (1). Pokud byla Jednotková cena $100 (nejedná se o skutečnou cenu), budou měsíční náklady na spuštění této služby $100 v průměru.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Stránka škály zobrazující aktuální hodnoty" border="true":::

1. Pomocí posuvníku zvyšte nebo snižte počet oddílů. Vzorec v dolní části uvádí, kolik jednotek hledání se používá. Vyberte **Uložit**.

   Tento příklad přidá druhou repliku a oddíl. Všimněte si počtu jednotek hledání; je teď čtyři, protože vzorec fakturace je vynásobený pomocí oddílů (2 x 2). Dvojnásobná kapacita je větší než dvojnásobek nákladů na provoz služby. Pokud byla cena za jednotku vyhledávání $100, nový měsíční účet by byl nyní $400.

   Aktuální cenu za každou jednotku na jednotlivých úrovních najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Přidat repliky a oddíly" border="true":::

1. Po uložení můžete zkontrolovat oznámení a ověřit, jestli se akce úspěšně dokončila.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Uložit změny" border="true":::

   Dokončení změn kapacity může trvat až několik hodin. Po spuštění procesu nemůžete operaci zrušit a v reálném čase není monitorování pro repliky a úpravy oddílů. Během probíhajících změn však zůstane vidět následující zpráva.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Stavová zpráva na portálu" border="true":::

> [!NOTE]
> Po zřízení služby ji nelze upgradovat na vyšší úroveň. Je nutné vytvořit vyhledávací službu na nové úrovni a znovu načíst indexy. Nápovědu k zřizování služeb najdete [v tématu Vytvoření služby Azure kognitivní hledání na portálu](search-create-service-portal.md) .
>
> Oddíly a repliky jsou navíc spravované výhradně a interně službou. Neexistuje koncept spřažení procesoru ani přiřazení úlohy ke konkrétnímu uzlu.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinace oddílů a repliky

Základní služba může mít přesně jeden oddíl a až tři repliky, a to maximálním limitem tři služby SUs. Jediným upravitelným prostředkem jsou repliky. Pro zajištění vysoké dostupnosti dotazů potřebujete minimálně dvě repliky.

Všechny služby a služby optimalizované pro úložiště Standard a Storage můžou u těchto úrovní předpokládat, že jsou v souladu s limitem 36-SU povoleným pro tyto úrovně. 

|   | **1 oddíl** | **2 oddíly** | **3 oddíly** | **4 oddíly** | **6 oddílů** | **12 oddílů** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1. SU |2. SU |3. SU |4. SU |6. SU |12. SU |
| **2 repliky** |2. SU |4. SU |6. SU |8. SU |12. SU |24 SU |
| **3 repliky** |3. SU |6. SU |9. SU |12. SU |18 SU |36 SU |
| **4 repliky** |4. SU |8. SU |12. SU |16. SU |24 SU |– |
| **5 replik** |5 SU |10. SU |15 SU |20 SU |30 SU |– |
| **6 replik** |6. SU |12. SU |18 SU |24 SU |36 SU |– |
| **12 replik** |12. SU |24 SU |36 SU |N/A |N/A |N/A |

Služba SUs, ceny a kapacita jsou podrobně vysvětleny na webu Azure. Další informace najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Počet replik a oddílů se rozdělí i na 12 (konkrétně 1, 2, 3, 4, 6, 12). Je to proto, že Azure Kognitivní hledání předdělí každý index na 12 horizontálních oddílů, aby se mohl rozdělit na stejné části ve všech oddílech. Například pokud má vaše služba tři oddíly a vytvoříte index, každý oddíl bude obsahovat čtyři horizontálních oddílůy indexu. Jak Azure Kognitivní hledání horizontálních oddílů index je podrobný popis implementace, se může v budoucích verzích změnit. I když je číslo 12 dnes, neměli byste očekávat, že toto číslo bude v budoucnu vždy 12.
>

<a id="HA"></a>

## <a name="high-availability"></a>Vysoká dostupnost

Vzhledem k tomu, že je snadné a poměrně rychlé horizontální navýšení kapacity, doporučujeme, abyste začali s jedním oddílem a jednou nebo dvěma replikami a pak nastavili horizontální navýšení kapacity pro sestavení dotazů. Úlohy dotazů běží hlavně na replikách. Pokud potřebujete větší propustnost nebo vysokou dostupnost, budete pravděpodobně potřebovat další repliky.

K dispozici jsou obecná doporučení pro vysokou dostupnost:

+ Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazů)

+ Tři nebo více replik pro vysokou dostupnost úloh čtení a zápisu (dotazy a indexování při přidání, aktualizaci nebo odstranění jednotlivých dokumentů)

Smlouvy o úrovni služeb (SLA) pro Azure Kognitivní hledání jsou zaměřené na operace dotazování a indexových aktualizací, které se skládají z přidávání, aktualizace a odstraňování dokumentů.

Úroveň Basic je vystaralá na jednom oddílu a tři repliky. Pokud chcete, aby flexibilita okamžitě reagovala na kolísání poptávky při indexování i propustnosti dotazů, vezměte v úvahu jednu z úrovní Standard.  Pokud zjistíte, že vaše požadavky na úložiště roste mnohem rychleji než propustnost dotazu, vezměte v úvahu jednu z vrstev optimalizovaných pro úložiště.

## <a name="about-queries-per-second-qps"></a>O dotazech za sekundu (QPS)

Vzhledem k velkému počtu faktorů, které se blíží k výkonu dotazů, Microsoft nepublikuje očekávaná QPS čísla. Odhady QPS musí být vyvíjeny nezávisle u každého zákazníka, a to pomocí úrovně služeb, konfigurace, indexu a konstrukcí dotazů, které jsou platné pro vaši aplikaci. Velikost indexu a složitost, velikost a složitost dotazů a množství přenosů jsou primárními determinanty QPS. Neexistuje žádný způsob, jak nabízet smysluplné odhady v případě, že tyto faktory nejsou známy.

Odhady jsou předvídatelné při výpočtu na službách, které běží na vyhrazených prostředcích (úrovně Basic a Standard). QPS můžete odhadnout přesněji, protože máte kontrolu nad více parametry. Pokyny pro přístup k odhadu najdete v tématu [výkon a optimalizace pro Azure kognitivní hledání](search-performance-optimization.md).

Pro vrstvy optimalizované pro úložiště (L1 a L2) byste měli očekávat nižší propustnost dotazů a vyšší latenci než na úrovni Standard.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak odhadnout a spravovat náklady](search-sku-manage-costs.md)