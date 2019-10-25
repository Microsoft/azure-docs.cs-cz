---
title: Horizontální navýšení kapacity oddílů a replik pro přidání kapacity pro úlohy dotazů a indexů
titleSuffix: Azure Cognitive Search
description: V Azure Kognitivní hledání upravovat prostředky oddílů a počítačů repliky, kde se jednotlivé prostředky účtují jako Fakturovatelné jednotky hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8613ddc668df338c4f96a9d37f32120718513925
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792509"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>Nahorizontální navýšení kapacity pro úlohy dotazů a indexů v Azure Kognitivní hledání

Po [výběru cenové úrovně](search-sku-tier.md) a [zřízení Vyhledávací služby](search-create-service-portal.md)je dalším krokem volitelně zvýšení počtu replik nebo oddílů používaných vaší službou. Každá úroveň nabízí pevný počet fakturačních jednotek. Tento článek vysvětluje, jak tyto jednotky přidělit, abyste dosáhli optimální konfigurace, která bude vyrovnávat vaše požadavky na provádění dotazů, indexování a úložiště.

Konfigurace prostředků je k dispozici při nastavení služby na úrovni [Basic](https://aka.ms/azuresearchbasic) nebo na jednu z [úrovní optimalizovaných pro úložiště Standard nebo Storage](search-limits-quotas-capacity.md). Pro služby na těchto úrovních je kapacita zakoupena v přírůstcích *jednotek hledání* (SUs), kde se každý oddíl a replika počítají jako jeden su. 

Použití menšího počtu výsledků služby SUs v poměrné nižší faktuře. Fakturace je platná, pokud je služba nastavená. Pokud službu nepoužíváte, jediným způsobem, jak se vyvarovat, je odstranit službu a pak ji znovu vytvořit, až ji budete potřebovat.

> [!Note]
> Odstranění služby odstraní vše. V rámci Azure Kognitivní hledání neexistuje žádné zařízení pro zálohování a obnovování trvalých dat hledání. Chcete-li znovu nasadit stávající index nové služby, měli byste spustit program, který byl původně vytvořen a načten. 

## <a name="terminology-replicas-and-partitions"></a>Terminologie: repliky a oddíly
Repliky a oddíly jsou primárními prostředky, které zálohují vyhledávací službu.

| Prostředek | Definice |
|----------|------------|
|*Oddíly* | Poskytuje úložiště indexu a vstupně-výstupní operace pro operace čtení/zápisu (například při opakovaném sestavování nebo obnovování indexu).|
|*Repliky* | Instance vyhledávací služby, které se primárně používají k vyrovnávání zatížení operací dotazů. Každá replika vždy hostuje jednu kopii indexu. Pokud máte 12 replik, budete mít 12 kopií každého indexu načteného ve službě.|

> [!NOTE]
> Neexistuje žádný způsob, jak přímo manipulovat nebo spravovat, které indexy jsou spuštěny na replice. Jedna kopie každého indexu v každé replice je součástí architektury služby.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Postup přidělení replik a oddílů
V Azure Kognitivní hledání je služba zpočátku přidělena minimální úroveň prostředků, která se skládá z jednoho oddílu a jedné repliky. U vrstev, které to podporují, můžete přírůstkově upravovat výpočetní prostředky tím, že zvětšíte oddíly, pokud potřebujete víc úložiště a vstupně-výstupní operace, nebo přidat další repliky pro větší objemy dotazů nebo lepší výkon. Jedna služba musí mít dostatek prostředků pro zpracování všech úloh (indexování a dotazů). Úlohy mezi několika službami nelze rozdělit.

Pro zvýšení nebo změnu přidělení replik a oddílů doporučujeme použít Azure Portal. Portál vynutil omezení povolených kombinací, které jsou nižší než maximální limity. Pokud vyžadujete přístup založený na skriptech nebo způsobu zřizování na základě kódu, [Azure PowerShell](search-manage-powershell.md) nebo [REST API správy](https://docs.microsoft.com/rest/api/searchmanagement/services) jsou alternativní řešení.

Vyhledávací aplikace obecně vyžadují více replik než oddílů, zejména v případě, že operace služby jsou posunuty k úlohám dotazů. Oddíl o [vysoké dostupnosti](#HA) vysvětluje, proč.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a vyberte vyhledávací službu.

2. V **Nastavení**otevřete stránku **škálování** a upravte repliky a oddíly. 

   Následující snímek obrazovky ukazuje standardní službu zřízenou s jednou replikou a oddílem. Vzorec v dolní části indikuje, kolik jednotek hledání se používá (1). Pokud byla Jednotková cena $100 (nejedná se o skutečnou cenu), budou měsíční náklady na spuštění této služby $100 v průměru.

   ![Stránka škály zobrazující aktuální hodnoty](media/search-capacity-planning/1-initial-values.png "Stránka škály zobrazující aktuální hodnoty")

3. Pomocí posuvníku zvyšte nebo snižte počet oddílů. Vzorec v dolní části uvádí, kolik jednotek hledání se používá.

   Tento příklad zdvojnásobuje kapacitu se dvěma replikami a oddíly. Všimněte si počtu jednotek hledání; je teď čtyři, protože vzorec fakturace je vynásobený pomocí oddílů (2 x 2). Dvojnásobná kapacita je větší než dvojnásobek nákladů na provoz služby. Pokud byla cena za jednotku vyhledávání $100, nový měsíční účet by byl nyní $400.

   Aktuální cenu za každou jednotku na jednotlivých úrovních najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/search/).

   ![Přidat repliky a oddíly](media/search-capacity-planning/2-add-2-each.png "Přidat repliky a oddíly")

3. Potvrďte změny kliknutím na **Uložit** .

   ![Potvrďte změny ve škálování a fakturaci](media/search-capacity-planning/3-save-confirm.png "Potvrďte změny ve škálování a fakturaci")

   Změny v kapacitě trvá několik hodin. Po spuštění procesu nemůžete operaci zrušit a v reálném čase není monitorování pro repliky a úpravy oddílů. Během probíhajících změn však zůstane vidět následující zpráva.

   ![Stavová zpráva na portálu](media/search-capacity-planning/4-updating.png "Stavová zpráva na portálu")


> [!NOTE]
> Po zřízení služby ji nelze upgradovat na vyšší SKU. Je nutné vytvořit vyhledávací službu na nové úrovni a znovu načíst indexy. Nápovědu k zřizování služeb najdete [v tématu Vytvoření služby Azure kognitivní hledání na portálu](search-create-service-portal.md) .
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinace oddílů a repliky

Základní služba může mít přesně jeden oddíl a až tři repliky, a to maximálním limitem tři služby SUs. Jediným upravitelným prostředkem jsou repliky. Pro zajištění vysoké dostupnosti dotazů potřebujete minimálně dvě repliky.

Všechny služby a služby optimalizované pro úložiště Standard a Storage můžou v souladu s limitem 36-SU předpokládat následující kombinace replik a oddílů. 

|   | **1 oddíl** | **2 oddíly** | **3 oddíly** | **4 oddíly** | **6 oddílů** | **12 oddílů** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1\. SU |2\. SU |3\. SU |4\. SU |6\. SU |12. SU |
| **2 repliky** |2\. SU |4\. SU |6\. SU |8\. SU |12. SU |24 SU |
| **3 repliky** |3\. SU |6\. SU |9\. SU |12. SU |18 SU |36 SU |
| **4 repliky** |4\. SU |8\. SU |12. SU |16. SU |24 SU |Nevztahuje se |
| **5 replik** |5 SU |10. SU |15 SU |20 SU |30 SU |Nevztahuje se |
| **6 replik** |6\. SU |12. SU |18 SU |24 SU |36 SU |Nevztahuje se |
| **12 replik** |12. SU |24 SU |36 SU |Nevztahuje se |Nevztahuje se |Nevztahuje se |

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

### <a name="index-availability-during-a-rebuild"></a>Dostupnost indexu během opětovného sestavení

Vysoká dostupnost pro Azure Kognitivní hledání souvisí s dotazy a aktualizacemi indexu, které nezahrnují opakované sestavení indexu. Pokud odstraníte pole, změníte datový typ nebo přejmenujete pole, bude nutné index znovu sestavit. Chcete-li znovu sestavit index, je nutné index odstranit, znovu vytvořit index a znovu načíst data.

> [!NOTE]
> Do indexu služby Azure Kognitivní hledání můžete přidat nová pole bez nového sestavení indexu. Hodnota nového pole bude null pro všechny dokumenty, které jsou již v indexu.

Chcete-li zachovat dostupnost indexu během opětovného sestavení, je nutné mít kopii indexu s jiným názvem ve stejné službě nebo kopii indexu se stejným názvem v jiné službě a potom v kódu poskytnout logiku přesměrování nebo převzetí služeb při selhání.

## <a name="disaster-recovery"></a>Zotavení po havárii
V současné době není k dispozici žádný vestavěný mechanismus pro zotavení po havárii. Přidání oddílů nebo replik by představovalo špatnou strategii pro splnění cílů zotavení po havárii. Nejběžnějším přístupem je přidat redundanci na úrovni služby nastavením druhé služby vyhledávání v jiné oblasti. Stejně jako v případě dostupnosti při opětovném sestavování indexu musí být logika přesměrování nebo převzetí služeb při selhání pocházet z vašeho kódu.

## <a name="increase-query-performance-with-replicas"></a>Zvýšení výkonu dotazů pomocí replik
Latence dotazů je ukazatel, který vyžaduje další repliky. Obecně platí, že prvním krokem k vylepšení výkonu dotazů je přidání dalších prostředků. Když přidáváte repliky, další kopie indexu se přenesou do online režimu, aby podporovaly větší úlohy dotazů a vyrovnaly zatížení požadavků přes více replik.

Pro dotazy za sekundu nemůžeme poskytnout pevné odhady dotazů (QPS): výkon dotazů závisí na složitosti dotazu a konkurenčních úlohách. I když je přidání replik jasně výsledkem lepšího výkonu, výsledek není výhradně lineární: přidání tří replik nezaručuje trojnásobnou propustnost.

Pokyny k odhadování QPS pro vaše úlohy najdete v tématu věnovaném [důležitým informacím o výkonu a optimalizaci pro Azure kognitivní hledání](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Zvýšení výkonu indexování pomocí oddílů
Prohledat aplikace, které vyžadují aktualizaci dat téměř v reálném čase, budou potřebovat rozčlenit více oddílů než repliky. Přidávání oddílů rozšíří operace čtení/zápisu v rámci většího počtu výpočetních prostředků. Nabízí také více místa na disku pro ukládání dalších indexů a dokumentů.

Větším indexům trvá dotaz déle. V takovém případě se může stát, že při každém přírůstkovém navýšení oddílů se v replikách vyžaduje menší, ale úměrný nárůst. Složitost vašich dotazů a svazků dotazů se projeví v tom, jak se rychle vykoná provádění dotazů.


## <a name="next-steps"></a>Další kroky

[Výběr cenové úrovně pro Azure Kognitivní hledání](search-sku-tier.md)
