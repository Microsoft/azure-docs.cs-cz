---
title: Dopad používání Azure Application Insights | Dokumentace Microsoftu
description: Analýza jak různé vlastnosti potenciálně ovlivnit míry úspěšnosti části vaší aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/25/2018
ms.reviewer: daviste
ms.author: mbullwin
ms.openlocfilehash: 9188776fdd213f01523069b08bd898f48bee57a4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643114"
---
# <a name="impact-analysis-with-application-insights"></a>Analýza dopadu pomocí Application Insights

Dopad analyzuje, jak ovlivnit dobu načítání a dalších vlastností míry úspěšnosti pro různé části vaší aplikace. K přesněji řečeno, zjistí jak **jinou dimenzi** z **zobrazení stránky**, **vlastní událost**, nebo **požadavek** ovlivňuje využití různé **zobrazení stránky** nebo **vlastní událost**. 

![Nástroj dopad](./media/app-insights-usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Ještě nejste si jistí dopad čemu?

Jeden ze způsobů zamyslete dopad na chod firmy je jako ultimate nástroj pro vyrovnání argumenty s někým z vašeho týmu o jak ovlivňuje pomalost v některých aspektů vašeho webu, zda zaujmout uživatele. Zatímco uživatelé může tolerovat určité množství pomalého chodu, dopad poskytuje přehled o tom, jak nejlepší vyvážit optimalizace a výkon pro maximalizaci převod uživatelů.

Ale analýzu výkonu je pouze podmnožinu těchto dopad na možnosti. Protože dopad podporuje vlastní události a dimenze, zodpovězení otázek, třeba jak volba prohlížeče uživatele je možné korelovat s jinou sazby převodu jsou jenom pár kliknutí.

![Snímek obrazovky převodu pomocí prohlížeče](./media/app-insights-usage-impact/0004-browsers.png)

> [!NOTE]
> Prostředek Application Insights může obsahovat zobrazení stránek nebo vlastní události a použít nástroj dopad. [Zjistěte, jak nastavit aplikaci shromažďovat zobrazení stránek automaticky pomocí Application Insights JavaScript SDK](app-insights-javascript.md). Také je důležité Uvědomte si, že vzhledem k tomu, že se analýza korelace, ukázkový velikost.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Je čas načítání stránky vliv, kolik lidí převést na stránce?

Pokud chcete začít, odpovídání na dotazy pomocí nástroje dopad, zvolte zobrazení úvodní stránky, vlastní událost nebo žádost.

![Nástroj dopad](./media/app-insights-usage-impact/0002-dropdown.png)

1. Vyberte zobrazení stránky z **pro zobrazení stránky** rozevíracího seznamu.
2. Nechte **analyzovat jak jeho** rozevírací seznam pro výběr výchozí **doba trvání** (v tomto kontextu **doba trvání** je alias pro **čas načítání stránky**.)
3. Pro **ovlivní využití tohoto** rozevírací seznam, vyberte vlastní událost. Tato událost, musí odpovídat prvku uživatelského rozhraní na zobrazení stránky, které jste vybrali v kroku 1.

![Snímek obrazovky s výsledky](./media/app-insights-usage-impact/0003-results.png)

V tomto případě jako **stránky produktu** čas načtení zvyšuje konverzní poměr k **nákupu produktu kliknutí na** ocitne mimo provoz. Podle výše uvedené distribuční, doba trvání optimální stránky zatížení 3.5 sekund může cílit dosáhnout potenciální 55 % konverzní poměr. Další vylepšení výkonu zkrátit čas načtení nižší než 3.5 sekund není aktuálně korelovat s výhodami pro další převodu.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Co když mám jsem sledování zobrazení stránek nebo načíst časy vlastní způsoby?

Dopad podporuje standardní a vlastní vlastnosti a měření. Použijte cokoli, co chcete. Místo doby trvání pomocí filtrů událostí primární a sekundární si to trochu přiblížit.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Převoditelné různými rychlostmi uživatelů z různých zemí nebo oblastí?

1. Vyberte zobrazení stránky z **pro zobrazení stránky** rozevíracího seznamu.
2. Vyberte "Země nebo oblast" v **analyzovat jak jeho** rozevíracího seznamu
3. Pro **ovlivní využití tohoto** rozevírací seznam, vyberte vlastní událost, která odpovídá prvku uživatelského rozhraní pro zobrazení stránky jste zvolili v kroku 1.

V tomto případě výsledky se už nevejdou do modelu spojitou osou x stejně jako v prvním příkladu. Místo toho se zobrazí vizualizaci podobný segmentovaným trychtýřového grafu. Seřadit podle **využití** zobrazíte variace převod na vaše vlastní událost na základě země.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Jak nástroj dopad vypočítat tyto míry úspěšnosti?

Pod pokličkou, využívá nástroj dopad [Pearsonova korelačního koeficientu] (https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Výsledky se zpracovávají mezi -1 a 1 hodnotou -1 představují nulové korelace a 1 představující kladné korelace.

Základní přehled fungování analýza dopadu vypadá takto:

Umožní _A_ = hlavní stránku zobrazení/vlastní události nebo žádosti vyberte v prvním rozevíracím seznamu. (**Pro zobrazení stránky**).

Umožní _B_ = událostí zobrazení/vlastní sekundární stránky vyberete (**ovlivní využití tohoto**).

Dopad zjistí ukázku všechny relace uživatelů ve vybraném časovém rozsahu. Pro každou relaci hledá všechny výskyty _A_.

Relace jsou pak rozdělenou do dvou různých typů z _subsessions_ na základě jedné z dvě podmínky:

- Převedený subsession se skládá z relace končí _B_ událostí a zahrnuje všechny _A_ události, ke kterým došlo před _B_.
- Nepřevedeném subsession nastane, když všechny _A_společnosti k nim dojde terminálu _B_.

Takže v konečném důsledku výpočtu dopad se liší v závislosti na tom, jestli analyzujeme podle metriky nebo dimenze. Pro všechny metriky _A_uživatele v podrelaci zprůměrovány. Vzhledem k tomu pro dimenze hodnotu každého _A_ přispívá _1 nebo N_ hodnotě přiřazené _B_ kde _N_ je počet _A_uživatele v podrelací.

## <a name="next-steps"></a>Další postup

- Povolit použití prostředí, spusťte odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již posílat vlastní události nebo zobrazení stránek, prozkoumejte nástroje využití se dozvíte, jak uživatelé vaši službu používat.
    - [Trychtýře](usage-funnels.md)
    - [Uchování](app-insights-usage-retention.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Workbooks](app-insights-usage-workbooks.md)
    - [Přidat kontext uživatele](app-insights-usage-send-user-context.md)