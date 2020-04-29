---
title: Přizpůsobení kapacity pro úlohy dotazů a indexů
titleSuffix: Azure Cognitive Search
description: V Azure Kognitivní hledání upravovat prostředky oddílů a počítačů repliky, kde se jednotlivé prostředky účtují jako Fakturovatelné jednotky hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546268"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Nastavení kapacity v Azure Kognitivní hledání

Než [zřídíte službu vyhledávání](search-create-service-portal.md) a zamknete ji v konkrétní cenové úrovni, pochopíte úlohu replik a oddílů ve službě a způsob, jakým můžete službu upravit tak, aby vyhovovala špičkám a nedorozuměním na vyžádání prostředků.

Kapacita je funkce zvolené [úrovně](search-sku-tier.md) (úrovně určují hardwarové charakteristiky) a kombinaci repliky a oddílu potřebné pro plánované úlohy. V závislosti na vrstvě a velikosti úpravy může přidání nebo snížení kapacity trvat až 15 minut až několik hodin. 

Při úpravách přidělení replik a oddílů doporučujeme použít Azure Portal. Portál vynutil omezení povolených kombinací, které jsou nižší než maximální limity vrstvy. Nicméně pokud vyžadujete přístup založený na skriptech nebo způsobu zřizování na základě kódu, [Azure PowerShell](search-manage-powershell.md) nebo [REST API správy](https://docs.microsoft.com/rest/api/searchmanagement/services) jsou alternativní řešení.

## <a name="terminology-replicas-and-partitions"></a>Terminologie: repliky a oddíly

|||
|-|-|
|*Oddíly* | Poskytuje úložiště indexu a vstupně-výstupní operace pro operace čtení/zápisu (například při opakovaném sestavování nebo obnovování indexu). Každý oddíl má podíl celkového indexu. Pokud přidělíte tři oddíly, váš index je rozdělen na třetiny. |
|*Repliky* | Instance vyhledávací služby, které se primárně používají k vyrovnávání zatížení operací dotazů. Každá replika je jednou kopií indexu. Pokud přidělíte tři repliky, budete mít k dispozici tři kopie indexu pro obsluhu požadavků dotazů.|

## <a name="when-to-add-nodes"></a>Kdy přidat uzly

Zpočátku je služba přidělena minimální úroveň prostředků, které se skládají z jednoho oddílu a jedné repliky. 

Jedna služba musí mít dostatek prostředků pro zpracování všech úloh (indexování a dotazů). Na pozadí se nespouští žádné úlohy. Indexování můžete naplánovat na časy, kdy jsou požadavky na dotazy přirozeně méně časté, ale služba nebude jinak určovat prioritu jednoho úkolu v jiném. Kromě toho určité množství redundance vyplynule výkon dotazů při interní aktualizaci služeb nebo uzlů.

V rámci obecného pravidla hledají aplikace za následek větší repliky než oddíly, zejména v případě, že operace služby jsou posunuty k úlohám dotazů. Oddíl o [vysoké dostupnosti](#HA) vysvětluje, proč.

> [!NOTE]
> Přidání dalších replik nebo oddílů zvyšuje náklady na provoz služby a může způsobit mírné variace při objednání výsledků. Nezapomeňte se podívat na [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) , abyste porozuměli důsledkům fakturace při přidávání dalších uzlů. [Následující graf](#chart) vám může pomáhat s křížovým odkazem na počet jednotek hledání potřebných pro konkrétní konfiguraci. Další informace o tom, jak další repliky ovlivňují zpracování dotazů, najdete v tématu [řazení výsledků](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Postup přidělení replik a oddílů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyberte vyhledávací službu.

1. V **Nastavení**otevřete stránku **škálování** a upravte repliky a oddíly. 

   Následující snímek obrazovky ukazuje standardní službu zřízenou s jednou replikou a oddílem. Vzorec v dolní části indikuje, kolik jednotek hledání se používá (1). Pokud byla Jednotková cena $100 (nejedná se o skutečnou cenu), budou měsíční náklady na spuštění této služby $100 v průměru.

   ![Stránka škály zobrazující aktuální hodnoty](media/search-capacity-planning/1-initial-values.png "Stránka škály zobrazující aktuální hodnoty")

1. Pomocí posuvníku zvyšte nebo snižte počet oddílů. Vzorec v dolní části uvádí, kolik jednotek hledání se používá.

   Tento příklad zdvojnásobuje kapacitu se dvěma replikami a oddíly. Všimněte si počtu jednotek hledání; je teď čtyři, protože vzorec fakturace je vynásobený pomocí oddílů (2 x 2). Dvojnásobná kapacita je větší než dvojnásobek nákladů na provoz služby. Pokud byla cena za jednotku vyhledávání $100, nový měsíční účet by byl nyní $400.

   Aktuální cenu za každou jednotku na jednotlivých úrovních najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/search/).

   ![Přidat repliky a oddíly](media/search-capacity-planning/2-add-2-each.png "Přidat repliky a oddíly")

1. Potvrďte změny kliknutím na **Uložit** .

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
| **12 replik** |12. SU |24 SU |36 SU |– |– |– |

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