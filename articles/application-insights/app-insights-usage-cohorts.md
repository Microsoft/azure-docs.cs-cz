---
title: Aplikace Azure Statistika využití kohorty | Microsoft docs
description: Analýza různé skupiny nebo uživatele, relace, události nebo operace, které mají něco společné
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights kohorty

Kohorty je sada uživatelů, relací, události nebo operace, které mají něco společné. Ve službě Azure Application Insights kohorty definované dotaz Analytics. Pokud se přistihnete analýza konkrétní skupinu uživatelů nebo události opakovaného kohorty vám může ještě větší flexibilitu při express přesně sadu, do které vás zajímají.

![Podokno kohorty](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty versus základní filtry

Při kohorty používají podobným způsobem jako filtry, umožňuje skutečnost, že definice kohorty je sestaven z vlastní analytické dotazy mají být mnohem víc přizpůsobivé a komplexní. Na rozdíl od filtry můžete uložit kohorty, ostatní členové týmu je opětovně použít.

Můžete třeba definovat kohorty uživatelů, kteří mají o novou funkci ve vaší aplikaci. Pomocí této kohorty uloží do zdroje Application Insights umožňuje analýza konkrétní skupiny uživatelů v budoucnosti jedním kliknutím ji okamžitě.

> [!NOTE]
> Po vytvoření kohorty jsou dostupné z nástrojů pro uživatele, relace, události a toků uživatele.

## <a name="example-engaged-users"></a>Příklad: pověření uživatele

Váš tým definuje aktivnější uživatele jako každý, kdo používá aplikace pět nebo vícekrát v daném měsíci. Umožňuje definovat kohorty tyto aktivnější uživatelů.

1. Otevřete **kohorty** nástroj.

2. Klikněte na tlačítko **Galerie šablon** kartě. Zde najdete kolekci šablon pro různé kohorty.

3. Zvolte **pověření uživatele** – dnů používá ".

    Existují tři parametry pro tuto kohorty:
      * **Aktivity** které umožňují zvolit, které události a zobrazení stránek měli počítat jako "využití".
      * **Období** definici v měsíci.
      * **UsedAtleastCustom** počet, kolikrát je nutné použít něco v období počítat jako uživatel s angažovaní.

4. Změna **UsedAtleastCustom** "5 + dní" a nechte **období** na výchozí 28 dnů.

    ![Image](.\media\app-insights-usage-cohorts\003.png)

    Teď tento kohorty představuje všechna ID uživatelů, které byly odeslány pomocí vlastní událost nebo stránka zobrazení na pět dní samostatné v posledních 28 dnů.

5. Klikněte na **Uložit**.

   > [!TIP]
   >  Název, jako je třeba poskytnout vaše kohorty "Engaged uživatelé (5 + ve dnech)" a uložte jej do "Sestavy" nebo "Sdílené sestavy" v závislosti na Pokud chcete ostatním uživatelům s přístupem k tomuto prostředku Statistika aplikací tento kohorty.

6. Klikněte na tlačítko **zpět do Galerie**.

### <a name="what-can-you-do-with-this-cohort"></a>Co se děje s Tento kohorty?

Otevřete **uživatelé** nástroj > v **zobrazit** rozevírací > zvolte kohorty jste vytvořili v části **uživatelů, kteří patří do...**

Nástroj pro uživatele je nyní filtruje tak, aby tento kohorty uživatelů:

![Uživatelé podokně filtruje tak, aby konkrétní kohorty](.\media\app-insights-usage-cohorts\004.png)

Několik důležitých věcí Všimněte:
   * To je sada vytvořených nelze prostřednictvím normální filtry. Pokročilejší logice datum.
   * Dále můžete filtrovat tento kohorty pomocí normální filtrů v nástroji pro uživatele. Proto při kohorty je definována v systému windows 28 dnů, můžete upravit časové rozmezí v nástroji Uživatelé 30, 60 nebo 90 dny. 

To umožňuje sofistikovanější ptají se třeba: _pro uživatele, kteří se zabývají posledních 28 dnů, na osoby stejné chování za posledních 60 dnů?_ který by jinak nebylo možné vyjádřit pomocí Tvůrce dotazů.

## <a name="example-events-cohort"></a>Příklad: události kohorty

Můžete také nastavit kohorty události. Umožňuje definovat kohorty událostí a zobrazení stránek a poté zjistit, jak použít z dalších nástrojů. To může být užitečné k definování sadu událostí, které váš tým zvažuje _active využití_, nebo k definování sady událostí souvisejících s některé nové funkce.

1. Otevřete **kohorty** nástroj.

2. Klikněte **Galerie šablon** kartě. Zde najdete kolekci šablon pro různé kohorty.

3. Zvolte **výběr události**.

    ![Výběr snímek událostí.](.\media\app-insights-usage-cohorts\006.png)

4. V **aktivity** rozevíracího seznamu, vyberte události, kterou chcete být v kohorty

5. Uložení kohorty a pojmenujte ho.

## <a name="example-active-users-where-you-modify-query"></a>Příklad: aktivních uživatelů, kde můžete upravit dotaz

Předchozí dva kohorty byly definovány pomocí rozevíracích seznamů. Ale jsme můžete také definovat kohorty s dotazy Analytics celkový flexibilitu. Podívejme se, jak vytvořením kohorty uživatelů z Spojené království.

![Animovaný bitové kopie, použijte nástroj kohorty s návodem](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Otevřete **kohorty** nástroj > klikněte na tlačítko **Galerie šablon** kartě > zvolte **prázdné uživatelé kohorty**.

    ![Prázdné uživatelé kohorty](.\media\app-insights-usage-cohorts\001.png)

    Existují tři části:
       * Část textu Markdownu, kde popisují kohorty podrobněji ostatní uživatelé ve vašem týmu.

       * Oddílu parametry, můžete vytvořit vlastní parametry, jako **aktivity** a dalších rozevíracích seznamů z předchozí dva příklady.

       * Dotaz oddílu, který používáte k definování kohorty pomocí dotazu Analytics.

    V části dotazu je [zápisu dotaz Analytics](https://docs.loganalytics.io/index) , vybere určitou sadu řádků, které popisují kohorty chcete definovat. Nástroj kohorty pak implicitně přidá "| shrnout pomocí funkce user_Id"klauzule dotazu. Toto zobrazení náhledu dole dotazu v tabulce, abyste měli jistotu, že dotaz vrací výsledky.

    > [!NOTE]
    > Pokud nevidíte dotazu, zkuste upravit velikost oddílu a nastavit jej jako vyšší odhalit dotazu. Animovaný GIF na začátku této části ukazuje změnu velikosti chování.

2. Zkopírujte vložte následující kód do editoru dotazů:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Klikněte na tlačítko **spuštění dotazu**. Měli byste vidět ID uživatele uvedené v tabulce. V opačném případě změňte na zemi, kde vaše aplikace používá uživatelů.

4. Uložte a pojmenujte kohorty.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

_I jste definovali kohorty uživatelů z určité zemi. Při porovnání I tato kohorty v nástroji Uživatelé právě nastavení filtru na dané země v nástroji pro uživatele, zobrazuje různé výsledky. Proč?_

Kohorty a filtry se liší. Předpokládejme, že máte kohorty uživatelů z Spojené království (definovanou jako výše uvedeném příkladu) a porovnání své výsledky nastavení filtru "zemi nebo oblast = Spojené království."

* Verze kohorty se zobrazí všechny události od uživatelů, kteří odeslali aspoň jednu událost z Spojené království v aktuální časové rozmezí. Pokud je rozdělit podle země nebo oblasti, pravděpodobně uvidíte mnoho jiných zemí a oblastí.
* Verze filtry se zobrazí pouze události z Spojené království. Vzhledem k tomu, pokud je rozdělit podle země nebo oblasti, uvidíte jenom Spojené království.

## <a name="learn-more"></a>Další informace
- [Analýza dotazovací jazyk](https://go.microsoft.com/fwlink/?linkid=856587)
- [Uživatelé, relace, události](app-insights-usage-segmentation.md)
- [Toky uživatele](app-insights-usage-flows.md)
- [Přehled využití](app-insights-usage-overview.md)