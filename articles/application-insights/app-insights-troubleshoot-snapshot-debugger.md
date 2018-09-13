---
title: Azure Application Insights Snapshot Debugger Průvodce odstraňováním potíží | Dokumentace Microsoftu
description: Nejčastější dotazy ohledně Azure Application Insights Snapshot debuggeru.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 285f42a6b52819077b92abce78c1f51756780604
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643236"
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Snapshot Debugger: Průvodce odstraňováním potíží

Application Insights Snapshot debuggeru umožňuje automaticky shromažďovat snímky ladění z živých webových aplikací. Snímek zobrazuje stav zdrojového kódu a proměnné v okamžiku, kdy došlo k výjimce. Tento článek vysvětluje, jak funguje ladicí program a poskytuje řešení běžných problémů.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Jak Application Insights Snapshot debuggeru funguje

Application Insights Snapshot debuggeru je součástí kanálu telemetrie Application Insights (instance ITelemetryProcessor). Snapshot Collector sleduje výjimky vyvolané v kódu (AppDomain.FirstChanceException) a telemetrie výjimek hlásí do Application Insights prostřednictvím `TelemetryClient.TrackException`. Jakmile Snapshot Collector balíček úspěšně jste přidali do projektu a jednu událost detekoval v kanálu telemetrických dat, vlastní událost s názvem "AppInsightsSnapshotCollectorLogs" a "SnapshotCollectorEnabled" ve vlastních dat bude odeslání. Ve stejnou dobu, bude spuštěn proces s názvem "SnapshotUploader.exe" (verzi 1.2.0 nebo novější) nebo "MinidumpUploader.exe" (dříve než ve verzi 1.2.0), k nahrání shromážděných snímků datových souborů do služby Application Insights.  Při spuštění procesu odeslání, odešle vlastní události s názvem "UploaderStart". Snapshot collector, zadá své normální monitorování chování.

Zatímco monitoruje telemetrie výjimek Application Insights snapshot collector, používá parametry (například ThresholdForSnapshotting MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) definované v nakonfigurovat a určit, kdy se mají shromažďovat snímku. Pokud se splní všechna pravidla, požádá kolektor snímku pro další výjimky na jednom místě. Současně vlastních událostí Application Insights s názvem "AppInsightsSnapshotCollectorLogs" a "RequestSnapshots" se odešlou. Protože kompilátor optimalizuje kód "Verze", nemusí být viditelný ve shromážděných snímků lokální proměnné. Kolektor snímků se pokusí deoptimize metodu, která vyvolala výjimku, když jej požaduje snímky. Během této doby se odešlou ve vlastních dat Application Insights vlastní událost s názvem "AppInsightsSnapshotCollectorLogs" a "ProductionBreakpointsDeOptimizeMethod".  Snímek další výjimky jsou shromažďovány, nebudou mít k dispozici místní proměnné. Po shromáždění snímku, ji budou reoptimize kód. 

> [!NOTE]
> Deoptimization vyžaduje rozšíření webu Application Insights k instalaci.

Pokud snímku se požaduje pro určité výjimky, začne kolektor snímků monitorování zpracování kanálu (AppDomain.FirstChanceException) výjimek vaší aplikace. Při výjimce dojde znovu, začne kolektor snímků (vlastní události Application Insights s názvem "AppInsightsSnapshotCollectorLogs" a "SnapshotStart" ve vlastních dat). Pak je k vytvoření stínové kopie spuštěný proces (tabulky stránky se duplicitní). Obvykle bude to trvat 10 až 20 MS. Potom se odešlou vlastních událostí Application Insights s názvem "AppInsightsSnapshotCollectorLogs" a "SnapshotStop" ve vlastní data. Po vytvoření procesu rozvětveného zvýší celkový stránkované paměti stejným způsobem jako stránkované paměti spuštěné aplikaci (pracovní sada bude mnohem menší). Když vaše aplikace je proces spuštěn za normálních okolností stínové kopie bude paměti v procesu zálohované na disk a nahraje do služby Application Insights. Po nahrání snímku se odesílat vlastní událost Application Insights s názvem "UploadSnapshotFinish".

## <a name="is-the-snapshot-collector-working-properly"></a>Kolektor snímků funguje správně?

### <a name="how-to-find-snapshot-collector-logs"></a>Jak najít protokoly Snapshot Collector
Snímek kolektoru protokolů se odešlou do účtu Application Insights, pokud [balíček NuGet nástroje Snapshot Collector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) je verze 1.1.0 nebo novější. Ujistěte se, *ProvideAnonymousTelemetry* není nastaven na hodnotu false (hodnota je true ve výchozím nastavení).

* Přejděte do prostředku Application Insights na webu Azure Portal.
* Klikněte na tlačítko *hledání* v oddílu Přehled.
* Do vyhledávacího pole zadejte následující řetězec:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Poznámka: změnit *časový rozsah* v případě potřeby.

