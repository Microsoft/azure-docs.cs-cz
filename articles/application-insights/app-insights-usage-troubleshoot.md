---
title: Řešení potíží s analýzy využití ve službě Azure Application Insights
description: Řešení potíží s Průvodce – analýza využití serveru a aplikace s Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin;daviste
ms.openlocfilehash: 654b99085c406f13fe95476457234761bf840422
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Řešení potíží s analýzy využití ve službě Application Insights
Máte otázky o [nástroje analýzy využití ve službě Application Insights](app-insights-usage-overview.md): [uživatele, relace, události](app-insights-usage-segmentation.md), [nálevky](usage-funnels.md), [uživatele toků](app-insights-usage-flows.md), [Uchování](app-insights-usage-retention.md), nebo kohorty? Zde jsou některé odpovědi.

## <a name="counting-users"></a>Počítání uživatelů
**Analýzy využití, nástrojů zobrazovat, že Moje aplikace má jeden uživatel nebo relaci, ale poznám, že aplikace má mnoho uživatelů nebo relací. Jak lze tyto nesprávné počty odstranit?**

Mají všechny události telemetrii ve službě Application Insights [ID anonymní uživatele](application-insights-data-model-context.md) a [ID relace](application-insights-data-model-context.md) jako dvě jejich standardních vlastností. Ve výchozím nastavení všechny nástroje analýzy využití počet uživatelů a relací v závislosti na tyto identifikátory. Pokud tyto standardní vlastnosti nejsou probíhá naplněný jedinečné ID pro každého uživatele a relace vaší aplikace, se zobrazí správný počet uživatelů a relací v nástrojích pro analýzy využití.

Pokud jste monitorování webové aplikace, nejsnazším řešením je přidat [Application Insights JavaScript SDK](app-insights-javascript.md) do vaší aplikace a ujistěte se, že je načtena fragment skriptu na každé stránce, kterou chcete sledovat. JavaScript SDK automaticky vygeneruje anonymní uživatel a ID relace a potom naplní telemetrické události v těchto ID, jak jste odeslali z vaší aplikace.

Pokud probíhá sledování webové služby (bez uživatelského rozhraní), [vytvořit inicializátoru telemetrických dat, který naplní vlastnosti anonymního uživatele ID a relace ID](app-insights-usage-send-user-context.md) podle vaší služby názory jedinečných uživatelů a relací.

Pokud vaše aplikace odesílá [ověřit ID uživatelů](app-insights-api-custom-events-metrics.md#authenticated-users), můžete počet ověřeného uživatele na základě ID v nástroji pro uživatele. V rozevírací nabídce "Zobrazit" zvolte "Authenticated users".

Nástroje pro analýzu využití není aktuálně podporován počítání uživatele nebo relací v závislosti na vlastnosti než ID anonymního uživatele, ID ověřeného uživatele nebo ID relace.

## <a name="naming-events"></a>Pojmenování události
**Moje aplikace má tisíce zobrazení různé stránky a názvy vlastních událostí. Je obtížné rozlišit mezi nimi a nástroje pro analýzu využití často přestat reagovat. Jak můžete tyto problémy pojmenování řešit?**

Zobrazení stránky a názvy vlastních událostí se používají v rámci nástrojů pro analýzu využití. Pojmenování události a je velmi důležité k získání hodnoty z těchto nástrojů. Cílem je rovnováhu mezi s názvy příliš málo, příliš obecný ("tlačítko kliknutí na") a má příliš mnoho, příliš konkrétní názvy ("na kliknutí na tlačítko Upravit http://www.contoso.com/index").

Žádné změny k zobrazení stránky a názvy vlastní události, které vaše aplikace odesílá, budete muset změnit zdrojový kód a znovu ho zaveďte vaší aplikace. **Všechny telemetrická data ve službě Application Insights se uchovávají po dobu 90 dnů a nelze ji odstranit**, takže změny názvy událostí bude trvat 90 dnů se plně projeví. Za 90 dní po provedení změny názvu názvy staré a nové události se zobrazí v telemetrii, proto upravte dotazy a komunikaci v rámci týmů, odpovídajícím způsobem.

Pokud vaše aplikace odesílá příliš mnoho názvů zobrazení stránky, zkontrolujte, zda jsou tyto názvy zobrazení stránky ručně zadaný v kódu nebo pokud jste se odešlou automaticky pomocí Application Insights JavaScript SDK:

* Pokud názvy zobrazení stránky jsou ručně zadané v kódu pomocí [ `trackPageView` rozhraní API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), změnit název, který má být méně přesný. Vyhněte se běžných chyb jako uvedení adresu URL názvu zobrazení stránky. Místo toho použít parametr adresy URL `trackPageView` rozhraní API. Další podrobnosti od názvu zobrazení stránky, přesuňte do vlastní vlastnosti.

* Pokud Application Insights JavaScript SDK je automaticky odesílá názvy zobrazení stránek, můžete změnit záhlaví stránky se nebo přepněte na ruční odeslání názvy zobrazení stránky. Odešle sadu SDK [název](https://developer.mozilla.org/docs/Web/HTML/Element/title) každé stránky jako název stránky zobrazení, ve výchozím nastavení. Můžete změnit nadpisy obecnější, ale mějte na paměti optimalizace pro vyhledávací weby a jiných dopadů, které by mohly mít tuto změnu. Ruční zadání zobrazení stránky s názvy `trackPageView` rozhraní API přepsání automaticky shromažďovat názvy, aniž byste museli měnit titulů může odeslat další obecné názvy v telemetrie.   

Pokud vaše aplikace odesílá příliš mnoho názvů vlastní události, změňte název v méně specifické kódu. Znovu nevkládejte adresy URL a dalších na stránku nebo dynamické informace v názvech vlastní události přímo. Místo toho přesuňte tyto podrobnosti do vlastní vlastnosti vlastní události s `trackEvent` rozhraní API. Například místo z `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, doporučujeme něco podobného jako `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Další postup

* [Přehled analýzy využití](app-insights-usage-overview.md)

## <a name="get-help"></a>Podpora
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

