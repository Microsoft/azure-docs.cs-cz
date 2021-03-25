---
title: Řešení potíží s nástroji pro analýzu uživatelů – Azure Application Insights
description: Průvodce odstraňováním potíží – analýza využití webů a aplikací pomocí Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a1d77016fdf94de4fdd574b0d4cbd22d6b0b8490
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024740"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Řešení potíží s nástroji pro analýzu chování uživatelů v Application Insights
Máte dotazy týkající se [nástrojů pro analýzy chování uživatelů v Application Insights](usage-overview.md): [Uživatelé, relace, události](usage-segmentation.md), [trychtýře](usage-funnels.md), [toky uživatelů](usage-flows.md), [uchovávání](usage-retention.md)nebo kohorty? Tady jsou některé odpovědi.

## <a name="counting-users"></a>Počítání uživatelů
**Nástroje pro analýzu chování uživatelů ukazují, že má moje aplikace jeden uživatel nebo relaci, ale ví, že moje aplikace má mnoho uživatelů a relací. Jak mohu tyto nesprávné počty opravit?**

Všechny události telemetrie v Application Insights mají [ID anonymního uživatele](./data-model-context.md) a [ID relace](./data-model-context.md) jako dvě z jejich standardních vlastností. Ve výchozím nastavení se všechny nástroje pro analýzu využití počítají jako uživatelé a relace na základě těchto ID. Pokud se tyto standardní vlastnosti neplní jedinečnými identifikátory pro každého uživatele a relaci vaší aplikace, uvidíte v nástrojích analýzy využití nesprávný počet uživatelů a relací.

Pokud sledujete webovou aplikaci, nejjednodušší řešení je přidat do aplikace [sadu Application Insights JavaScript SDK](./javascript.md) a zajistit, aby byl fragment skriptu načtený na každé stránce, kterou chcete monitorovat. Sada JavaScript SDK automaticky generuje identifikátory anonymního uživatele a relace a potom naplní události telemetrie pomocí těchto ID, jak jsou odesílány z vaší aplikace.

Pokud sledujete webovou službu (bez uživatelského rozhraní), [vytvořte inicializátor telemetrie, který naplní vlastnosti anonymního uživatele a ID relace](./usage-overview.md) podle pojmů jedinečných uživatelů a relací vaší služby.

Pokud vaše aplikace posílá [ověřená ID uživatelů](./api-custom-events-metrics.md#authenticated-users), můžete se na základě uživatelských identifikátorů uživatelů v nástroji uživatelů na základě ID ověřených uživatelů spolehnout. V rozevíracím seznamu Zobrazit Vyberte možnost ověření uživatelé.

Nástroje pro analýzu chování uživatelů v současné době nepodporují počítání uživatelů nebo relací na základě vlastností kromě anonymního ID uživatele, ověřeného ID uživatele nebo ID relace.

## <a name="naming-events"></a>Události pojmenování
**Moje aplikace obsahuje tisíce různých zobrazení stránky a vlastní názvy událostí. Mezi nimi je těžké rozlišovat a časté nástroje pro analýzu chování uživatelů přestanou reagovat. Jak můžu opravit tyto problémy s pojmenování?**

Zobrazení stránky a vlastní názvy událostí se používají v rámci nástrojů pro analýzu chování uživatelů. Pro získání hodnoty z těchto nástrojů je důležité, aby byly události pojmenovány správně. Cílem je zůstatek mezi příliš malými a obecnými jmény ("kliknuli na tlačítko") a s příliš velkým počtem nejenom specifických názvů ("tlačítko Upravit klikněte na http: \/ /www.contoso.com/index").

Chcete-li provést změny zobrazení stránky a vlastních názvů událostí, které vaše aplikace posílá, je nutné změnit zdrojový kód aplikace a znovu nasadit. **Všechna data telemetrie v Application Insights jsou uložená po dobu 90 dnů a nejde je odstranit**, takže změny provedené v názvech událostí budou trvat 90 dnů, než se plně manifest zaplní. Po dobu 90 dnů od změny názvu se ve své telemetrii zobrazí staré i nové názvy událostí, takže podle potřeby upravte dotazy a sdělte v rámci svých týmů.

Pokud vaše aplikace odesílá příliš mnoho názvů zobrazení stránky, zkontrolujte, zda jsou tyto názvy zobrazení stránky zadány ručně v kódu nebo zda jsou automaticky odesílány Application Insights JavaScript SDK:

* Pokud jsou názvy zobrazení stránky ručně zadány v kódu pomocí [ `trackPageView` rozhraní API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), změňte název tak, aby byl méně specifický. Vyhněte se běžným chybám, jako je vložení adresy URL do názvu zobrazení stránky. Místo toho použijte parametr adresy URL `trackPageView` rozhraní API. Přesuňte další podrobnosti z názvu zobrazení stránky do vlastních vlastností.

* Pokud Application Insights JavaScript SDK automaticky odesílá názvy stránek, můžete buď změnit nadpisy stránek, nebo přepnout na ruční odesílání názvů zobrazení stránky. Sada SDK ve výchozím nastavení [odesílá název každé](https://developer.mozilla.org/docs/Web/HTML/Element/title) stránky jako název zobrazení stránky. Vaše tituly můžete změnit tak, aby byly obecnější, ale měli byste s vědomím SEO a dalšími dopady na tuto změnu. Ruční určení názvů zobrazení stránky s `trackPageView` rozhraním API přepíše automaticky shromážděné názvy, takže můžete odeslat obecnější názvy v telemetrie beze změny názvů stránek.   

Pokud vaše aplikace odesílá příliš mnoho vlastních názvů událostí, změňte název v kódu tak, aby byl méně specifický. Znovu se vyhněte vkládání adres URL a dalších informací do vlastních názvů událostí přímo na stránce. Místo toho tyto podrobnosti přesunete do vlastních vlastností vlastní události pomocí `trackEvent` rozhraní API. Například místo `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` na, navrhujeme něco podobného `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` .

## <a name="next-steps"></a>Další kroky

* [Přehled nástrojů pro analýzu chování uživatelů](usage-overview.md)

## <a name="get-help"></a>Získání pomoci
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)