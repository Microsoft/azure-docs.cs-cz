---
title: Přizpůsobení kapacity pro úlohy dotazů a indexů
titleSuffix: Azure Cognitive Search
description: V Azure Kognitivní hledání upravovat prostředky oddílů a počítačů repliky, kde se jednotlivé prostředky účtují jako Fakturovatelné jednotky hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 92dcbfd360938724bb65b734d7c69ea61d7826b0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533039"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>Úprava kapacity služby Azure Kognitivní hledání

Než [zřídíte službu vyhledávání](search-create-service-portal.md) a zamknete ji v konkrétní cenové úrovni, pochopíte si, jak kapacita funguje a jak můžete upravovat repliky a oddíly, abyste vyhověli výkyvům úloh.

Kapacita je funkce zvolené [úrovně](search-sku-tier.md) (úrovně určují hardwarové charakteristiky) a kombinaci repliky a oddílu potřebné pro plánované úlohy. Po vytvoření služby můžete zvýšit nebo snížit počet replik nebo oddílů nezávisle na sobě. Náklady se budou nacházet s každým dalším fyzickým prostředkem, ale po dokončení rozsáhlých úloh můžete snížit měřítko a snížit tak svůj účet. V závislosti na vrstvě a velikosti úpravy může přidání nebo snížení kapacity trvat až 15 minut až několik hodin.

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

+ Řazení anomálií: skóre hledání se nejprve vypočítávají na úrovni horizontálních oddílů a pak se agreguje do jedné sady výsledků. V závislosti na charakteristikách horizontálních oddílů obsahu může být shoda z jedné horizontálních oddílů seřazená na vyšší hodnotu než shoda v jiném typu. Pokud si všimnete, že ve výsledcích hledání nenajdete intuitivní hodnocení, je nejpravděpodobnější vzhledem k efektům horizontálního dělení, zejména pokud jsou indexy malé. Těmto anomáliím se můžete vyhnout tak, že vyberete možnost [vypočítat skóre globálně napříč celým indexem](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), ale dojde k snížení výkonu.

+ Gramatiky automatického dokončování: Automatické dokončování dotazů, kde se shody provádějí na prvních několika znakech částečně zadaného výrazu, přijměte přibližný parametr, který forgives malé odchylky v pravopisu. Pro automatické dokončování je přibližné porovnání omezené na výrazy v rámci aktuální horizontálních oddílů. Například pokud horizontálních oddílů obsahuje "Microsoft" a částečný výraz "micor", vyhledávací web bude v tomto horizontálních oddílů odpovídat "Microsoft", ale ne v jiném horizontálních oddílů, které obsahují zbývající části indexu.

## <a name="when-to-add-nodes"></a>Kdy přidat uzly

Zpočátku je služba přidělena minimální úroveň prostředků, které se skládají z jednoho oddílu a jedné repliky.

Jedna služba musí mít dostatek prostředků pro zpracování všech úloh (indexování a dotazů). Na pozadí se nespouští žádné úlohy. Indexování můžete naplánovat na časy, kdy jsou požadavky na dotazy přirozeně méně časté, ale služba nebude jinak určovat prioritu jednoho úkolu v jiném. Kromě toho určité množství redundance vyplynule výkon dotazů při interní aktualizaci služeb nebo uzlů.

V rámci obecného pravidla hledají aplikace za následek větší repliky než oddíly, zejména v případě, že operace služby jsou posunuty k úlohám dotazů. Oddíl o [vysoké dostupnosti](#HA) vysvětluje, proč.

> [!NOTE]
> Přidání dalších replik nebo oddílů zvyšuje náklady na provoz služby a může způsobit mírné variace při objednání výsledků. Nezapomeňte se podívat na [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) , abyste porozuměli důsledkům fakturace při přidávání dalších uzlů. [Následující graf](#chart) vám může pomáhat s křížovým odkazem na počet jednotek hledání potřebných pro konkrétní konfiguraci. Další informace o tom, jak další repliky ovlivňují zpracování dotazů, najdete v tématu [řazení výsledků](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Postup přidělení replik a oddílů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyberte vyhledávací službu.

1. V **Nastavení** otevřete stránku **škálování** a upravte repliky a oddíly. 

   Následující snímek obrazovky ukazuje standardní službu zřízenou s jednou replikou a oddílem. Vzorec v dolní části indikuje, kolik jednotek hledání se používá (1). Pokud byla Jednotková cena $100 (nejedná se o skutečnou cenu), budou měsíční náklady na spuštění této služby $100 v průměru.

   ![Stránka škály zobrazující aktuální hodnoty](media/search-capacity-planning/1-initial-values.png "Stránka škály zobrazující aktuální hodnoty")

1. Pomocí posuvníku zvyšte nebo snižte počet oddílů. Vzorec v dolní části uvádí, kolik jednotek hledání se používá.

   Tento příklad zdvojnásobuje kapacitu se dvěma replikami a oddíly. Všimněte si počtu jednotek hledání; je teď čtyři, protože vzorec fakturace je vynásobený pomocí oddílů (2 x 2). Dvojnásobná kapacita je větší než dvojnásobek nákladů na provoz služby. Pokud byla cena za jednotku vyhledávání $100, nový měsíční účet by byl nyní $400.

   Aktuální cenu za každou jednotku na jednotlivých úrovních najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/search/).

   ![Přidat repliky a oddíly](media/search-capacity-planning/2-add-2-each.png "Přidat repliky a oddíly")

1. Vyberte **Uložit** a potvrďte změny.

   ![Potvrďte změny ve škálování a fakturaci](media/search-capacity-planning/3-save-confirm.png "Potvrďte změny ve škálování a fakturaci")

   Změny v kapacitě trvá několik hodin. Po spuštění procesu nemůžete operaci zrušit a v reálném čase není monitorování pro repliky a úpravy oddílů. Během probíhajících změn však zůstane vidět následující zpráva.

   ![Stavová zpráva na portálu](media/search-capacity-planning/4-updating.png "Stavová zpráva na portálu")

> [!NOTE]
> Po zřízení služby ji nelze upgradovat na vyšší úroveň. Je nutné vytvořit vyhledávací službu na nové úrovni a znovu načíst indexy. Nápovědu k zřizování služeb najdete [v tématu Vytvoření služby Azure kognitivní hledání na portálu](search-create-service-portal.md) .
>
> Oddíly a repliky jsou navíc spravované výhradně a interně službou. Neexistuje koncept spřažení procesoru ani přiřazení úlohy ke konkrétnímu uzlu.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinace oddílů a repliky

Základní služba může mít přesně jeden oddíl a až tři repliky, a to maximálním limitem tři služby SUs. Jediným upravitelným prostředkem jsou repliky. Pro zajištění vysoké dostupnosti dotazů potřebujete minimálně dvě repliky.

Všechny služby a služby optimalizované pro úložiště Standard a Storage můžou v souladu s limitem 36-SU předpokládat následující kombinace replik a oddílů. 

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

* Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazů)

