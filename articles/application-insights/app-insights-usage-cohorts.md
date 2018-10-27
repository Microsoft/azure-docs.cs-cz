---
title: Kohorta využití služby Azure Application Insights | Dokumentace Microsoftu
description: Analyzovat různé skupiny nebo uživatelé, relace, události nebo operací, které mají něco společné
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 35737b91d8ef0b58223f51530f56408df1c7eb8e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138788"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorty

Kohorta je sada uživatelů, relací, událostí nebo operací, které mají něco společné. Ve službě Azure Application Insights jsou definovány kohorty analytický dotaz. V případech, kdy máte k analýze konkrétní sadu uživatelů nebo událostí opakovaně, kohorty vám může poskytnout větší flexibilitu při express přesně sady, které vás zajímají.

![Podokno kohorty](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorta oproti základní filtry

Způsoby, podobné filtry se používají kohorty. Ale kohorty Definice jsou sestaveny z vlastních analytických dotazů, aby byly snáze přizpůsobitelné a složité. Na rozdíl od filtry můžete uložit kohorty, ostatní členové týmu můžete je znovu použít.

Můžete třeba definovat kohorta uživatelů, kteří jste zkusili novou funkci ve vaší aplikaci. Tato kohorta můžete uložit do prostředku Application Insights. Je snadné analyzovat tato skupina uložena konkrétních uživatelů v budoucnosti.

> [!NOTE]
> Po vytvoření, jsou k dispozici v nástrojích uživatelé, relace, události a toky uživatelů kohorty.

## <a name="example-engaged-users"></a>Příklad: Znamenají Angažovaní uživatelé

Váš tým definuje Angažované uživatele jako každý, kdo používá vaše aplikace pět nebo víckrát v daném měsíci. V tomto oddílu definujete kohortu z těchto znamenají angažovaní uživatelé.

1. Otevřete nástroji kohorty.

2. Vyberte **Galerie šablon** kartu. Kolekce šablon, které se zobrazí pro různé kohorty.

3. Vyberte **znamenají Angažovaní uživatelé--dny, používá**.

    Existují tři parametry pro tato kohorta:
    * **Aktivity**, kde vyberete, které události nebo zobrazení stránek se počítají jako "použití."
    * **Období**, definice za měsíc.
    * **UsedAtleastCustom**, počet pokusů o uživatelé muset použít něco v období počítat jako zapojení.

4. Změna **UsedAtleastCustom** k **víc než 5 dnů**a nechat **období** na výchozí hodnotu 28 dnů.

    ![Znamenají angažovaní uživatelé](.\media\app-insights-usage-cohorts\003.png)

    Nyní tato kohorta představuje všechna ID uživatelů odesílat všechny vlastní události nebo zobrazení stránky na 5 dnů v minulosti oddělení 28.

5. Vyberte **Uložit**.

   > [!TIP]
   >  Pojmenujte vaši kohortu, jako je třeba "Engaged uživatelé (víc než 5 dnů)." Uložte ho do "Zprávy" nebo "Sdílené sestavy," v závislosti na tom, zda chcete, aby ostatní uživatele, kteří mají přístup k tomuto prostředku Application Insights, chcete-li zobrazit tato kohorta.

6. Vyberte **zpět do Galerie**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co můžete dělat pomocí tato kohorta?

Otevřete nástroj Uživatelé. V **zobrazit** rozevíracího seznamu vyberte kohortu vytvořené v rámci **uživatelům patřícím do**.

Nástroj Uživatelé je vyfiltruje a tato kohorta uživatelů:

![Uživatelé podokně filtrovat na konkrétní kohorty](.\media\app-insights-usage-cohorts\004.png)

Několik důležitých věcí a Všimněte si:
* Tuto sadu nelze vytvořit běžné filtry. Logika datum je složitější.
* Tato kohorta můžete dále filtrovat pomocí běžné filtry v nástroj Uživatelé. Takže i když se kohorta je definován v systému windows 28 dní, můžete upravit časový rozsah v nástroj Uživatelé 30, 60 nebo 90 dny.

Tyto filtry podporují složitější dotazy, které nelze vyjádřit pomocí Tvůrce dotazů. Příkladem je _uživatelů, kteří se zabývají posledních 28 dnů. Osoby stejné chování za posledních 60 dnů?_

## <a name="example-events-cohort"></a>Příklad: Kohorta událostí

Můžete provést také kohorta událostí. V tomto oddílu definujete kohortu události a zobrazení stránek. Pak naleznete v tématu Jak používat z dalších nástrojů. Tato kohorta může definují sadu událostí, které váš tým bude považovat za _aktivní využití_ nebo sada související s některé nové funkce.

1. Otevřete nástroji kohorty.

2. Vyberte **Galerie šablon** kartu. Zobrazí se kolekce šablon, které pro různé kohorty.

3. Vyberte **výběr události**.

    ![Snímek obrazovky výběru události](.\media\app-insights-usage-cohorts\006.png)

4. V **aktivity** rozevíracího seznamu vyberte události, které mají být v kohortě.

5. Uložit kohortu a pojmenujte ho.

## <a name="example-active-users-where-you-modify-a-query"></a>Příklad: Aktivních uživatelů, kde můžete upravit dotaz

Předchozí dva kohorty byly definovány pomocí rozevíracího seznamu polí. Ale můžete také definovat kohorty pomocí analytických dotazů pro celkovou flexibilitu. Pokud chcete zobrazit, jak vytvořit kohorta uživatelů z Spojeném království.

![Animovaný obrázek provede užívání nástroji kohorty](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Otevřete nástroji kohorty, vyberte **Galerie šablon** kartu a vyberte **kohorta uživatelů prázdnou**.

    ![Kohorta uživatelů prázdné](.\media\app-insights-usage-cohorts\001.png)

    Existují tři části:
    * Markdownu text oddílu, ve kterém popíšete kohorta podrobněji pro ostatní uživatele ve vašem týmu.

    * Parametry části, kde provádět vlastní parametry, jako je **aktivity** a další pole rozevíracího seznamu v předchozích dvou příkladech.

    * Oddíl dotazu Tady můžete definovat kohortu pomocí dotazu analytics.

    V části dotazu je [napsat dotaz analytics](/azure/kusto/query). Dotaz vybere určité sady řádků, které popisují, které chcete definovat kohortu. Nástroji kohorty implicitně přidá "| vytvořit souhrn podle user_Id"klauzule dotazu. Tato data je zobrazen pod dotaz v tabulce, abyste měli jistotu, že dotaz vrací výsledky.

    > [!NOTE]
    > Pokud se dotaz, zkuste změnit velikost oddílu, který má byl vyšší a zobrazit dotaz. Animovaný obrázek GIF na začátku této části ukazuje chování změny velikosti.

2. Zkopírujte a vložte následující text do editoru dotazů:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Vyberte **spuštění dotazu**. Pokud nevidíte ID uživatele uvedené v tabulce, změňte zemi, kde má vaše aplikace uživatelům.

4. Uložte a pojmenujte kohorty.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

_Můžu jste definovali kohorta uživatelů v určité zemi. Můžu porovnat tato kohorta nástroji pro uživatele na pouhé nastavení filtru na země, zobrazuje různé výsledky. Proč?_

Kohorta a filtry se liší. Předpokládejme, že máte kohorta uživatelů z Spojené království (definované jako v předchozím příkladu), a porovnat výsledky nastavení filtru "země nebo oblast = Spojeném království."

* Kohorta verze se zobrazí všechny události od uživatelů, kteří odeslaný Spojené království v aktuálním časovém rozsahu jednoho nebo více událostí. Pokud je rozdělte podle země nebo oblasti, uvidíte pravděpodobně mnoha zemích a oblastech.
* Verze filtry zobrazuje jenom události z Spojeném království. Ale pokud je rozdělte podle země nebo oblasti, se zobrazí pouze Spojeném království.

## <a name="learn-more"></a>Další informace
- [Dotazovací jazyk Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
- [Uživatelé, relace, události](app-insights-usage-segmentation.md)
- [Toky uživatelů](app-insights-usage-flows.md)
- [Přehled využití](app-insights-usage-overview.md)