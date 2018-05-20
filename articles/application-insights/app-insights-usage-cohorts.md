---
title: Kohorty využití služby Azure Application Insights | Microsoft Docs
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
ms.openlocfilehash: f8d566f552c86f749b914ffed70512209ad76ab7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights kohorty

Kohorty je sada uživatelů, relací, události nebo operace, které mají něco společné. Ve službě Azure Application Insights jsou definovány kohorty dotaz analytics. V případech, kdy máte k analýze konkrétní sada uživatelů nebo události opakovaného kohorty můžete poskytují větší flexibilitu, jak vyjádřit přesně sadu, do které vás zajímají.

![Podokno kohorty](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty versus základní filtry

Kohorty používají způsoby podobná filtry. Ale kohorty Definice jsou integrované z vlastní analytické dotazy, proto jsou mnohem víc přizpůsobivé a komplexní. Na rozdíl od filtry můžete uložit kohorty, ostatní členové týmu je opětovně použít.

Můžete třeba definovat kohorty uživatelů, kteří mají o novou funkci ve vaší aplikaci. Tato kohorty můžete uložit v prostředku Application Insights. Je snadné k analýze této uložené skupiny konkrétním uživatelům v budoucnu.

> [!NOTE]
> Po vytváří, kohorty jsou dostupné z nástrojů pro uživatele, relace, události a toků uživatele.

## <a name="example-engaged-users"></a>Příklad: Pověření uživatele

Váš tým definuje aktivnější uživatele jako každý, kdo používá aplikace pět nebo vícekrát v daném měsíci. V této části definujete kohorty tyto aktivnější uživatelů.

1. Otevřete nástroj kohorty.

2. Vyberte **Galerie šablon** kartě. Zobrazí soubor šablony pro různé kohorty.

3. Vyberte **Angažovaní uživatelé – dnů použitím**.

    Existují tři parametry pro tuto kohorty:
    * **Aktivity**, kde si zvolíte, které události a zobrazení stránek se počítají jako "využití."
    * **Období**, definice v měsíci.
    * **UsedAtleastCustom**, počet nezdařených pokusů o uživatelé muset použít něco v období počítat jako pověření.

4. Změna **UsedAtleastCustom** k **5 + dnů**a nechat **období** na výchozí hodnotu 28 dnů.

    ![Angažovaní uživatelé](.\media\app-insights-usage-cohorts\003.png)

    Teď tento kohorty představuje všechna ID uživatelů s vlastní událost nebo stránka zobrazení na 5 dní v minulosti oddělit 28.

5. Vyberte **Uložit**.

   > [!TIP]
   >  Název, jako je třeba poskytnout vaše kohorty "uživatelé Engaged (5 + dnů)." Uložte jej do "Sestavy" nebo "Sdílené sestavy," v závislosti na tom, zda chcete, aby ostatní uživatelé, kteří mají přístup k tomuto prostředku Application Insights zobrazíte tuto kohorty.

6. Vyberte **zpět do Galerie**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co lze provádět pomocí této kohorty?

Otevřete nástroj pro uživatele. V **zobrazit** rozevíracího seznamu vyberte kohorty jste vytvořili v části **uživatelů, kteří patří do**.

Nástroj pro uživatele je nyní filtruje tak, aby tento kohorty uživatelů:

![Uživatelé podokně filtruje tak, aby konkrétní kohorty](.\media\app-insights-usage-cohorts\004.png)

Několik důležitých věcí Všimněte:
* Tato sada nelze vytvořit pomocí normální filtry. Pokročilejší logice datum.
* Tato kohorty lze dále filtrovat pomocí filtrů normální v nástroji pro uživatele. Proto i když kohorty je definována v systému windows 28 dnů, můžete upravit časové rozmezí v nástroji Uživatelé 30, 60 nebo 90 dny.

Tyto filtry podporují sofistikovanější otázky, které jsou možné express prostřednictvím Tvůrce dotazů. Příkladem je _lidé, kteří se zabývají posledních 28 dnů. Na osoby stejné chování za posledních 60 dnů?_

## <a name="example-events-cohort"></a>Příklad: Události kohorty

Můžete také nastavit kohorty události. V této části definujete kohorty událostí a zobrazení stránek. Pak můžete zjistit, jak použít z dalších nástrojů. Tato kohorty může definovat sadu událostí, které váš tým zvažuje _active využití_ nebo sadu související s některé nové funkce.

1. Otevřete nástroj kohorty.

2. Vyberte **Galerie šablon** kartě. Pro různé kohorty uvidíte kolekci šablon.

3. Vyberte **výběr události**.

    ![Snímek obrazovky výběru události](.\media\app-insights-usage-cohorts\006.png)

4. V **aktivity** rozevíracího seznamu vyberte události, které mají být v kohorty.

5. Uložení kohorty a pojmenujte ho.

## <a name="example-active-users-where-you-modify-a-query"></a>Příklad: Aktivních uživatelů, kde změna dotazu

Předchozí dva kohorty byly definovány pomocí rozevíracího seznamu polí. Ale můžete také definovat kohorty pomocí analytické dotazy pro celkový počet flexibilitu. Pokud chcete zobrazit, jak vytvořit kohorty uživatelů z Spojené království.

![Animovaný bitové kopie, použijte nástroj kohorty s návodem](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Otevřete nástroj kohorty, vyberte **Galerie šablon** a vyberte **prázdné uživatelé kohorty**.

    ![Prázdné uživatelé kohorty](.\media\app-insights-usage-cohorts\001.png)

    Existují tři části:
    * Markdownu text oddílu, kde kohorty podrobněji popisují ostatní uživatelé ve vašem týmu.

    * Oddíl parametry, kdy vytvoříte vlastní parametry, jako je **aktivity** a jiných polích z předchozí dva příklady.

    * Dotaz části, ve které definujete kohorty pomocí dotaz analytics.

    V části dotazu je [zápisu dotaz analytics](https://docs.loganalytics.io/index). Dotaz vybere určitou sadu řádků, které popisují kohorty, které chcete definovat. Nástroj kohorty pak implicitně přidá "| shrnout pomocí funkce user_Id"klauzule dotazu. Tato data zobrazení náhledu dole dotazu v tabulce, abyste měli jistotu, že dotaz vrací výsledky.

    > [!NOTE]
    > Pokud nevidíte dotazu, zkuste upravit velikost oddílu a nastavit jej jako vyšší odhalit dotazu. Animovaný GIF na začátku této části ukazuje změnu velikosti chování.

2. Zkopírujte a vložte následující text do editoru dotazů:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Vyberte **spuštění dotazu**. Pokud nevidíte ID uživatele uvedené v tabulce, změňte na zemi, kde vaše aplikace používá uživatelů.

4. Uložte a pojmenujte kohorty.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

_I jste definovali kohorty uživatelů z určité zemi. Při porovnání I tato kohorty v nástroji Uživatelé právě nastavení filtru na země, zobrazuje různé výsledky. Proč?_

Kohorty a filtry se liší. Předpokládejme, že máte kohorty uživatelů z Spojené království (definovanou jako v předchozím příkladu), a porovnejte jeho výsledky nastavení filtru "zemi nebo oblast = Spojené království."

* Verze kohorty zobrazuje všechny události od uživatelů, kteří odeslaný Spojené království v aktuální časové rozmezí jedné nebo více událostí. Pokud je rozdělit podle země nebo oblasti, zobrazí pravděpodobně mnoha jiných zemí a oblastí.
* Verze filtry se zobrazí pouze události z Spojené království. Ale pokud je rozdělit podle země nebo oblasti, zobrazí jenom Spojené království.

## <a name="learn-more"></a>Další informace
- [Analýza dotazovací jazyk](https://go.microsoft.com/fwlink/?linkid=856587)
- [Uživatele, relace, události](app-insights-usage-segmentation.md)
- [Toky uživatele](app-insights-usage-flows.md)
- [Přehled využití](app-insights-usage-overview.md)