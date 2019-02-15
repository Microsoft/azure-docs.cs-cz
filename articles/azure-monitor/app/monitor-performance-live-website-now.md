---
title: Monitorování živé webové aplikace v ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorování výkonu webu bez opětovného nasazení. Funguje s ASP.NET webové aplikace hostované v místním nebo ve virtuálních počítačích.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: f7d7b7f470f43d8a7a1cd94b4b1ce79503f0dfca
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301022"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Instrumentace webových aplikací za běhu pomocí monitorování stavu Application Insights

Azure Application Insights vám umožňuje instrumentovat živou webovou aplikaci, aniž byste museli upravovat nebo znovu nasazovat kód. Budete potřebovat předplatné [Microsoft Azure](https://azure.com).

Monitorování stavu slouží k instrumentaci aplikace .NET hostované ve službě IIS místně nebo na virtuálním počítači.

- Pokud vaše aplikace se nasazuje do Azure app Service, postupujte podle [tyto pokyny](azure-web-apps.md).
- Pokud vaše aplikace je nasazená ve Virtuálním počítači Azure, můžete přepnout na monitorování pomocí Application Insights z ovládacího panelu Azure.
- (Existují také samostatné články o instrumentaci [živých webových aplikací Java EE](java-live.md) a [Azure Cloud Services](../../azure-monitor/app/cloudservices.md).)


![Snímek obrazovky s App Insights přehled grafy obsahující informace o neúspěšných požadavků, doba odezvy serveru a požadavky na server](./media/monitor-performance-live-website-now/overview-graphs.png)

Máte možnost volby dvě tras, jak použít Application Insights do webových aplikací .NET:

* **Čas sestavení:** [Přidejte Application Insights SDK] [ greenbrown] na kód vaší webové aplikace.
* **Čas spuštění:** Instrumentace webové aplikace na serveru, jak je popsáno níže, bez znovu sestavovat a nasazovat kód.

> [!NOTE]
> Pokud používáte instrumentace čas sestavení, spuštění instrumentace nebude fungovat i v případě, že je zapnutý.

Tady je rekapitulace toho, co každý způsob přináší:

|  | Při sestavení | Za běhu |
| --- | --- | --- |
| Požadavky a výjimky |Ano |Ano |
| [Podrobnější výjimky](../../azure-monitor/app/asp-net-exceptions.md) | |Ano |
| [Diagnostika závislostí](../../azure-monitor/app/asp-net-dependencies.md) |Na platformě .NET 4.6+, ale méně podrobná |Ano, úplné podrobnosti: kódy výsledků, text příkazu SQL, příkaz HTTP|
| [Čítače výkonu systému](../../azure-monitor/app/performance-counters.md) |Ano |Ano |
| [Rozhraní API pro vlastní telemetrii][api] |Ano |Ne |
| [Integrace protokolu trasování](../../azure-monitor/app/asp-net-trace-logs.md) |Ano |Ne |
| [Zobrazení stránky a uživatelská data](../../azure-monitor/app/javascript.md) |Ano |Ne |
| Nutnost znovu sestavit kód |Ano | Ne |



## <a name="monitor-a-live-iis-web-app"></a>Monitorování živé webové aplikace IIS

Pokud je vaše aplikace hostovaná na serveru služby IIS, povolte Application Insights pomocí Monitorování stavu.

1. Na webovém serveru služby IIS se přihlaste pomocí přihlašovacích údajů správce.
2. Pokud monitorování stavu Application Insights není nainstalovaná, [stáhněte a spusťte instalační program](#download)
3. V Monitorování stavu vyberte nainstalovanou webovou aplikaci nebo web, které chcete monitorovat. Přihlaste se pomocí přihlašovacích údajů Azure.

    Nakonfigurujte prostředek,ve kterém chcete zobrazovat výsledky na portálu Application Insights. (Obvykle je nejlepší vytvořit nový prostředek. Vyberte existující prostředek, pokud už pro tuto aplikaci máte [webové testy][availability] dostupnosti nebo [monitorování klienta][client] .) 

    ![Vyberte aplikaci a prostředek.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Restartujte službu IIS.

    ![Zvolte restartování v horní části dialogového okna.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Webová služba bude na krátkou dobu přerušena.

## <a name="customize-monitoring-options"></a>Přizpůsobení možností monitorování

Povolením Application Insights se do webové aplikace přidají knihovny DLL a soubor ApplicationInsights.config. Můžete [upravit soubor .config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) a změnit některé možnosti.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Když opětovně publikujete aplikaci, znovu povolte Application Insights

Před opětovným publikováním aplikace zvažte [přidání Application Insights do kódu v sadě Visual Studio][greenbrown]. Získáte tak podrobnější telemetrii a možnost zapisovat vlastní telemetrická data.

Pokud chcete znovu publikovat aniž byste přidali Application Insights do kódu, mějte na paměti, že proces nasazení může odstranit knihovny DLL a soubor ApplicationInsights.config z publikovaného webu. Proto:

1. Pokud jste upravili soubor ApplicationInsights.config, pořiďte si jeho zálohu, než budete aplikaci znovu publikovat.
2. Znovu publikujte aplikaci.
3. Znovu povolte monitorování pomocí Application Insights. (Použijte vhodnou metodu: ovládací panel webové aplikace Azure, Monitorování stavu nebo hostitele služby IIS.)
4. Obnovte veškeré úpravy, které jste provedli v souboru .config.


## <a name="troubleshoot"></a>Řešení potíží

### <a name="confirm-a-valid-installation"></a>Zkontrolujte platná instalace 

Toto jsou některé kroky, které můžete provádět potvrďte, že vaše instalace byla úspěšná.

- Zkontrolujte, jestli soubor applicationInsights.config je k dispozici v cílovém adresáři aplikace a obsahuje váš Instrumentační klíč.

- Pokud máte podezření, že data budou chybějící jednoduchý dotaz můžete spustit [Analytics](../log-query/get-started-portal.md) vypsat všechny role v cloudových procesech právě odesílá telemetrická data.
```Kusto
union * | summarize count() by cloud_RoleName, cloud_RoleInstance
```

- Pokud je potřeba potvrdit, že je Application Insights se úspěšně připojil můžete spustit [Sysinternals popisovač](https://docs.microsoft.com/sysinternals/downloads/handle) příkazového okna a potvrdí, že applicationinsights.dll se načetl službou IIS.
```cmd
handle.exe /p w3wp.exe
```


### <a name="cant-connect-no-telemetry"></a>Nelze se připojit? Žádná telemetrie?

* Otevřete v bráně firewall vašeho serveru [potřebné odchozí porty](../../azure-monitor/app/ip-addresses.md#outgoing-ports), aby Monitorování stavu mohlo fungovat.

### <a name="unable-to-login"></a>Nelze se přihlásit

* Pokud monitorování stavu, se nemůžou přihlásit, postupujte příkazového řádku nainstalujte místo toho. Monitorování stavu se pokouší přihlásit ke shromažďování svůj Instrumentační klíč, ale můžete zadat ručně pomocí příkazu:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Nepovedlo se načíst soubor nebo sestavení "System.Diagnostics.DiagnosticSource.

Tato chyba může zobrazit po povolení Application Insights. Je to proto, že instalační program nahradí tuto knihovnu dll v adresáři bin.
Chcete-li vyřešit aktualizaci souboru web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Tento problém sledujeme [tady](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Diagnostické zprávy aplikace

* Otevřete monitorování stavu a vyberte svou aplikaci v levém podokně. Zkontrolujte, zda existují jakékoli zprávy diagnostiky pro tuto aplikaci v části „Konfigurace oznámení“:

  ![Otevřete okno Výkon a zobrazte si žádost, dobu odezvy, závislosti a další data.](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Podrobné protokoly

* Ve výchozím nastavení bude monitorování stavu výstupní diagnostické protokoly: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Výstup podrobné protokoly, upravte konfigurační soubor: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` a přidejte `<add key="TraceLevel" value="All" />` k `appsettings`.
Restartujte monitorování stavu.

### <a name="insufficient-permissions"></a>Nedostatečná oprávnění
  
* Na serveru, pokud se zobrazí zpráva o „nedostatečných oprávněních“, zkuste následující postup:
  * Ve Správci služby IIS vyberte fond aplikací, otevřete položku **Upřesnit nastavení**, a v části **Model procesu** si povšimněte identity.
  * V ovládacích panelech správy počítače přidejte tuto identitu do skupiny uživatelů Sledování výkonu.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt s nástrojem System Center Operations Manager

* Pokud máte na serveru nainstalovaný MMA/SCOM (System Center Operations Manager), může u některých verzí dojít ke konfliktu. Odinstalujte SCOM a sledování stavu a znovu nainstalujte nejnovější verze.

### <a name="failed-or-incomplete-installation"></a>Neúspěšné nebo neúplná instalace

Pokud během instalace se nezdaří monitorování stavu, by mohla zůstat s nekompletní instalaci, který se nedokáže provést obnovení při monitorování stavu. To vyžaduje ruční vynulování.

Odstraní všechny soubory v adresáři vaší aplikace:
- Všechny knihovny DLL v adresáři bin, počínaje buď "Microsoft.AI." nebo "Microsoft.ApplicationInsights.".
- Tato knihovna DLL v adresáři bin "Microsoft.Web.Infrastructure.dll"
- Tato knihovna DLL v adresáři bin "System.Diagnostics.DiagnosticSource.dll"
- V adresáři vaší aplikace odebrat "App_Data\packages"
- V adresáři vaší aplikace odebrat "soubor applicationinsights.config"


### <a name="additional-troubleshooting"></a>Další řešení potíží

* Zjistěte víc [řešení potíží s][qna].

## <a name="system-requirements"></a>Systémové požadavky
Podpora operačního systému pro sledování stavu Application Insights na serveru:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

s nejnovější aktualizací SP a rozhraním .NET Framework 4.5

Na straně klienta: Windows 7, 8, 8.1 a 10, znovu s rozhraním .NET Framework 4.5

Podpora služby IIS je: Služba IIS 7, 7.5, 8, 8.5 (je vyžadována služba IIS)

## <a name="automation-with-powershell"></a>Automatizace v prostředí PowerShell
K zahájení a spuštění monitorování můžete na serveru služby IIS použít prostředí PowerShell.

Nejdřív importujte modul Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Zjistěte, které aplikace se monitorují:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Volitelné) Název webové aplikace.
* Zobrazí sledování stavu Application Insights pro každou webovou aplikaci (nebo pojmenované aplikace) na tomto serveru služby IIS.
* Vrátí `ApplicationInsightsApplication` pro každou aplikaci:

  * `SdkState==EnabledAfterDeployment`: Aplikace je monitorována a byla instrumentována v době běhu pomocí nástroje Monitor stavu nebo pomocí `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Aplikace není instrumentována pro Application Insights. Buď nebyla nikdy instrumentována, nebo bylo zakázáno spuštění sledování pomocí nástroje Monitor stavu nebo pomocí `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: Aplikace byla instrumentována přidáním sady SDK ke zdrojovému kódu. Její SDK nelze aktualizovat ani zastavit.
  * `SdkVersion` zobrazuje verzi používanou k monitorování této aplikace.
  * `LatestAvailableSdkVersion` zobrazuje aktuálně dostupnou verzi v galerii NuGet. Chcete-li upgradovat aplikaci na tuto verzi, použijte `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Název aplikace v IIS.
* `-InstrumentationKey` Ikey prostředku Application Insights, kde se mají zobrazovat výsledky.
* Tato rutina ovlivní pouze aplikace, které již nejsou instrumentovány – to znamená, SdkState==NotInstrumented.

    Rutina neovlivní již instrumentované aplikace. Nezáleží na tom, jestli aplikace byla instrumentovaná v okamžiku sestavení přidáním sady SDK do kódu nebo v době běhu předchozím použitím této rutiny.

    Verze sady SDK používaná k instrumentaci aplikace je verze, která byla naposledy stažena do tohoto serveru.

    Chcete-li stáhnout poslední verzi, použijte příkaz Update-ApplicationInsightsVersion.
* V případě úspěchu vrátí `ApplicationInsightsApplication`. Pokud se nezdaří, zaprotokoluje trasování do stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Název aplikace v IIS.
* `-All` Zastaví monitorování všech aplikací v tomto serveru IIS, pro který platí, že `SdkState==EnabledAfterDeployment`.
* Zastaví monitorování zadané aplikace a odebere instrumentace. Pracuje pouze pro aplikace, které byly instrumentovány v době běhu pomocí nástroje pro monitorování stavu nebo příkazu Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Vrátí ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Název webové aplikace ve službě IIS.
* `-InstrumentationKey` (Volitelné) Tuto položku použijte ke změně prostředku, na kterou se telemetrie aplikace odesílá.
* Tato rutina:
  * Upgrady pojmenované aplikace na verzi sady SDK naposledy stažené v tomto počítači. (Funguje pouze v případě `SdkState==EnabledAfterDeployment`)
  * Pokud jste zadali kód instrumentace, pojmenovaná aplikace se překonfiguruje na odeslání telemetrie do prostředku s tímto klíčem. (Funguje v případě `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Stáhne nejnovější Application Insights SDK na server.

## <a name="questions"></a>Dotazy týkající se Monitorování stavu

### <a name="what-is-status-monitor"></a>Co je Monitorování stavu?

Desktopová aplikace, kterou instalujete s webovým serverem IIS. Pomáhá provádět instrumentaci a konfiguraci webových aplikací. 

### <a name="when-do-i-use-status-monitor"></a>Kdy použít Monitorování stavu?

* Při instrumentaci libovolné webové aplikace, která běží na serveru IIS, i když je už spuštěná.
* Při povolení další telemetrie pro webové aplikace, které byly [vytvořené pomocí sady Application Insights SDK](../../azure-monitor/app/asp-net.md), v době kompilace. 

### <a name="can-i-close-it-after-it-runs"></a>Můžu ji po spuštění zavřít?

Ano. Poté, co se provedla instrumentaci vybraných webových stránek, můžete ji zavřít.

Sama o sobě telemetrii neshromažďuje. Pouze nakonfiguruje webové aplikace a nastaví některá oprávnění.

### <a name="what-does-status-monitor-do"></a>K čemu Monitorování stavu slouží?

Když vyberete webovou aplikaci pro instrumentaci pomocí Monitorování stavu:

* Stáhne a umístí sestavení Application Insights a soubor ApplicationInsights.config složku binárních souborů webové aplikace.
* Povolí profilaci CLR shromažďovat volání závislostí.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Jakou verzi Application Insights SDK monitorování stavu instalace?

V současné době můžete monitorování stavu nainstalovat pouze sadu SDK Application Insights verze 2.3 nebo 2.4.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Je potřeba spustit Monitorování stavu při každé aktualizaci aplikace?

Ne, pokud provádíte opakované nasazení postupně. 

Pokud při procesu publikování vyberete možnost Odstranit stávající soubory, bude potřeba konfigurovat Application Insights opakovaným spuštěním Monitorování stavu.

### <a name="what-telemetry-is-collected"></a>Jaké telemetrická data se shromažďují?

Pro aplikace instrumentované pouze za běhu pomocí Monitorování stavu:

* Požadavky HTTP
* Volání závislostí
* Výjimky
* Čítače výkonu

Pro aplikace již instrumentované v době kompilace:

 * Čítače procesů
 * Volání závislostí (.NET 4.5); návratové hodnoty ve voláních závislostí (.NET 4.6)
 * Hodnoty trasování zásobníku výjimek

[Další informace](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Stáhnout monitorování stavu

- Stáhněte a spusťte [instalační program monitorování stavu](https://go.microsoft.com/fwlink/?LinkId=506648)
- Nebo spusťte [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx) a vyhledejte v ní monitorování stavu Application Insights.

## <a name="next"></a>Další kroky

Zobrazení telemetrických dat:

* [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití
* [Prohledávání událostí a protokolů][diagnostic] pro diagnostiku problémů
* [Analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy
* [Vytváření řídicích panelů](../../azure-monitor/app/app-insights-dashboards.md)

Přidání další telemetrie:

* [Vytvoření webových testů][availability] a ověření, jestli web zůstává živý.
* [Přidání telemetrie webového klienta][usage] pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
* [Přidání sady Application Insights SDK do kódu][greenbrown] tak, abyste mohli vložit volání trasování a protokolování

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
