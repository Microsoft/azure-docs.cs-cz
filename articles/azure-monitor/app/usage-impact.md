---
title: Dopad využití Azure Application Insights | Dokumentace Microsoftu
description: Analyzujte, jak různé vlastnosti potenciálně ovlivňují sazby za převod pro části vašich aplikací.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fffb83fe680572c2448323a61b767a401c9a4834
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323702"
---
# <a name="impact-analysis-with-application-insights"></a>Analýza dopadu pomocí Application Insights

Dopad analyzuje způsob, jakým doba načítání a další vlastnosti ovlivňují přepočítací poměry pro různé části aplikace. Chcete-li přesněji umístit, zjistí, jak **libovolná dimenze** **zobrazení stránky**, **vlastní událost**nebo **žádost** ovlivňuje použití jiného **zobrazení stránky** nebo **vlastní události**. 

![Dopad – nástroj](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Pořád si nejste jisti, jaký dopad to dělá?

Jedním ze způsobů, jak si představit dopad, je jako skutečný nástroj pro vyrovnávání argumentů s někým v týmu o tom, jak zpomalení v některých aspektech vaší lokality ovlivňují to, jestli se uživatelé můžou pohybovat. I když uživatelé můžou tolerovat určitý objem zpomalení, dopad vám poskytne přehled o tom, jak nejlépe vyvážit optimalizaci a výkon a maximalizovat převod uživatelů.

Ale Analýza výkonu je jenom podmnožinou možností dopadu. Vzhledem k tomu, že dopad podporuje vlastní události a dimenze, odpovídáte na otázky, jako je například způsob, jakým se volba prohlížeče uživatelů koreluje s různými sazbami převodu, stačí jen pár kliknutí.

![Převod snímků obrazovky prohlížeči](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Váš Application Insights prostředek musí obsahovat zobrazení stránky nebo vlastní události pro použití nástroje dopad. [Naučte se, jak nastavit aplikaci tak, aby automaticky shromáždila zobrazení stránek pomocí Application Insights JavaScript SDK](./javascript.md). Pamatujte také na to, že vzhledem k tomu, že analyzujete korelační, záleží na velikosti vzorku.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Je doba načítání stránky ovlivněna tím, kolik lidí převádí na moji stránku?

Chcete-li začít s odpovědí na dotazy pomocí nástroje dopad, vyberte počáteční zobrazení stránky, vlastní událost nebo žádost.

![Dopad – nástroj](./media/usage-impact/0002-dropdown.png)

1. V rozevíracím seznamu **zobrazení stránky** vyberte zobrazení stránky.
2. Ponechte možnost **analyzovat způsob, jakým** má rozevírací seznam výchozí výběr **doby trvání** (v **tomto kontextu je** alias pro **dobu načtení stránky**).
3. Pro **vliv na použití** rozevíracího seznamu vyberte vlastní událost. Tato událost by měla odpovídat prvku uživatelského rozhraní v zobrazení stránky, které jste vybrali v kroku 1.

![Snímek obrazovky s výsledky](./media/usage-impact/0003-results.png)

V této instanci jako čas načítání **stránky produktu** se zvýší poměr konverze na **zakoupený produkt** . Na základě výše uvedené distribuce by bylo možné cílit na optimální dobu trvání načítání stránky 3,5 sekund, aby se dosáhlo potenciálního poměru 55%%. Další vylepšení výkonu pro snížení doby načítání pod 3,5 sekund v současné době nekoreluje s dalšími výhodami převodu.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Co když mi vlastním způsobem sleduje zobrazení stránek nebo dobu načítání?

Dopad podporuje standardní i vlastní vlastnosti a měření. Použijte cokoli, co potřebujete. Místo doby trvání použijte filtry na primárních a sekundárních událostech, abyste získali konkrétnější informace.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Převádí se uživatelé z různých zemí nebo oblastí v různých tarifech?

1. V rozevíracím seznamu **zobrazení stránky** vyberte zobrazení stránky.
2. Vyberte zemi nebo oblast v části **analyzovat způsob jejich** rozevíracího seznamu.
3. Pro **vliv na použití** rozevíracího seznamu vyberte vlastní událost, která odpovídá prvku uživatelského rozhraní v zobrazení stránky, které jste zvolili v kroku 1.

V takovém případě se výsledky již nevejdou do souvislého modelu osy x stejně jako v prvním příkladu. Místo toho se zobrazí vizualizace podobná segmentaci trychtýře. Seřadit podle **využití** a zobrazit variaci převodu na vlastní událost na základě země nebo oblasti.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Jak nástroj dopad vypočítá tyto přepočítací koeficienty?

V rámci digestoře nástroj dopady spoléhá na [korelační koeficient Pearsonova](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Výsledky jsou vypočítány mezi-1 a 1 s-1 představující negativní lineární korelaci a 1 představuje pozitivní lineární korelaci.

Základní rozpis toho, jak analýza dopadů funguje, je následující:

Nechť _A_ = hlavní zobrazení stránky/vlastní událost/požadavek, který jste vybrali v prvním rozevíracím seznamu. (**Pro zobrazení stránky**).

Nechť _B_ = sekundární zobrazení stránky a vlastní událost, kterou vyberete (**ovlivňuje použití**).

Dopad na ukázku všech relací od uživatelů ve vybraném časovém rozsahu. Pro každou relaci vyhledá všechny výskyty _._

Relace se pak rozdělí do dvou různých druhů _podrelací_ na základě jedné ze dvou podmínek:

- Převedená subrelace se skládá z relace končící na událost _B_ a _zahrnuje všechny události, ke kterým_ dojde před _B_.
- K nepřevedené podrelaci dojde, když _se_objeví vše bez terminálu _B_.

Způsob, jakým se nakonec počítá dopad, se liší podle toho, jestli analyzujeme podle metriky nebo podle dimenze. Pro metriky jsou _A_v podrelaci průměrně. Vzhledem k tomu, že pro dimenze je hodnota každé _a_ přispívá _1/N_ do hodnoty přiřazené hodnotě _B_ , kde _N_ je _číslo v_podrelaci.

## <a name="next-steps"></a>Další kroky

- Chcete-li povolit prostředí používání, začněte odesílat [vlastní události](./api-custom-events-metrics.md#trackevent) nebo [zobrazení stránek](./api-custom-events-metrics.md#page-views).
- Pokud jste už odeslali vlastní události nebo zobrazení stránky, prozkoumejte nástroje využití a zjistěte, jak uživatelé používají vaši službu.
    - [Trychtýře](usage-funnels.md)
    - [Uchovávání](usage-retention.md)
    - [Toky uživatelů](usage-flows.md)
    - [Workbooks](../platform/workbooks-overview.md)
    - [Přidat kontext uživatele](usage-send-user-context.md)

