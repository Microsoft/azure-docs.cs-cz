---
title: Řešení potíží s nástrojů analýzy chování uživatelů ve službě Azure Application Insights
description: Průvodce odstraňováním potíží – analýza webu a využití aplikace pomocí Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7da0717273892893bec03c164b9b297f28e5218d
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995542"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Řešení potíží s nástrojů analýzy chování uživatelů ve službě Application Insights
Máte otázky týkající [nástrojů analýzy chování uživatelů ve službě Application Insights](app-insights-usage-overview.md): [uživatelé, relace, události](app-insights-usage-segmentation.md), [trychtýře](usage-funnels.md), [toky uživatelů](app-insights-usage-flows.md), [Uchování](app-insights-usage-retention.md), nebo kohorty? Tady jsou odpovědi.

## <a name="counting-users"></a>Počítání uživatelů
**Nástroje analýzy chování uživatelů ukazují, že aplikace má jeden uživatel nebo relace, ale vím, že aplikace má mnoho uživatelů a relací. Jak můžete opravit tyto nesprávné počty?**

Mají všechny telemetrických událostí ve službě Application Insights [ID anonymního uživatele](application-insights-data-model-context.md) a [ID relace](application-insights-data-model-context.md) jako dvě z jejich standardní vlastnosti. Ve výchozím nastavení všechny nástrojů pro analýzu využití počet uživatelů a relací v závislosti na tyto identifikátory. Pokud tyto standardní vlastnosti nejsou připravují se jedinečné identifikátory pro každého uživatele a relace z vaší aplikace, zobrazí se vám nesprávnému počtu uživatelů a relací v nástrojích pro analýzu využití.

Pokud monitorujete webové aplikace, nejjednodušším řešením je přidat [Application Insights JavaScript SDK](app-insights-javascript.md) do vaší aplikace a ujistěte se, že se načíst fragment skriptu na každé stránce, kterou chcete monitorovat. Sada JavaScript SDK automaticky generuje anonymní uživatel a ID relace a potom naplní telemetrické události mají tyto identifikátory jsou odeslané z vaší aplikace.

Pokud monitorujete webové služby (bez uživatelského rozhraní), [vytvořit inicializátor telemetrie, který naplní vlastnosti anonymního uživatelského ID a relace ID](app-insights-usage-send-user-context.md) podle svojí služby správy jedinečných uživatelů a relací.

Pokud vaše aplikace odesílá [ověřit ID uživatelů](app-insights-api-custom-events-metrics.md#authenticated-users), můžete počítat ověřeného uživatele na základě ID v nástroji pro uživatele. V rozevíracím seznamu "Show" zvolte možnost "Authenticated users".

Nástroje analýzy chování uživatelů v současnosti nepodporujeme počítání uživatelů nebo relace na základě vlastností než ID anonymního uživatele, ID ověřeného uživatele nebo ID relace.

## <a name="naming-events"></a>Pojmenování události
**Moje aplikace má tisíce zobrazení různé stránky a názvy vlastních událostí. Je těžké k rozlišení mezi nimi a analytické nástroje chování uživatele často přestat reagovat. Jak můžete tyto zásady problémy řešit?**

Názvy vlastních událostí a zobrazení stránky se používají v nástroje analýzy chování uživatelů. Názvy událostí a je velmi důležité k získání hodnoty z těchto nástrojů. Cílem je rovnováhu mezi s názvy příliš málo, příliš obecného ("kliknutí na tlačítko") a s příliš mnoho, zbytečně konkrétní názvy ("na kliknutí na tlačítko Upravit http://www.contoso.com/index").

Žádné změny k zobrazení stránky a názvy vlastních událostí, které vaše aplikace odesílá, budete muset změnit zdrojový kód a opětovné nasazení vaší aplikace. **Veškerá telemetrie data ve službě Application Insights je uložena po dobu 90 dnů a nelze ji odstranit**, takže změny provedené názvy událostí bude trvat plně manifest 90 dní. Za 90 dnů po provedení změny názvu názvy staré a nové události se zobrazí v telemetrii, tak upravte dotazy a komunikaci v rámci vašich týmů odpovídajícím způsobem.

Pokud vaše aplikace odesílá příliš mnoho názvů zobrazení stránky, zkontrolujte, zda jsou tyto názvy zobrazení stránek se ručně zadat v kódu nebo pokud se už odesíláno automaticky Application Insights JavaScript SDK:

* Je-li zobrazit názvy stránek jsou ručně zadané v kódu pomocí [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), změňte název, který má být specifické pro less. Vyhněte se běžných chyb, jako je uvedení adresu URL název zobrazení stránky. Místo toho použít parametr adresy URL `trackPageView` rozhraní API. Přesuňte další podrobnosti do vlastní vlastnosti názvu zobrazení stránky.

* Pokud sadu Application Insights JavaScript SDK automaticky odesílá názvy zobrazení stránek, můžete změnit názvy vaše stránky nebo přepnout na odesílání ručně názvy zobrazení stránek. Sada SDK odesílá [název](https://developer.mozilla.org/docs/Web/HTML/Element/title) každé stránky jako název zobrazení stránky, ve výchozím nastavení. Můžete změnit nadpisy obecnější, ale mějte na paměti optimalizace pro vyhledávací weby a další dopady, které tato změna může mít. Ruční zadání zobrazení stránky s názvy `trackPageView` API přepíše shromažďují automaticky názvy, můžete odeslat další obecné názvy v telemetrii beze změny titulů.   

Pokud vaše aplikace odesílá příliš mnoho názvů vlastních událostí, změňte název v byl kód méně konkrétní. Znovu Vyhněte se vložení adresy URL a jiných na stránku nebo dynamických informací v názvy vlastních událostí přímo. Místo toho přesunout tyto podrobnosti do vlastních vlastností vlastní událost se `trackEvent` rozhraní API. Například namísto z `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, doporučujeme něco jako `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Další postup

* [Přehled nástrojů analýzy chování uživatelů](app-insights-usage-overview.md)

## <a name="get-help"></a>Podpora
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

