---
title: Analýza uživatelů, relací a událostí ve službě Azure Application Insights | Microsoft docs
description: Demografické údaje analýzy uživatelů vaší webové aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 3ac738d6b3c0f1f3579a9b644a03a01f1509173a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analýza uživatelů, relací a událostí ve službě Application Insights

Zjistěte, kdy lidí používá vaši webovou aplikaci, jaké stránky, že jste nejvíce zajímá, kde jsou vaši uživatelé nachází, a jaké prohlížeče a operační systémy používají. Analyzovat obchodní a využití telemetrie pomocí [Azure Application Insights](app-insights-overview.md).

![Snímek obrazovky Application Insights uživatelů](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>Začínáme

Pokud nevidíte ještě data uživatelů, relací nebo okna události na portálu služby Application Insights [zjistěte, jak začít pracovat s nástroji využití](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Nástroj segmentaci uživatelů, relací a události

Tři z okna využití použít nástroj stejné k nařezání a rozčlenění telemetrie z vaší webové aplikace z tři perspektivy. Filtrování a rozdělení dat, můžete odkrýt přehledy o relativní použití různých stránek a funkcí.

* **Nástroj Uživatelé**: kolik lidí používá vaši aplikaci a jejich funkce.  Uživatelé, se počítají pomocí anonymní ID uložené v prohlížeči soubory cookie. Jednoho uživatele, kteří používají různé prohlížeče nebo počítače se budou počítat jako více než jeden uživatel.
* **Nástroj relací**: kolik relací aktivity uživatelů, zahrnuli určité stránky a funkce vaší aplikace. Relace se počítá po půl hodiny nečinnosti uživatele nebo po 24 hodinách nepřetržitého používání.
* **Nástroj pro události**: jak často se používají určité stránky a funkce vaší aplikace. Zobrazení stránky se počítá při prohlížeč načte stránky z vaší aplikace, pokud máte [instrumentovány ho](app-insights-javascript.md). 

    Vlastní události představuje jeden výskyt něco stane v aplikaci, často zásah uživatele takto: klikněte na tlačítko nebo na dokončení některých úloh. Vložení kódu v aplikaci, kterou chcete [generovat vlastní události](app-insights-api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Dotaz na určité uživatele

Prozkoumejte různé skupiny uživatelů úpravou možností dotazu v horní části nástroj Uživatelé:

* Zobrazit: Zvolte kohorty uživatelů k analýze.
* Kdo používá: Zvolte vlastní události a stránky zobrazení.
* Během: Vyberte časový rozsah.
* Podle: Volba způsobu bucket dat, buď v časovém intervalu, nebo jinou vlastnost třeba prohlížeče nebo města.
* Rozdělit: Zvolte vlastnosti a které segmentu nebo rozdělení data. 
* Přidání filtrů: Omezte dotaz na určité uživatele, relace nebo události na základě jejich vlastností, jako je například prohlížeč nebo města. 
 
## <a name="saving-and-sharing-reports"></a>Ukládání a sdílení sestavy 
Uživatelé sestavy, privátní vám v části Moje sestavy nebo sdílené můžete uložit se všemi ostatními s přístupem k tomuto prostředku Application Insights v části sdílené sestavy.

Chcete-li sdílet odkaz na sestavu uživatelů, relací a událostí; Klikněte na tlačítko **sdílené složky** na panelu nástrojů, pak zkopírujte odkaz.

Chcete-li sdílet kopii data v sestavě uživatelů, relací a událostí; Klikněte na tlačítko **sdílené složky** na panelu nástrojů klikněte **ikona aplikace Word** vytvořit dokument aplikace Word s daty. Nebo klikněte na tlačítko **ikona aplikace Word** výše hlavního grafu.

## <a name="meet-your-users"></a>Splňují vaše uživatele

**Splňují vaše uživatele** části se zobrazují informace o pět ukázkových uživatelů odpovídala aktuálního dotazu. Vzhledem k tomu a zkoumat chování jednotlivce, kromě agregace, může poskytovat přehled o tom, jak lidé ve skutečnosti používají vaši aplikaci.

## <a name="next-steps"></a>Další postup

- Pokud chcete povolit použití možnosti, zahájit odesílání [vlastních událostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) nebo [stránky zobrazení](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Pokud jste již odeslat vlastní události nebo zobrazení stránky, prozkoumejte využití nástroje se dozvíte, jak uživatelé používat služby.
    - [Trychtýře](usage-funnels.md)
    - [Uchování](app-insights-usage-retention.md)
    - [Toky uživatele](app-insights-usage-flows.md)
    - [Workbooks](app-insights-usage-workbooks.md)
    - [Přidat uživatelský kontext](app-insights-usage-send-user-context.md)