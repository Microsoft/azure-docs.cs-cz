---
title: Monitorování živé webové aplikace v ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorování výkonu webu bez opětovného nasazení. Funguje s ASP.NET webových aplikací hostovaných místně nebo ve virtuálních počítačích.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: ba17ee275a744b88f2c76e7e3f99a1ac9cc8e758
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536824"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrument webové aplikace za běhu s Application Insights Codeless Attach

> [!IMPORTANT]
> Sledování stavu se již nedoporučuje používat. Byl nahrazen agentem Azure Monitor Application Insights (dříve s názvem Sledování stavu v2). Podívejte se na naši dokumentaci pro [nasazení místních serverů](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) nebo nasazení [virtuálních strojů Azure a škálovací sady virtuálních strojů](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Azure Application Insights vám umožňuje instrumentovat živou webovou aplikaci, aniž byste museli upravovat nebo znovu nasazovat kód. Budete potřebovat předplatné [Microsoft Azure](https://azure.com).

Sledování stavu se používá k instrumentace aplikace .NET hostované ve službě IIS v místním prostředí nebo ve virtuálním počítači.

- Pokud se vaše aplikace nasadí do škálovací sady virtuálních zařízení Azure nebo virtuálního počítače Azure, postupujte [podle těchto pokynů](azure-vm-vmss-apps.md).
- Pokud je vaše aplikace nasazená do služeb aplikací Azure, postupujte [podle těchto pokynů](azure-web-apps.md).
- Pokud se vaše aplikace nasadí v virtuálním počítači Azure, můžete zapnout monitorování přehledů aplikací z ovládacího panelu Azure.
- (Existují také samostatné články o instrumentaci [Cloudových služeb Azure](../../azure-monitor/app/cloudservices.md).)


![Snímek obrazovky s grafy přehledu přehledů aplikací obsahující informace o neúspěšných požadavcích, době odezvy serveru a požadavcích na server](./media/monitor-performance-live-website-now/overview-graphs.png)

Máte na výběr ze dvou tras, které mají použít Application Insights pro vaše webové aplikace .NET:

* **Čas sestavení:** [Přidejte Application Insights SDK][greenbrown] do kódu webové aplikace.
* **Za běhu:** Podle níže popsaného postupu proveďte instrumentaci webové aplikace na serveru, aniž byste museli znovu sestavovat a nasazovat kód.

> [!NOTE]
> Pokud používáte instrumentaci času sestavení, instrumentace běhu nebude fungovat, i když je zapnutá.

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
2. Pokud ještě není nainstalované Monitorování stavu Application Insights, [stáhněte si a spusťte instalační program](#download).
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


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Řešení potíží

### <a name="confirm-a-valid-installation"></a>Potvrzení platné instalace 

Toto jsou některé kroky, které můžete provést k potvrzení, že instalace byla úspěšná.

- Zkontrolujte, zda je soubor applicationInsights.config přítomen v adresáři cílové aplikace a obsahuje váš iklíč.

- Pokud máte podezření, že chybí data, můžete spustit jednoduchý dotaz v Analytics a [vypsat](../log-query/get-started-portal.md) všechny cloudové role, které aktuálně posílají telemetrii.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Pokud potřebujete potvrdit, že application insights je úspěšně připojen, můžete spustit [Sysinternals Handle](https://docs.microsoft.com/sysinternals/downloads/handle) v příkazovém okně a potvrdit, že applicationinsights.dll byl načten službou IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Nelze se připojit? Žádná telemetrie?

* Otevřete v bráně firewall vašeho serveru [potřebné odchozí porty](../../azure-monitor/app/ip-addresses.md#outgoing-ports), aby Monitorování stavu mohlo fungovat.

### <a name="unable-to-login"></a>Nelze se přihlásit.

* Pokud se nástroj Sledování stavu nemůže přihlásit, proveďte místo toho instalaci příkazového řádku. Sledování stavu se pokusí přihlásit ke shromažďování vašeho ikey, ale můžete to zadat ručně pomocí příkazu:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Nelze načíst soubor nebo sestavení System.Diagnostics.DiagnosticSource.

Tato chyba se může stát po povolení Application Insights. Je to proto, že instalační program nahradí tuto dll v adresáři přihrádky.
Aktualizace webu web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Sledujeme tento problém [zde](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Diagnostické zprávy aplikace

* Otevřete monitorování stavu a vyberte svou aplikaci v levém podokně. Zkontrolujte, zda existují jakékoli zprávy diagnostiky pro tuto aplikaci v části „Konfigurace oznámení“:

  ![Otevřete okno Výkon a zobrazte si žádost, dobu odezvy, závislosti a další data.](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Podrobné protokoly

* Ve výchozím nastavení sledování stavu bude výstup diagnostických protokolů na:`C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Chcete-li vytvořit podrobné protokoly, upravte `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` konfigurační soubor: a přidejte `<add key="TraceLevel" value="All" />` do . `appsettings`
Potom restartujte sledování stavu.

* Jako Sledování stavu je aplikace .NET můžete také povolit [trasování .net přidáním příslušné diagnostiky do konfiguračního souboru](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). V některých scénářích může být například užitečné zjistit, co se děje na úrovni [sítě, konfigurací trasování sítě](https://docs.microsoft.com/dotnet/framework/network-programming/how-to-configure-network-tracing)

### <a name="insufficient-permissions"></a>Nedostatečná oprávnění
  
* Na serveru, pokud se zobrazí zpráva o „nedostatečných oprávněních“, zkuste následující postup:
  * Ve Správci služby IIS vyberte fond aplikací, otevřete položku **Upřesnit nastavení**, a v části **Model procesu** si povšimněte identity.
  * V ovládacích panelech správy počítače přidejte tuto identitu do skupiny uživatelů Sledování výkonu.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt s operačním manažerem systémového centra

* Pokud máte na serveru nainstalovaný MMA/SCOM (System Center Operations Manager), může u některých verzí dojít ke konfliktu. Odinstalujte SCOM a sledování stavu a znovu nainstalujte nejnovější verze.

### <a name="failed-or-incomplete-installation"></a>Neúspěšná nebo neúplná instalace

Pokud se během instalace nezdaří nástroj Sledování stavu, může vám zůstat neúplná instalace, ze které se nástroj Sledování stavu nemůže zotavit. To bude vyžadovat ruční reset.

Odstraňte některý z těchto souborů nalezených v adresáři aplikace:
- Všechny knihovny DLL v adresáři přihrádky začínající buď Microsoft.AI. nebo "Microsoft.ApplicationInsights.".
- Tato dll v adresáři bin "Microsoft.Web.Infrastructure.dll"
- Tato dll v adresáři bin "System.Diagnostics.DiagnosticSource.dll"
- V adresáři aplikace odeberte "App_Data\packages"
- V adresáři aplikace odeberte soubor applicationinsights.config.


### <a name="additional-troubleshooting"></a>Další řešení potíží

* Viz Další [řešení potíží][qna].

## <a name="system-requirements"></a>Systémové požadavky
Podpora operačního systému pro sledování stavu Application Insights na serveru:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

s nejnovějšími sp a .NET Framework 4.5 (Sledování stavu je postaveno na této verzi frameworku)

Na straně klienta: Windows 7, 8, 8.1 a 10, znovu s rozhraním .NET Framework 4.5

Podpora služby IIS je: IIS 7, 7.5, 8, 8.5 (je vyžadována služba IIS)

## <a name="automation-with-powershell"></a>Automatizace v prostředí PowerShell
K zahájení a spuštění monitorování můžete na serveru služby IIS použít prostředí PowerShell.

Nejdřív importujte modul Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Zjistěte, které aplikace se monitorují:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Volitelné) Název webové aplikace.
* Zobrazí sledování stavu Application Insights pro každou webovou aplikaci (nebo pojmenované aplikace) na tomto serveru služby IIS.
* Vrátí `ApplicationInsightsApplication` pro každou aplikaci:

  * `SdkState==EnabledAfterDeployment`: Aplikace je monitorována a byla instrumentována za běhu nástrojem Monitor stavu nebo rutinou `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Aplikace není instrumentována pro Application Insights. Buď nebyla nikdy instrumentována, nebo bylo zakázáno spuštění sledování pomocí nástroje Monitor stavu nebo pomocí `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: Aplikace byla instrumentována přidáním sady SDK do zdrojového kódu. Její SDK nelze aktualizovat ani zastavit.
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

* `-Name`: Název webové aplikace v IIS.
* `-InstrumentationKey`(Nepovinné.) Pomocí tohoto změnit prostředek, do kterého je odeslána telemetrie aplikace.
* Tato rutina:
  * Upgrady pojmenované aplikace na verzi sady SDK naposledy stažené v tomto počítači. (Funguje pouze v případě `SdkState==EnabledAfterDeployment`)
  * Pokud jste zadali kód instrumentace, pojmenovaná aplikace se překonfiguruje na odeslání telemetrie do prostředku s tímto klíčem. (Funguje v případě `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Stáhne nejnovější Application Insights SDK na server.

## <a name="questions-about-status-monitor"></a><a name="questions"></a>Dotazy týkající se Monitorování stavu

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

* Soubory Application Insights a ApplicationInsights.config se stáhnou a umístí do složky binárních souborů webové aplikace.
* Povolí profilaci CLR shromažďovat volání závislostí.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Jakou verzi sady Application Insights SDK nainstaluje nástroj Sledování stavu?

Od této chvíle může nástroj Sledování stavu instalovat pouze sady Application Insights s dsad verze 2.3 nebo 2.4. 

Application Insights SDK verze 2.4 je [poslední verze pro podporu .NET 4.0,](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) která byla [EOL leden 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Proto od nynějška sledování stavu lze použít k instrumentaci .NET 4.0 aplikace. 

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

## <a name="download-status-monitor"></a><a name="download"></a>Stáhnout sledování stavu

- Použití nového [modulu PowerShellu](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)
- Stažení a spuštění [instalačního programu sledování stavu](https://go.microsoft.com/fwlink/?LinkId=506648)
- Nebo spusťte [Instalační službu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx) a vyhledejte v ní nástroj Sledování stavu přehledů aplikací.

## <a name="next-steps"></a><a name="next"></a>Další kroky

Zobrazení telemetrických dat:

* [Zkoumání metrik](../../azure-monitor/platform/metrics-charts.md) pro monitorování výkonu a využití
* [Prohledávání událostí a protokolů][diagnostic] pro diagnostiku problémů
* [Analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy

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