![Snímek obrazovky hledání Snapshot Collector protokoly](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>V protokolech Snapshot collector
Při hledání protokolů Snapshot Collector, musí být "UploadSnapshotFinish" události v rozsahu cílový čas. Pokud stále nevidíte tlačítko 'Otevřít snímek ladění' k otevření snímku, pošlete e-mail na adresu snapshothelp@microsoft.com s Instrumentační klíč Application Insights.

![Snímek obrazovky prozkoumat snímku kolektoru protokolů](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Nemůžu najít snímek, který chcete otevřít
Pokud následující kroky není můžete řešit potíže, pošlete e-mail na adresu snapshothelp@microsoft.com s Instrumentační klíč Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Krok 1: Ujistěte se, že vaše aplikace odesílá telemetrická data a data výjimky do Application Insights
Přejděte do prostředku Application Insights, zkontrolujte, jestli data odeslaná z vaší aplikace.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Krok 2: Ujistěte se, že Snapshot collector se správně přidá do kanálu vaší aplikace Telemetrie Application Insights
Pokud zjistíte protokoly v kroku "Jak najít protokoly Snapshot Collector" kolektor snímků je správně přidány do projektu a můžete tento krok ignorovat.

Pokud neexistují žádné protokoly Snapshot collector, ověřte následující:
* Pro klasické aplikace pro ASP.NET, vyhledejte tento řádek *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* v *soubor ApplicationInsights.config* souboru.

* Pro aplikace ASP.NET Core, ujistěte se, že *ITelemetryProcessorFactory* s *SnapshotCollectorTelemetryProcessor* se přidá do *IServiceCollection* služby .

* Zkontrolujte také, že používáte správné Instrumentační klíč v publikované aplikaci.

* Snapshot collector nepodporuje více klíčů instrumentace v rámci jedné aplikace, snímky se odešle do Instrumentační klíč první výjimky, které ho dodržuje.

* Pokud nastavíte *InstrmentationKey* ručně v kódu, aktualizujte *InstrumentationKey* element z *soubor ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Krok 3: Ujistěte se, že je spuštěná s minimálním výpisem uživatele nahrávajícího
Vyhledejte ve snímku kolektoru protokolů, *UploaderStart* (do textového pole hledání zadejte UploaderStart). Měla by existovat událost v případě snapshot collector monitorovat první výjimku. Pokud tato událost neexistuje, v protokolech další podrobnosti. Jeden z možných způsobů řešení tohoto problému je restartování aplikace.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Krok 4: Ujistěte se, že Snapshot Collector vyjádřit svůj záměr shromažďování snímků
Vyhledejte ve snímku kolektoru protokolů, *RequestSnapshots* (typ ```RequestSnapshots``` do textového pole hledání).  Pokud není k dispozici žádné, zkontrolujte konfiguraci. Například *ThresholdForSnapshotting*, který označuje číslo, které konkrétní výjimky, ke kterému může dojít předtím, než začne shromažďování snímků.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Krok 5: Ujistěte se, že snímku není zakázáno z důvodu ochrany paměti
K ochraně výkon vaší aplikace, snímek bude se zachycuje, jenom když je dobré paměti vyrovnávací paměti. Do kolektoru protokolů snímku vyhledejte "CannotSnapshotDueToMemoryUsage". V události vlastní data bude mít podrobné důvod. Pokud vaše aplikace běží ve webové aplikaci Azure, může být omezení strict. Od webové aplikace Azure se restartování vaší aplikace, pokud se splní určitá pravidla, paměti. Můžete vertikálně navýšit kapacitu plánu služby na počítače s víc paměti k vyřešení tohoto problému.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Krok 6: Ujistěte se, že byly zachycené snímky
Vyhledejte ve snímku kolektoru protokolů, ```RequestSnapshots```.  Pokud není k dispozici, zkontrolujte konfiguraci. Například *ThresholdForSnapshotting*, který označuje počet určité výjimky před shromážděním snímku.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Krok 7: Ujistěte se, že snímky jsou nahrány správně
Vyhledejte ve snímku kolektoru protokolů, ```UploadSnapshotFinish```.  Pokud to není k dispozici, pošlete e-mail na adresu snapshothelp@microsoft.com s Instrumentační klíč Application Insights. Pokud tato událost existuje, otevřete ho v protokolech a zkopírujte hodnotu "SnapshotId" ve vlastní Data. Vyhledejte hodnotu. Vyhledá se výjimka odpovídající snímku. Pak klikněte na výjimku a otevřít snímek ladění. (Pokud neexistuje žádný odpovídající výjimky, telemetrie výjimek vzorkovat lze z důvodu vysoké objemy. Zkuste jiné snapshotId.)

![Snímek obrazovky hledání SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Snímek obrazovky otevřete výjimky](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Snímek obrazovky otevřít snímek ladění](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Lokální proměnné zobrazení snímků nebyly dokončeny.

Chybí některé lokální proměnné. Pokud vaše aplikace je spuštěna verze kódu, bude kompilátor optimalizovat hned několik proměnných. Příklad:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Pokud váš případ je rozdílný, mohlo by to znamenat chybu. Pošlete e-mail na adresu snapshothelp@microsoft.com s Instrumentační klíč Application Insights spolu s fragmentu kódu.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Zobrazení snímku: Nelze získat hodnotu lokální proměnné nebo argumentu
Ujistěte se, [rozšíření webu Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) je nainstalována. Pokud se problém nevyřeší, pošlete e-mail na adresu snapshothelp@microsoft.com s Instrumentační klíč Application Insights.
