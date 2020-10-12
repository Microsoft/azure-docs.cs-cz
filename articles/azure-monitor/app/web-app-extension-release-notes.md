---
title: Poznámky k verzi pro rozšíření webové aplikace Azure – Application Insights
description: Vydává poznámky pro rozšíření Azure Web Apps pro instrumentaci za běhu pomocí Application Insights.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539716"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Poznámky k verzi pro rozšíření webové aplikace Azure pro Application Insights

Tento článek obsahuje poznámky k verzím pro rozšíření Azure Web Apps pro instrumentaci za běhu s Application Insights. To platí jenom pro předem nainstalovaná rozšíření.

[Přečtěte si další informace](azure-web-apps.md) o rozšíření webové aplikace Azure pro Application Insights.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

- Jak zjistit, která verze rozšíření právě mám?
    - Přejděte na `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Další informace najdete [v podrobné příručce Průvodce odstraňováním potíží s rozšířením a monitorováním na základě agentů](./azure-web-apps.md?tabs=net#troubleshooting) .

- Co když používám privátní rozšíření?
    - Odinstalujte rozšíření privátních webů, protože už není podporovaná.

## <a name="release-notes"></a>Poznámky k verzi

### <a name="2833"></a>2.8.33

- Agenti .NET, .NET Core, Java a Node.js a rozšíření Windows: podpora pro cloudy svrchovaného prostředí. Řetězce připojení se dají použít k odesílání dat do cloudů z svrchovaného.

### <a name="2831"></a>2.8.31

- Agent ASP.NET Core: opravený problém související s jedním z aktualizovaných odkazů sady Application Insights SDK (viz známé problémy pro 2.8.26). Pokud `System.Diagnostics.DiagnosticSource.dll` je v modulu runtime již načtena nesprávná verze nástroje, rozšíření bez kódu nyní neprovede selhání aplikace a vrátí zpět. Pro zákazníky postižené tímto problémem doporučujeme odebrat `System.Diagnostics.DiagnosticSource.dll` ze složky bin nebo použít starší verzi rozšíření nastavením ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24, jinak není monitorování aplikací povolené.

### <a name="2826"></a>2.8.26

- Agent ASP.NET Core: opravený problém související s aktualizací Application Insights SDK. Agent se nepokusí načíst, `AiHostingStartup` pokud ApplicationInsights.dll ve složce bin již existuje. Tím se vyřeší problémy související s reflexí prostřednictvím sestavení \<AiHostingStartup\> . GetTypes ().
- Známé problémy: výjimku lze `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` vyvolat `DiagnosticSource` , pokud je načtena jiná verze knihovny DLL. K tomu může dojít například tehdy, když `System.Diagnostics.DiagnosticSource.dll` se nachází ve složce pro publikování. Jako zmírňujte použití předchozí verze rozšíření nastavením nastavení aplikace ve službě App Services: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- Znovu zabalit verzi 2.8.21.

### <a name="2823"></a>2.8.23

- Přidání podpory pro monitorování neASP.NET Coreho kódu 3,0.
- Aktualizace sady ASP.NET Core SDK na [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) pro běhové verze 2,1, 2,2 a 3,0. Aplikace cílené na .NET Core 2,0 nadále používají verzi 2.1.1 sady SDK.

### <a name="2814"></a>2.8.14

- Aktualizovaná verze sady ASP.NET Core SDK z 2.3.0 na nejnovější (2.6.1) pro aplikace cílené na .NET Core 2,1, 2,2. Aplikace cílené na .NET Core 2,0 nadále používají verzi 2.1.1 sady SDK.

### <a name="2812"></a>2.8.12

- Podpora aplikací ASP.NET Core 2,2.
- Opravili jsme chybu v rozšíření ASP.NET Core způsobující vkládání sady SDK, a to i v případě, že je aplikace už instrumentovaná pomocí sady SDK. V případě aplikací 2,1 a 2,2 znamená přítomnost ApplicationInsights.dll ve složce aplikace teď rozšíření zpátky. Pro aplikace 2,0 se rozšíření odvolá jenom v případě, že je u volání povolené ApplicationInsights `UseApplicationInsights()` .

- Trvalá oprava pro nekompletní odpověď HTML pro aplikace ASP.NET Core. Tato oprava je nyní rozšířena na práci pro aplikace .NET Core 2,2.

- Přidání podpory pro vypnutí vkládání JavaScriptu pro aplikace ASP.NET Core ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ) V případě ASP.NET Core je vkládání JavaScriptu ve výchozím nastavení v režimu "výslovný výběr", pokud není explicitně vypnuté. (Výchozí nastavení se provádí pro zachování aktuálního chování.)

- Opravená chyba rozšíření ASP.NET Core, která způsobila vložení i v případě, že ikey nebyl nalezen.
- Opravili jsme chybu v logice předpony verze sady SDK, která způsobila nesprávnou verzi sady SDK v telemetrii.

- Přidala se předpona verze sady SDK pro aplikace ASP.NET Core pro identifikaci způsobu shromažďování telemetrie.
- Pevná stránka SCM-ApplicationInsights pro správné zobrazení verze předinstalovaného rozšíření.

### <a name="2810"></a>2.8.10

- Oprava pro nekompletní odpověď HTML pro aplikace ASP.NET Core.

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak nakonfigurovat monitorování pro Azure App Services, najdete v [dokumentaci k Azure App Service](azure-web-apps.md) . 
