---
title: Úprava kapacity pro úlohy dotazů a indexů
titleSuffix: Azure Cognitive Search
description: Upravte prostředky oddílu a repliky počítače v Azure Cognitive Search, kde každý prostředek je cena v jednotkách fakturovatelné vyhledávání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546268"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Úprava kapacity v Azure Cognitive Search

Před [zřizováním vyhledávací služby](search-create-service-portal.md) a uzamčením v určité cenové úrovni trvá několik minut pochopit roli replik a oddílů ve službě a jak můžete upravit službu tak, aby vyhovovala špičkám a poklesům poptávky po prostředcích.

Kapacita je funkce [vrstvy, kterou zvolíte](search-sku-tier.md) (vrstvy určují vlastnosti hardwaru) a kombinace replik a oddílů nezbytná pro předpokládané úlohy. V závislosti na úrovni a velikosti nastavení může přidání nebo snížení kapacity trvat od 15 minut do několika hodin. 

Při úpravě přidělení replik a oddílů doporučujeme použít portál Azure. Portál vynucuje limity na povolené kombinace, které zůstávají pod maximální limity úrovně. Pokud ale požadujete přístup zřizování založený na skriptech nebo na kódu, azure [powershell](search-manage-powershell.md) nebo [rozhraní REST api pro správu](https://docs.microsoft.com/rest/api/searchmanagement/services) jsou alternativní řešení.

## <a name="terminology-replicas-and-partitions"></a>Terminologie: repliky a oddíly

|||
|-|-|
|*Oddíly* | Poskytuje úložiště indexu a vstupně-va pro operace čtení a zápisu (například při opětovném sestavení nebo aktualizaci indexu). Každý oddíl má podíl na celkovém indexu. Pokud přidělíte tři oddíly, index je rozdělen do třetin. |
|*Repliky* | Instance vyhledávací služby, které se používají především k operacím dotazů vyrovnávání zatížení. Každá replika je jedna kopie indexu. Pokud přidělíte tři repliky, budete mít k dispozici tři kopie indexu pro obsluhu požadavků na dotazy.|

## <a name="when-to-add-nodes"></a>Kdy přidat uzly

Zpočátku je službě přidělena minimální úroveň prostředků skládající se z jednoho oddílu a jedné repliky. 

Jedna služba musí mít dostatek prostředků pro zpracování všech úloh (indexování a dotazy). Ani zatížení běží na pozadí. Indexování můžete naplánovat pro časy, kdy jsou požadavky na dotazy přirozeně méně časté, ale služba jinak nebude upřednostňovat jednu úlohu před druhou. Kromě toho určité množství redundance vyhladí výkon dotazu při interně aktualizovány služby nebo uzly.

Obecně platí, že vyhledávací aplikace mají tendenci potřebovat více replik než oddíly, zejména pokud jsou operace služby zkreslené směrem k úlohám dotazů. Část o [vysoké dostupnosti](#HA) vysvětluje proč.

> [!NOTE]
> Přidání dalšírepliky nebo oddíly zvyšuje náklady na spuštění služby a může zavést mírné odchylky v pořadí výsledků. Nezapomeňte zkontrolovat [cenovou kalkulačku,](https://azure.microsoft.com/pricing/calculator/) abyste pochopili důsledky pro fakturaci přidání dalších uzlů. [Níže uvedená tabulka](#chart) vám pomůže křížově odkazovat na počet vyhledávacích jednotek požadovaných pro určitou konfiguraci. Další informace o tom, jak další repliky ovlivňují zpracování dotazů, naleznete [v tématu Řazení výsledků](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Jak přidělit repliky a oddíly

1. Přihlaste se k [portálu Azure a](https://portal.azure.com/) vyberte vyhledávací službu.

1. V **nastavení**otevřete stránku **Měřítko** a upravte repliky a oddíly. 

   Následující snímek obrazovky ukazuje standardní službu zřízenou s jednou replikou a oddílem. Vzorec v dolní části označuje, kolik vyhledávacích jednotek je používáno (1). Pokud by jednotková cena byla 100 USD (nikoli skutečná cena), měsíční náklady na provoz této služby by byly v průměru 100 USD.

   ![Změnit velikost stránky zobrazující aktuální hodnoty](media/search-capacity-planning/1-initial-values.png "Změnit velikost stránky zobrazující aktuální hodnoty")

1. Pomocí posuvníku zvyšte nebo snižte počet oddílů. Vzorec v dolní části označuje, kolik vyhledávacích jednotek je používáno.

   Tento příklad zdvojnásobí kapacitu, se dvěma replikami a oddíly každý. Všimněte si počtu vyhledávacích jednotek; nyní je čtyři, protože fakturační vzorec je repliky vynásobené oddíly (2 x 2). Zdvojnásobení kapacity více než zdvojnásobuje náklady na provoz služby. Pokud by náklady na vyhledávání byly 100 USD, nový měsíční účet by nyní činil 400 USD.

   Aktuální jednotkové náklady každé úrovně najdete na [stránce Ceny](https://azure.microsoft.com/pricing/details/search/).

   ![Přidání replik a oddílů](media/search-capacity-planning/2-add-2-each.png "Přidání replik a oddílů")

1. Kliknutím na **Uložit** potvrďte změny.

   ![Potvrzení změn škálování a fakturace](media/search-capacity-planning/3-save-confirm.png "Potvrzení změn škálování a fakturace")

   Změny kapacity trvat několik hodin. Po spuštění procesu nelze zrušit a neexistuje žádné monitorování replik a oddílů v reálném čase. Následující zpráva však zůstane viditelná, zatímco změny probíhají.

   ![Stavová zpráva na portálu](media/search-capacity-planning/4-updating.png "Stavová zpráva na portálu")

> [!NOTE]
> Po zřízení služby ji nelze upgradovat na vyšší úroveň. Je nutné vytvořit vyhledávací službu na nové úrovni a znovu načíst indexy. Nápovědu k zřizování služeb [najdete v tématu Vytvoření služby Azure Cognitive Search na portálu.](search-create-service-portal.md)
>
> Kromě toho oddíly a repliky jsou spravovány výhradně a interně službou. Neexistuje žádný koncept spřažení procesoru nebo přiřazení úlohy k určitému uzlu.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinace oddílů a replik

Základní služba může mít přesně jeden oddíl a až tři repliky, pro maximální limit tří su. Jediným nastavitelným zdrojem jsou repliky. Potřebujete minimálně dvě repliky pro vysokou dostupnost na dotazy.

Všechny vyhledávací služby optimalizované pro standardy a úložiště mohou předpokládat následující kombinace replik a oddílů s výhradou limitu 36-SU. 

|   | **1 oddíl** | **2 oddíly** | **3 oddíly** | **4 oddíly** | **6 oddílů** | **12 oddílů** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliky** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliky** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliky** |4 SU |8 SU |12 SU |16 SU |24 SU |Není dostupné. |
| **5 replik** |5 SU |10 SU |15 SU |20 SU |30 SU |Není dostupné. |
| **6 replik** |6 SU |12 SU |18 SU |24 SU |36 SU |Není dostupné. |
| **12 replik** |12 SU |24 SU |36 SU |Není dostupné. |Není dostupné. |Není dostupné. |

SU, ceny a kapacita jsou podrobně vysvětleny na webu Azure. Další informace naleznete [v tématu Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Počet replik a oddílů se rovnoměrně rozdělí na 12 (konkrétně 1, 2, 3, 4, 6, 12). Důvodem je, že Azure Cognitive Search předem rozdělí každý index do 12 střepů tak, aby jej lze rozložit ve stejných částech napříč všemi oddíly. Například pokud vaše služba má tři oddíly a vytvořit index, každý oddíl bude obsahovat čtyři oddíly indexu. Jak Azure Cognitive Search střepy index je podrobnosti implementace, může změnit v budoucích verzích. I když je číslo 12 dnes, neměli byste očekávat, že toto číslo bude vždy 12 v budoucnu.
>

<a id="HA"></a>

## <a name="high-availability"></a>Vysoká dostupnost

Vzhledem k tomu, že je snadné a relativně rychlé škálování, obecně doporučujeme začít s jedním oddílem a jednou nebo dvěma replikami a potom vertikálně navýšit kapacitu jako sestavení svazků dotazů. Úlohy dotazů se spouštějí především na replikách. Pokud potřebujete větší propustnost nebo vysokou dostupnost, budete pravděpodobně potřebovat další repliky.

Obecná doporučení pro vysokou dostupnost jsou:

* Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazy)

* Tři nebo více replik pro vysokou dostupnost úloh pro čtení a zápis (dotazy a indexování jako jednotlivé dokumenty jsou přidány, aktualizovány nebo odstraněny)

Smlouvy o úrovni služeb (SLA) pro Azure Cognitive Search jsou zaměřené na operace dotazů a na aktualizace indexu, které se skládají z přidávání, aktualizace nebo odstraňování dokumentů.

Základní úroveň vrcholy se na jeden oddíl a tři repliky. Pokud chcete flexibilitu okamžitě reagovat na výkyvy v poptávce pro indexování a propustnost dotazu, zvažte jednu z úrovní Standard.  Pokud zjistíte, že vaše požadavky na úložiště rostou mnohem rychleji než propustnost dotazů, zvažte jednu z úrovní optimalizovaných pro úložiště.

## <a name="disaster-recovery"></a>Zotavení po havárii

V současné době neexistuje žádný vestavěný mechanismus pro zotavení po havárii. Přidání oddílů nebo replik by bylo nesprávné strategie pro splnění cílů zotavení po havárii. Nejběžnějším přístupem je přidání redundance na úrovni služby nastavením druhé vyhledávací služby v jiné oblasti. Stejně jako u dostupnosti během obnovení indexu, přesměrování nebo převzetí služeb při selhání logika musí pocházet z kódu.

## <a name="estimate-replicas"></a>Odhadreplik

Ve výrobní službě byste měli přidělit tři repliky pro účely sla. Pokud dojde k pomalému výkonu dotazů, můžete přidat repliky tak, aby další kopie indexu byly přeneseny do režimu online, aby podporovaly větší úlohy dotazů a vyrovnaly požadavky na více replik.

Neposkytujeme pokyny o tom, kolik replik je potřeba pro přizpůsobení zatížení dotazů. Výkon dotazu závisí na složitosti dotazu a konkurenční úlohy. Přestože přidání replik y jasně vede k lepšímu výkonu, výsledek není striktně lineární: přidání tří replik nezaručuje trojitou propustnost.

Pokyny při odhadu QPS pro vaše řešení, najdete [v tématu škálování pro výkon](search-performance-optimization.md)a monitorování [dotazů](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Odhad oddílů

[Úroveň, kterou zvolíte,](search-sku-tier.md) určuje velikost oddílu a rychlost a každá úroveň je optimalizována kolem sady charakteristik, které odpovídají různým scénářům. Pokud zvolíte vyšší úroveň, budete potřebovat méně oddílů, než když jdete s S1. Jednou z otázek, které budete muset odpovědět prostřednictvím samořízenétestování je, zda větší a dražší oddíl poskytuje lepší výkon než dva levnější oddíly na služby zřízené na nižší úrovni.

Vyhledávací aplikace, které vyžadují aktualizaci dat téměř v reálném čase, budou potřebovat proporcionálně více oddílů než repliky. Přidání oddílů šíří operace čtení a zápisu napříč větším počtem výpočetních prostředků. Poskytuje také více místa na disku pro ukládání dalších indexů a dokumentů.

Větší indexy trvat déle dotaz. Jako takové můžete zjistit, že každé přírůstkové zvýšení oddílů vyžaduje menší, ale proporcionální zvýšení repliky. Složitost dotazů a objemu dotazů bude faktor do jak rychle spuštění dotazu se otočil.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výběr cenové úrovně pro Azure Cognitive Search](search-sku-tier.md)