---
title: Monitorování živé webové aplikace v ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorování výkonu webu bez opětovného nasazení. Funguje s ASP.NET webovými aplikacemi hostovanými místně nebo na virtuálních počítačích.
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: 53dbcc341fdd4bc194d34d40cdd2a975df496376
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186299"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-codeless-attach"></a>Instrumentace webových aplikací za běhu s Application Insights připojením bez kódu

> [!IMPORTANT]
> Monitorování stavu se již nedoporučuje používat a **od 1. června 2021** Tato verze monitorování stavu nebude podporována. Byl nahrazen agentem Azure Monitor Application Insights (dříve nazvaný Monitorování stavu v2). Podívejte se na naši dokumentaci pro [nasazení na místních serverech nebo na](./status-monitor-v2-overview.md) [virtuální počítač Azure a nasazení sady škálování virtuálních počítačů](./azure-vm-vmss-apps.md).

Azure Application Insights vám umožňuje instrumentovat živou webovou aplikaci, aniž byste museli upravovat nebo znovu nasazovat kód. Budete potřebovat předplatné [Microsoft Azure](https://azure.com).

Monitorování stavu slouží k instrumentaci aplikace .NET hostované ve službě IIS buď místně, nebo na virtuálním počítači.

- Pokud je vaše aplikace nasazená do virtuálního počítače Azure nebo Azure Virtual Machine Scale set, postupujte podle [těchto pokynů](azure-vm-vmss-apps.md).
- Pokud je vaše aplikace nasazená ve službě Azure App Services, postupujte podle [těchto pokynů](azure-web-apps.md).
- Pokud je vaše aplikace nasazená na virtuálním počítači Azure, můžete v Ovládacích panelech Azure přepnout na Application Insights monitorování.
- (K dispozici jsou také samostatné články o instrumentaci [Cloud Services Azure](./cloudservices.md).)


![Snímek obrazovky s přehledem grafů Application Insights, které obsahují informace o neúspěšných požadavcích, dobu odezvy serveru a požadavky na server](./media/monitor-performance-live-website-now/overview-graphs.png)

Máte možnost zvolit si dvě trasy, které Application Insights použít pro webové aplikace .NET:

* **Čas sestavení:** [Přidejte Application Insights SDK][greenbrown] do kódu webové aplikace.
* **Za běhu:** Podle níže popsaného postupu proveďte instrumentaci webové aplikace na serveru, aniž byste museli znovu sestavovat a nasazovat kód.

> [!NOTE]
> Použijete-li instrumentaci času sestavení, instrumentace běhu nebude fungovat ani v případě, že je zapnutá.

Tady je rekapitulace toho, co každý způsob přináší:

|  | Při sestavení | Za běhu |
| --- | --- | --- |
| **Vyžádá & výjimky.** |Ano |Ano |
| **[Podrobnější výjimky](./asp-net-exceptions.md)** | |Ano |
| **[Diagnostika závislostí](./asp-net-dependencies.md)** |Na platformě .NET 4.6+, ale méně podrobná |Ano, úplné podrobnosti: kódy výsledků, text příkazu SQL, příkaz HTTP|
| **[Čítače výkonu systému](./performance-counters.md)** |Ano |Ano |
| **[Rozhraní API pro vlastní telemetrii][api]** |Ano |No |
| **[Integrace protokolu trasování](./asp-net-trace-logs.md)** |Ano |No |
| **[Zobrazení stránky & uživatelských dat](./javascript.md)** |Ano |No |
| **Nutnost znovu sestavit kód** |Ano | No |



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

Povolením Application Insights se do webové aplikace přidají knihovny DLL a soubor ApplicationInsights.config. Můžete [upravit soubor .config](./configuration-with-applicationinsights-config.md) a změnit některé možnosti.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Když opětovně publikujete aplikaci, znovu povolte Application Insights

Před opětovným publikováním aplikace zvažte [přidání Application Insights do kódu v sadě Visual Studio][greenbrown]. Získáte tak podrobnější telemetrii a možnost zapisovat vlastní telemetrická data.

Pokud chcete znovu publikovat aniž byste přidali Application Insights do kódu, mějte na paměti, že proces nasazení může odstranit knihovny DLL a soubor ApplicationInsights.config z publikovaného webu. Proto:

1. Pokud jste upravili soubor ApplicationInsights.config, pořiďte si jeho zálohu, než budete aplikaci znovu publikovat.
2. Znovu publikujte aplikaci.
3. Znovu povolte monitorování pomocí Application Insights. (Použijte vhodnou metodu: ovládací panel webové aplikace Azure, Monitorování stavu nebo hostitele služby IIS.)
4. Obnovte veškeré úpravy, které jste provedli v souboru .config.


## <a name="troubleshooting"></a><a name="troubleshoot"></a>Poradce při potížích

### <a name="confirm-a-valid-installation"></a>Potvrďte platnou instalaci. 

Tady je několik kroků, pomocí kterých můžete ověřit, že se instalace úspěšně provedla.

- Ověřte, že se soubor applicationInsights.config nachází v adresáři cílové aplikace a obsahuje vaše ikey.

- Pokud se domníváte, že chybí data, můžete spustit dotaz v části [Analytics](../log-query/log-analytics-tutorial.md) a zobrazit seznam všech cloudových rolí, které aktuálně odesílají telemetrii.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Pokud potřebujete potvrdit, že Application Insights úspěšně připojen, můžete spustit [popisovač Sysinternals](/sysinternals/downloads/handle) v příkazovém okně a ověřit tak, že služba IIS načetla applicationinsights.dll.

  ```console
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Nelze se připojit? Žádná telemetrie?

* Otevřete v bráně firewall vašeho serveru [potřebné odchozí porty](./ip-addresses.md#outgoing-ports), aby Monitorování stavu mohlo fungovat.

### <a name="unable-to-login"></a>Nelze se přihlásit

Pokud se Monitorování stavu nemůže přihlásit, proveďte místo toho instalaci příkazového řádku. Monitorování stavu se pokusí přihlásit ke shromáždění vašich Ikey, ale můžete ho zadat ručně pomocí příkazu:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Nepovedlo se načíst soubor nebo sestavení System. Diagnostics. DiagnosticSource.

Tato chyba se může zobrazit po povolení Application Insights. Důvodem je to, že instalační program nahradí tuto knihovnu DLL v adresáři bin.
Oprava web.config aktualizace:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Tento problém sledujeme [tady](https://github.com/MohanGsk/ApplicationInsights-Home).


### <a name="application-diagnostic-messages"></a>Diagnostické zprávy aplikace

* Otevřete monitorování stavu a vyberte svou aplikaci v levém podokně. Zkontrolujte, zda existují jakékoli zprávy diagnostiky pro tuto aplikaci v části „Konfigurace oznámení“:

  ![Otevřete okno Výkon a zobrazte si žádost, dobu odezvy, závislosti a další data.](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Podrobné protokoly

* Ve výchozím nastavení bude Monitorování stavu výstupem diagnostických protokolů na: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Pro výstup podrobných protokolů upravte konfigurační soubor: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` a přidejte `<add key="TraceLevel" value="All" />` do `appsettings` .
Pak restartujte monitorování stavu.

* Jak Monitorování stavu je aplikace .NET, můžete také povolit [trasování .NET přidáním příslušné diagnostiky do konfiguračního souboru](/dotnet/framework/configure-apps/file-schema/trace-debug/system-diagnostics-element). V některých scénářích může být například užitečné zjistit, co se děje na úrovni sítě [konfigurací trasování sítě](/dotnet/framework/network-programming/how-to-configure-network-tracing) .

### <a name="insufficient-permissions"></a>Nedostatečná oprávnění
  
* Na serveru, pokud se zobrazí zpráva o „nedostatečných oprávněních“, zkuste následující postup:
  * Ve Správci služby IIS vyberte fond aplikací, otevřete položku **Upřesnit nastavení**, a v části **Model procesu** si povšimněte identity.
  * V ovládacích panelech správy počítače přidejte tuto identitu do skupiny uživatelů Sledování výkonu.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt se službou System Center Operations Manager

* Pokud máte na serveru nainstalovaný MMA/SCOM (System Center Operations Manager), může u některých verzí dojít ke konfliktu. Odinstalujte SCOM a sledování stavu a znovu nainstalujte nejnovější verze.

### <a name="failed-or-incomplete-installation"></a>Neúspěšná nebo nekompletní instalace

Pokud během instalace dojde k chybě Monitorování stavu, může být ponechána nekompletní instalace, ze které Monitorování stavu nelze provést obnovení. To bude vyžadovat ruční resetování.

Odstraňte všechny tyto soubory, které se nacházejí v adresáři aplikace:
- Všechny knihovny DLL v adresáři bin počínaje řetězcem "Microsoft.AI". nebo "Microsoft. ApplicationInsights.".
- Tato knihovna DLL v adresáři bin "Microsoft.Web.Infrastructure.dll"
- Tato knihovna DLL v adresáři bin "System.Diagnostics.DiagnosticSource.dll"
- V adresáři aplikace odeberte App_Data \Packages
- V adresáři aplikace odeberte "applicationinsights.config"


### <a name="additional-troubleshooting"></a>Další řešení potíží

* Další informace najdete v tématu [věnovaném řešení potíží][qna].

## <a name="system-requirements"></a>Požadavky na systém
Podpora operačního systému pro sledování stavu Application Insights na serveru:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

s nejnovějšími aktualizacemi SP a .NET Framework 4,5 (Monitorování stavu je postavená na této verzi rozhraní .NET Framework)

Na straně klienta: Windows 7, 8, 8.1 a 10, znovu s rozhraním .NET Framework 4.5

Podpora služby IIS je: IIS 7, 7.5, 8, 8.5 (je vyžadována služba IIS)

## <a name="automation-with-powershell"></a>Automatizace v prostředí PowerShell
K zahájení a spuštění monitorování můžete na serveru služby IIS použít prostředí PowerShell.

Nejdřív importujte modul Application Insights:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
```

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

   ```output
   Name                      : Default Web Site/WebApp1
   InstrumentationKey        : 00000000-0000-0000-0000-000000000000
   ProfilerState             : ApplicationInsights
   SdkState                  : EnabledAfterDeployment
   SdkVersion                : 1.2.1
   LatestAvailableSdkVersion : 1.2.3
   ```

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Název aplikace v IIS.
* `-All` Zastaví monitorování všech aplikací v tomto serveru IIS, pro který platí, že `SdkState==EnabledAfterDeployment`.
* Zastaví monitorování zadané aplikace a odebere instrumentace. Pracuje pouze pro aplikace, které byly instrumentovány v době běhu pomocí nástroje pro monitorování stavu nebo příkazu Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Vrátí ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Název webové aplikace v IIS.
* `-InstrumentationKey` (Volitelné.) Toto použijte ke změně prostředku, na který se má telemetrie aplikace odeslat.
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
* Při povolení další telemetrie pro webové aplikace, které byly [vytvořené pomocí sady Application Insights SDK](./asp-net.md), v době kompilace. 

### <a name="can-i-close-it-after-it-runs"></a>Můžu ji po spuštění zavřít?

Ano. Poté, co se provedla instrumentaci vybraných webových stránek, můžete ji zavřít.

Sama o sobě telemetrii neshromažďuje. Pouze nakonfiguruje webové aplikace a nastaví některá oprávnění.

### <a name="what-does-status-monitor-do"></a>K čemu Monitorování stavu slouží?

Když vyberete webovou aplikaci pro instrumentaci pomocí Monitorování stavu:

* Stáhne a umístí Application Insights sestavení a ApplicationInsights.config soubor do složky binárních souborů webové aplikace.
* Povolí profilaci CLR shromažďovat volání závislostí.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Jakou verzi sady Application Insights SDK Monitorování stavu nainstalovat?

Počínaje verzí teď Monitorování stavu dá instalovat jenom Application Insights SDK verze 2,3 nebo 2,4. 

Sada Application Insights SDK verze 2,4 je [Poslední verzí pro podporu rozhraní .net 4,0](https://github.com/microsoft/ApplicationInsights-dotnet/releases/tag/v2.5.0-beta1) , která byla [konce řádku. ledna 2016](https://devblogs.microsoft.com/dotnet/support-ending-for-the-net-framework-4-4-5-and-4-5-1/). Proto je teď Monitorování stavu možné použít k instrumentaci aplikace .NET 4,0. 

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

[Další informace](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download-status-monitor"></a><a name="download"></a>Stáhnout Monitorování stavu

- Použití nového [modulu PowerShellu](./status-monitor-v2-overview.md)
- Stažení a spuštění [instalačního programu monitorování stavu](https://go.microsoft.com/fwlink/?LinkId=506648)
- Nebo spusťte [instalaci webové platformy](https://www.microsoft.com/web/downloads/platform.aspx) a vyhledejte ji monitorování stavu Application Insights.

## <a name="next-steps"></a><a name="next"></a>Další kroky

Zobrazení telemetrických dat:

* [Zkoumání metrik](../platform/metrics-charts.md) pro monitorování výkonu a využití
* [Prohledávání událostí a protokolů][diagnostic] pro diagnostiku problémů
* [Analýzy](../log-query/log-query-overview.md) pro pokročilejší dotazy

Přidání další telemetrie:

* [Vytvoření webových testů][availability] a ověření, jestli web zůstává živý.
* [Přidání telemetrie webového klienta][usage] pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
* [Přidání sady Application Insights SDK do kódu][greenbrown] tak, abyste mohli vložit volání trasování a protokolování

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[roles]: ./resources-roles-access-control.md
[usage]: ./javascript.md