---
title: Dopad využití přehledů aplikací Azure | Dokumenty Microsoftu
description: Analyzujte, jaký by mohly mít různé vlastnosti vliv na míru konverze pro části aplikací.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 787221c4df3f06029d19ee779a28bb763723f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671031"
---
# <a name="impact-analysis-with-application-insights"></a>Analýza dopadů pomocí přehledů aplikací

Impact analyzuje, jak doba načítání a další vlastnosti ovlivňují míru konverze pro různé části aplikace. Přesněji řečeno, zjistí, jak **jakákoli dimenze** zobrazení **stránky**, **vlastní události**nebo **požadavku** ovlivňuje použití jiného zobrazení **stránky** nebo vlastní **události**. 

![Nástroj pro dopad](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Stále si nejste jisti, co impact dělá?

Jedním ze způsobů, jak si myslet, že impact je konečný nástroj pro vyrovnání argumentů s někým ve vašem týmu o tom, jak pomalost v některých aspektech vašeho webu ovlivňuje, zda uživatelé zůstanou. Zatímco uživatelé mohou tolerovat určité množství pomalosti, Impact vám poskytuje přehled o tom, jak nejlépe vyvážit optimalizaci a výkon, abyste maximalizovali konverzi uživatelů.

Ale analýza výkonu je jen podmnožinou schopností Impactu. Vzhledem k tomu, že impact podporuje vlastní události a dimenze, odpovídání na otázky, jako je to, jak volba prohlížeče uživatele koreluje s různými mírami konverze, jsou vzdáleny jen pár kliknutí.

![Konverze snímků obrazovky podle prohlížečů](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Prostředek Application Insights musí obsahovat zobrazení stránek nebo vlastní události, aby bylo nutné používat nástroj Dopad. [Přečtěte si, jak nastavit aplikaci tak, aby automaticky shromažďovala zobrazení stránek pomocí sady Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md). Také mějte na paměti, že vzhledem k tomu, že analyzujete korelaci, záleží na velikosti vzorku.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Má doba načítání stránky vliv na to, kolik lidí na mé stránce konvertuje?

Chcete-li začít odpovídat na otázky nástrojem dopad, zvolte počáteční zobrazení stránky, vlastní událost nebo požadavek.

![Nástroj pro dopad](./media/usage-impact/0002-dropdown.png)

1. V rozevíracím seznamu Pro zobrazení stránky vyberte zobrazení **stránky.**
2. Ponechte **analýzu, jak jeho** rozevírací seznam na výchozí výběr doba **trvání** (V tomto kontextu **Doba trvání** je alias pro čas **načítání stránky**.)
3. Pokud **chcete, aby to mělo vliv na použití** rozevíracího souboru, vyberte vlastní událost. Tato událost by měla odpovídat prvku uživatelského rozhraní v zobrazení stránky, které jste vybrali v kroku 1.

![Snímek obrazovky s výsledky](./media/usage-impact/0003-results.png)

V tomto případě, když doba načítání **stránky produktu** zvyšuje míru konverze na kliknutí na **nákup produktu,** klesá. Na základě výše uvedeného rozdělení by optimální doba načítání stránky 3,5 sekundy mohla být zaměřena na dosažení potenciálního 55% míry konverze. Další vylepšení výkonu ke snížení doby načítání pod 3,5 sekundy aktuálně nekorelují s dalšími výhodami převodu.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Co když sleduji zobrazení stránek nebo načítám časy vlastními způsoby?

Impact podporuje standardní i vlastní vlastnosti a měření. Použij, co chceš. Místo doby trvání použijte filtry na primární a sekundární události získat konkrétnější.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Konvertují uživatelé z různých zemí nebo oblastí různým tempem?

1. V rozevíracím seznamu Pro zobrazení stránky vyberte zobrazení **stránky.**
2. Zvolte "Země nebo oblast" v **analýze toho, jak její** rozbalovací nabídky
3. Pokud **chcete, aby to mělo vliv na použití** rozevíracího souboru, vyberte vlastní událost, která odpovídá prvku uživatelského rozhraní v zobrazení stránky, které jste zvolili v kroku 1.

V tomto případě se výsledky již nevejdou do souvislého modelu osy x, jako tomu bylo v prvním příkladu. Místo toho je zobrazena vizualizace podobná segmentované trychtýři. Seřazením podle **využití** zobrazíte variantu konverze na vlastní událost na základě země nebo oblasti.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Jak nástroj dopad vypočítá tyto míry konverze?

Pod kapotou se nástroj Impact spoléhá na [Pearsonův korelační koeficient](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Výsledky jsou vypočítány mezi -1 a 1 s -1 představující zápornou lineární korelaci a 1 představující kladnou lineární korelaci.

Základní rozpis fungování analýzy dopadů je následující:

Let _A_ = zobrazení hlavní stránky/ vlastní událost/požadavek, který vyberete v prvním rozevíracím seznamu. (**Pro zobrazení stránky**).

Umožňuje _B_ = sekundární zobrazení stránky nebo vlastní událost, kterou vyberete (**má vliv na použití**).

Dopad se podívá na ukázku všech relací od uživatelů ve vybraném časovém rozsahu. Pro každou relaci hledá každý výskyt _A_.

Relace jsou pak rozděleny do dvou různých druhů _podrem_ na základě jedné ze dvou podmínek:

- Převedená dílčí relace se skládá z relace končící událostí _B_ a zahrnuje všechny události _A,_ ke kterým dojde před _B_.
- Nepřevedená podrelace nastane, když dojde ke všem _A_'s bez terminálu _B_.

Způsob výpočtu dopadu se nakonec liší v závislosti na tom, zda analyzujeme podle metriky nebo dimenze. Pro metriky jsou zprůměrovány všechny _a_v dílčí relaci. vzhledem k tomu, že pro rozměry přispívá hodnota každého _A_ _1/N_ k hodnotě přiřazené _b,_ kde _N_ je číslo _A_v podrelaci.

## <a name="next-steps"></a>Další kroky

- Chcete-li povolit možnosti využití, začněte odesílat [vlastní události](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [zobrazení stránek](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud už vlastní události nebo zobrazení stránek odesíláte, prozkoumejte nástroje využití a zjistěte, jak uživatelé vaši službu používají.
    - [Trychtýře](usage-funnels.md)
    - [Uchovávání](usage-retention.md)
    - [Toky uživatele](usage-flows.md)
    - [Workbooks](../../azure-monitor/app/usage-workbooks.md)
    - [Přidání kontextu uživatele](usage-send-user-context.md)