* Tři nebo více replik pro vysokou dostupnost úloh čtení a zápisu (dotazy a indexování při přidání, aktualizaci nebo odstranění jednotlivých dokumentů)

Smlouvy o úrovni služeb (SLA) pro Azure Kognitivní hledání jsou zaměřené na operace dotazování a indexových aktualizací, které se skládají z přidávání, aktualizace a odstraňování dokumentů.

Úroveň Basic je vystaralá na jednom oddílu a tři repliky. Pokud chcete, aby flexibilita okamžitě reagovala na kolísání poptávky při indexování i propustnosti dotazů, vezměte v úvahu jednu z úrovní Standard.  Pokud zjistíte, že vaše požadavky na úložiště roste mnohem rychleji než propustnost dotazu, vezměte v úvahu jednu z vrstev optimalizovaných pro úložiště.

## <a name="disaster-recovery"></a>Zotavení po havárii

V současné době není k dispozici žádný vestavěný mechanismus pro zotavení po havárii. Přidání oddílů nebo replik by představovalo špatnou strategii pro splnění cílů zotavení po havárii. Nejběžnějším přístupem je přidat redundanci na úrovni služby nastavením druhé služby vyhledávání v jiné oblasti. Stejně jako v případě dostupnosti při opětovném sestavování indexu musí být logika přesměrování nebo převzetí služeb při selhání pocházet z vašeho kódu.

## <a name="estimate-replicas"></a>Odhadování replik

V produkční službě byste měli přidělit tři repliky pro účely smlouvy SLA. Pokud se setkáte s pomalým výkonem dotazů, můžete přidat repliky, aby byly další kopie indexu do online režimu, aby podporovaly větší úlohy dotazů a vyrovnaly zatížení požadavků přes více replik.

Neposkytujeme pokyny k tomu, kolik replik je potřeba pro přizpůsobení dotazů. Výkon dotazů závisí na složitosti dotazování a konkurenčních úlohách. I když je přidání replik jasně výsledkem lepšího výkonu, výsledek není výhradně lineární: přidání tří replik nezaručuje trojnásobnou propustnost.

Pokyny k odhadování QPS pro vaše řešení najdete v tématu [škálování pro výkon](search-performance-optimization.md)a [monitorování dotazů](search-monitor-queries.md) .

## <a name="estimate-partitions"></a>Odhadnout oddíly

[Úroveň, kterou zvolíte](search-sku-tier.md) , určuje velikost a rychlost oddílu a každá vrstva je optimalizovaná kolem sady vlastností, které odpovídají různým scénářům. Pokud zvolíte vyšší úroveň, budete možná potřebovat méně oddílů, než když provedete S1. Jedna z otázek, které budete potřebovat k zodpovězení na základě samy zaměřeného testování, je, jestli větší a levnější oddíl poskytuje lepší výkon než dvě levnější oddíly v rámci služby zřízené na nižší úrovni.

Prohledat aplikace, které vyžadují aktualizaci dat téměř v reálném čase, budou potřebovat rozčlenit více oddílů než repliky. Přidávání oddílů rozšíří operace čtení/zápisu v rámci většího počtu výpočetních prostředků. Nabízí také více místa na disku pro ukládání dalších indexů a dokumentů.

Větším indexům trvá dotaz déle. V takovém případě se může stát, že při každém přírůstkovém navýšení oddílů se v replikách vyžaduje menší, ale úměrný nárůst. Složitost vašich dotazů a svazků dotazů se projeví v tom, jak se rychle vykoná provádění dotazů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výběr cenové úrovně pro Azure Kognitivní hledání](search-sku-tier.md)