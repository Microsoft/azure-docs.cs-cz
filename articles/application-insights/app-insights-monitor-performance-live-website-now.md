---
title: Monitorování živé webové aplikace v ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Monitorování výkonu webu bez opětovného nasazení. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 24ab8e5171ebb33a5f3be5c6bf08e361fff4e4cc
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959541"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentace webových aplikací za běhu pomocí nástrojů Application Insights

Azure Application Insights vám umožňuje instrumentovat živou webovou aplikaci, aniž byste museli upravovat nebo znovu nasazovat kód. Pokud vaše aplikace hostuje místní server služby IIS, nainstalujte Monitorování stavu. Pokud se jedná o webové aplikace Azure nebo pokud běží ve virtuálním počítači Azure, můžete monitorování pomocí Application Insights zapnout z ovládacího panelu Azure. (Existují i samostatné články o instrumentaci [živých webových aplikací J2EE](app-insights-java-live.md) a [Azure Cloud Services](app-insights-cloudservices.md).) Budete potřebovat předplatné [Microsoft Azure](http://azure.com).

![Snímek obrazovky s App Insights přehled grafy obsahující informace o neúspěšných požadavků, doba odezvy serveru a požadavky na server](./media/app-insights-monitor-performance-live-website-now/overview-graphs.png)

Můžete si vybrat ze tří způsobů, jak u webových aplikací .NET použít službu Application Insights:

* **Čas sestavení:**[Přidejte Application Insights SDK][greenbrown] do kódu webové aplikace.
* **Za běhu:** Podle níže popsaného postupu proveďte instrumentaci webové aplikace na serveru, aniž byste museli znovu sestavovat a nasazovat kód.
* **Obojí:** Přidejte do kódu webové aplikace sadu SDK a zároveň uplatněte rozšíření za běhu. Získáte to nejlepší z obou možností.

Tady je rekapitulace toho, co každý způsob přináší:

|  | Při sestavení | Za běhu |
| --- | --- | --- |
| Požadavky a výjimky |Ano |Ano |
| [Podrobnější výjimky](app-insights-asp-net-exceptions.md) | |Ano |
| [Diagnostika závislostí](app-insights-asp-net-dependencies.md) |Na platformě .NET 4.6+, ale méně podrobná |Ano, úplné podrobnosti: kódy výsledků, text příkazu SQL, příkaz HTTP|
| [Čítače výkonu systému](app-insights-performance-counters.md) |Ano |Ano |
| [Rozhraní API pro vlastní telemetrii][api] |Ano |Ne |
| [Integrace protokolu trasování](app-insights-asp-net-trace-logs.md) |Ano |Ne |
| [Zobrazení stránky a uživatelská data](app-insights-javascript.md) |Ano |Ne |
| Nutnost znovu sestavit kód |Ano | Ne |


## <a name="monitor-a-live-azure-web-app"></a>Monitorování živé webové aplikace Azure

Pokud vaše aplikace běží jako webová služba Azure, monitorování zapnete následovně:

* Na ovládacím panelu aplikace v Azure vyberte Application Insights.

    ![Nastavení Application Insights pro webovou aplikaci Azure](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Až se otevře stránka se souhrnem Application Insights, kliknutím na odkaz v dolní části otevřete úplný prostředek Application Insights.

    ![Proklikání se k Application Insights](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Monitorování cloudových aplikací a aplikací virtuálních počítačů](app-insights-overview.md).

### <a name="enable-client-side-monitoring-in-azure"></a>Povolení monitorování na straně klienta v Azure

Pokud jste v Azure povolili nástroje Application Insights, můžete přidat zobrazení stránky a telemetrii uživatelů.

1. Klikněte na Nastavení > Nastavení aplikace.
2.  V části Nastavení aplikace přidejte novou dvojici klíče a hodnoty: 
   
    Klíč: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Hodnota: `true`
3. Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

Každá webová stránka má teď vloženou sadu SDK Application Insights JavaScript.

## <a name="monitor-a-live-iis-web-app"></a>Monitorování živé webové aplikace IIS

Pokud je vaše aplikace hostovaná na serveru služby IIS, povolte Application Insights pomocí Monitorování stavu.

1. Na webovém serveru služby IIS se přihlaste pomocí přihlašovacích údajů správce.
2. Pokud Monitorování stavu Application Insights ještě není nainstalované, stáhněte si a spusťte [instalační program Monitorování stavu](http://go.microsoft.com/fwlink/?LinkId=506648) (nebo spusťte [Instalaci webové platformy](https://www.microsoft.com/web/downloads/platform.aspx) a vyhledejte v ní Monitorování stavu Application Insights).
3. V Monitorování stavu vyberte nainstalovanou webovou aplikaci nebo web, které chcete monitorovat. Přihlaste se pomocí přihlašovacích údajů Azure.

    Nakonfigurujte prostředek,ve kterém chcete zobrazovat výsledky na portálu Application Insights. (Obvykle je nejlepší vytvořit nový prostředek. Vyberte existující prostředek, pokud už pro tuto aplikaci máte [webové testy][availability] dostupnosti nebo [monitorování klienta][client] .) 

    ![Vyberte aplikaci a prostředek.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Restartujte službu IIS.

    ![Zvolte restartování v horní části dialogového okna.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Webová služba bude na krátkou dobu přerušena.

## <a name="customize-monitoring-options"></a>Přizpůsobení možností monitorování

Povolením Application Insights se do webové aplikace přidají knihovny DLL a soubor ApplicationInsights.config. Můžete [upravit soubor .config](app-insights-configuration-with-applicationinsights-config.md) a změnit některé možnosti.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Když opětovně publikujete aplikaci, znovu povolte Application Insights

Před opětovným publikováním aplikace zvažte [přidání Application Insights do kódu v sadě Visual Studio][greenbrown]. Získáte tak podrobnější telemetrii a možnost zapisovat vlastní telemetrická data.

Pokud chcete znovu publikovat aniž byste přidali Application Insights do kódu, mějte na paměti, že proces nasazení může odstranit knihovny DLL a soubor ApplicationInsights.config z publikovaného webu. Proto:

1. Pokud jste upravili soubor ApplicationInsights.config, pořiďte si jeho zálohu, než budete aplikaci znovu publikovat.
2. Znovu publikujte aplikaci.
3. Znovu povolte monitorování pomocí Application Insights. (Použijte vhodnou metodu: ovládací panel webové aplikace Azure, Monitorování stavu nebo hostitele služby IIS.)
4. Obnovte veškeré úpravy, které jste provedli v souboru .config.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Řešení potíží s konfigurací modulu runtime služby Application Insights

### <a name="cant-connect-no-telemetry"></a>Nelze se připojit? Žádná telemetrie?

* Otevřete v bráně firewall vašeho serveru [potřebné odchozí porty](app-insights-ip-addresses.md#outgoing-ports), aby Monitorování stavu mohlo fungovat.

* Otevřete monitorování stavu a vyberte svou aplikaci v levém podokně. Zkontrolujte, zda existují jakékoli zprávy diagnostiky pro tuto aplikaci v části „Konfigurace oznámení“:

  ![Otevřete okno Výkon a zobrazte si žádost, dobu odezvy, závislosti a další data.](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Na serveru, pokud se zobrazí zpráva o „nedostatečných oprávněních“, zkuste následující postup:
  * Ve Správci služby IIS vyberte fond aplikací, otevřete položku **Upřesnit nastavení**, a v části **Model procesu** si povšimněte identity.
  * V ovládacích panelech správy počítače přidejte tuto identitu do skupiny uživatelů Sledování výkonu.
* Pokud máte na serveru nainstalovaný MMA/SCOM (System Center Operations Manager), může u některých verzí dojít ke konfliktu. Odinstalujte SCOM a sledování stavu a znovu nainstalujte nejnovější verze.
* Další informace najdete v tématu [Poradce při potížích][qna].

## <a name="system-requirements"></a>Systémové požadavky
Podpora operačního systému pro sledování stavu Application Insights na serveru:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

s nejnovější aktualizací SP a rozhraním .NET Framework 4.5

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
* Při povolení další telemetrie pro webové aplikace, které byly [vytvořené pomocí sady Application Insights SDK](app-insights-asp-net.md), v době kompilace. 

### <a name="can-i-close-it-after-it-runs"></a>Můžu ji po spuštění zavřít?

Ano. Poté, co se provedla instrumentaci vybraných webových stránek, můžete ji zavřít.

Sama o sobě telemetrii neshromažďuje. Pouze nakonfiguruje webové aplikace a nastaví některá oprávnění.

### <a name="what-does-status-monitor-do"></a>K čemu Monitorování stavu slouží?

Když vyberete webovou aplikaci pro instrumentaci pomocí Monitorování stavu:

* Stáhne a umístí sestavení Application Insights a soubor .config do složky binárních souborů webové aplikace.
* Upraví soubor `web.config` přidáním modulu sledování HTTP pro Application Insights.
* Povolí profilaci CLR shromažďovat volání závislostí.

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

[Další informace](http://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next"></a>Další kroky

Zobrazení telemetrických dat:

* [Zkoumání metrik](app-insights-metrics-explorer.md) pro monitorování výkonu a využití
* [Prohledávání událostí a protokolů][diagnostic] pro diagnostiku problémů
* [Analýzy](app-insights-analytics.md) pro pokročilejší dotazy
* [Vytváření řídicích panelů](app-insights-dashboards.md)

Přidání další telemetrie:

* [Vytvoření webových testů][availability] a ověření, jestli web zůstává živý.
* [Přidání telemetrie webového klienta][usage] pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
* [Přidání sady Application Insights SDK do kódu][greenbrown] tak, abyste mohli vložit volání trasování a protokolování

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-javascript.md
