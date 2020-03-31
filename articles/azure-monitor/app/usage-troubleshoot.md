---
title: Poradce při potížích s nástroji pro analýzu uživatelů – Azure Application Insights
description: Průvodce odstraňováním potíží – analýza využití webu a aplikací pomocí přehledů aplikací.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670912"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Poradce při potížích s nástroji pro analýzu chování uživatelů v přehledech aplikací
Máte dotazy týkající se [nástrojů pro analýzu chování uživatelů v přehledech aplikací](usage-overview.md): [Uživatelé, relace, Události](usage-segmentation.md), [Cesty](usage-funnels.md), [Toky uživatelů](usage-flows.md), Uchovávání [informací](usage-retention.md)nebo Kohorty? Zde jsou některé odpovědi.

## <a name="counting-users"></a>Počítání uživatelů
**Nástroje pro analýzu chování uživatelů ukazují, že moje aplikace má jednoho uživatele/relace, ale vím, že moje aplikace má mnoho uživatelů/relací. Jak mohu opravit tyto nesprávné počty?**

Všechny telemetrické události v Application Insights mají [anonymní ID uživatele](../../azure-monitor/app/data-model-context.md) a [ID relace](../../azure-monitor/app/data-model-context.md) jako dvě z jejich standardních vlastností. Ve výchozím nastavení všechny nástroje analýzy využití počítají uživatele a relace na základě těchto ID. Pokud tyto standardní vlastnosti nejsou naplněny jedinečnými ID pro každého uživatele a relace aplikace, zobrazí se v nástrojích pro analýzu využití nesprávný počet uživatelů a relací.

Pokud sledujete webovou aplikaci, nejjednodušším řešením je přidat do aplikace [javascriptovou sadu Application Insights](../../azure-monitor/app/javascript.md) a ujistit se, že je fragment skriptu načten na každé stránce, kterou chcete monitorovat. Sada JavaScript SDK automaticky generuje anonymní ID uživatelů a relací a poté naplní telemetrické události těmito ID při jejich odeslání z vaší aplikace.

Pokud sledujete webovou službu (bez uživatelského rozhraní), [vytvořte inicializátor telemetrie, která naplní vlastnosti ID anonymního id uživatele a ID relace](usage-send-user-context.md) podle představ vaší služby o jedinečných uživatelích a relacích.

Pokud vaše aplikace odesílá [ověřená ID uživatelů](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), můžete počítat na základě ověřených ID uživatelů v nástroji Uživatelé. V rozevíracím období Zobrazit zvolte Možnost Ověření uživatelé.

Nástroje pro analýzu chování uživatelů v současné době nepodporují počítání uživatelů nebo relací na základě jiných vlastností, než je anonymní ID uživatele, ověřené ID uživatele nebo ID relace.

## <a name="naming-events"></a>Pojmenování událostí
**Moje aplikace má tisíce různých zobrazení stránky a vlastní názvy událostí. Je těžké rozlišovat mezi nimi a nástroje analýzy chování uživatelů často přestanou reagovat. Jak mohu tyto problémy s pojmenováním vyřešit?**

Zobrazení stránky a názvy vlastních událostí se používají v rámci nástrojů pro analýzu chování uživatelů. Pojmenování události dobře je důležité pro získání hodnoty z těchto nástrojů. Cílem je rovnováha mezi tím, že příliš málo, příliš obecných jmen ("Tlačítko kliknuto") a mají\/příliš mnoho, příliš specifické názvy ("Upravit tlačítko kliknul na http: /www.contoso.com/index").

Chcete-li provést změny v zobrazení stránky a vlastních názvech událostí, které vaše aplikace odesílá, musíte změnit zdrojový kód aplikace a znovu se nasadit. **Všechna telemetrická data v Application Insights jsou uložena**po dobu 90 dnů a nelze je odstranit , takže změny názvů událostí budou trvat 90 dní, než se plně projeví. Po dobu 90 dnů po provedení změn názvu se staré i nové názvy událostí zobrazí ve vaší telemetrii, takže upravte dotazy a komunikujte v rámci týmů.

Pokud vaše aplikace odesílá příliš mnoho názvů zobrazení stránky, zkontrolujte, jestli jsou tyto názvy zobrazení stránky zadány ručně v kódu nebo jestli je javascriptová sada JavaScript SDK application insights odesílá automaticky:

* Pokud jsou názvy zobrazení stránky ručně zadány v kódu pomocí [ `trackPageView` rozhraní API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), změňte název tak, aby byl méně specifický. Vyhněte se běžným chybám, jako je umístění adresy URL do názvu zobrazení stránky. Místo toho použijte parametr `trackPageView` URL rozhraní API. Přesunutí dalších podrobností z názvu zobrazení stránky do vlastních vlastností

* Pokud sada JavaScript Application Insights JavaScript SDK automaticky odesílá názvy zobrazení stránek, můžete buď změnit názvy stránek, nebo přepnout na ruční odesílání názvů zobrazení stránky. Sada SDK [odešle](https://developer.mozilla.org/docs/Web/HTML/Element/title) název každé stránky jako název zobrazení stránky ve výchozím nastavení. Dalo by se změnit své tituly, které mají být obecnější, ale mějte na paměti SEO a další dopady tato změna by mohla mít. Ruční zadání názvů zobrazení stránky `trackPageView` pomocí rozhraní API přepíše automaticky shromažďované názvy, takže můžete odesílat obecnější názvy v telemetrii bez změny názvů stránek.   

Pokud vaše aplikace odesílá příliš mnoho vlastních názvů událostí, změňte název v kódu tak, aby byl méně specifický. Opět se vyhněte přímému umístění adres URL a dalších informací na stránce nebo dynamických informací do vlastních názvů událostí. Místo toho přesuňte tyto podrobnosti do `trackEvent` vlastních vlastností vlastní události s rozhraním API. Například místo `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, navrhujeme `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`něco jako .

## <a name="next-steps"></a>Další kroky

* [Přehled nástrojů pro analýzu chování uživatelů](usage-overview.md)

## <a name="get-help"></a>Podpora
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

