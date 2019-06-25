---
title: Azure Application Insights Snapshot Debugger pro aplikace .NET | Dokumentace Microsoftu
description: Ladění snímků se automaticky shromažďují, pokud jsou výjimky vyvolány v produkčních aplikacích .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 4157285e8af67acd1dc3627bebc12076d7fe072c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595558"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ladicí snímky pro výjimky v aplikacích .NET
Když dojde k výjimce, můžete automaticky shromažďovat snímky ladění z vaší živé webové aplikace. Snímek zobrazuje stav zdrojového kódu a proměnné v okamžiku, kdy byla vyvolána výjimka. Snapshot Debugger (preview) v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoruje telemetrie výjimek z vaší webové aplikace. Shromažďuje snímky na vaše horní vyvolání výjimky, abyste měli informace, které potřebujete k diagnostice problémů v produkčním prostředí. Zahrnout [balíček NuGet Snapshot collector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) ve vaší aplikaci a volitelně nakonfigurovat kolekci parametrů v [soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Snímky se zobrazí na [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na portálu Application Insights.

Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Chcete-li získat více výkonné možnosti ladění se zdrojovým kódem, otevřete snímky s Visual Studio Enterprise. 2019. V sadě Visual Studio, můžete také [nastavit snímkovací body interaktivně pořizovat snímky](https://aka.ms/snappoint) bez čekání na výjimku.

Ladění snímky se ukládají po dobu sedmi dní. Tyto zásady uchování je nastavena na základě jednotlivých aplikací. Pokud je potřeba tuto hodnotu zvýšit, můžete požádat o zvýšení tak, že otevřete případ podpory na webu Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Povolit Application Insights Snapshot debuggeru pro aplikaci
Shromažďování snímků je k dispozici pro:
* Aplikace rozhraní .NET framework a ASP.NET rozhraním .NET Framework 4.5 nebo novější.
* Aplikace .NET core 2.0 a ASP.NET Core 2.0 běžící na Windows.

Podporují se následující prostředí:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) spuštění operačního systému řady 4 nebo novější
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) systému na Windows serveru 2012 R2 nebo novějším
* [Azure Virtual Machines a virtuálních počítačů škálovací sady](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) systémem Windows Server 2012 R2 nebo novější
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) systémem Windows Server 2012 R2 nebo novější

> [!NOTE]
> Klientské aplikace (například WPF, Windows Forms nebo UPW) nejsou podporovány.

Pokud jste povolili ladicí program snímků, ale nezobrazují snímky, zkontrolujte naše [Průvodce odstraňováním potíží](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Udělení oprávnění

Přístup k snímků je chráněn řízení přístupu na základě role (RBAC). Ke kontrole snímku, je musíte nejprve přidat do potřebné role vlastník předplatného.

> [!NOTE]
> Vlastníci a přispěvatelé automaticky není nutné tuto roli. Aby bylo možné zobrazit snímky, musí si sami přidají do role.

Přiřaďte vlastníky těchto předplatných `Application Insights Snapshot Debugger` role pro uživatele, kteří budou kontrolovat snímky. Tato role může být přiřazena na jednotlivé uživatele nebo skupiny podle vlastníků předplatných pro cílový prostředek Application Insights nebo jeho skupina prostředků nebo předplatného.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
1. Klikněte na tlačítko **řízení přístupu (IAM)** .
1. Klikněte na tlačítko **+ přidat přiřazení role** tlačítko.
1. Vyberte **Application Insights Snapshot debuggeru** z **role** rozevíracího seznamu.
1. Vyhledejte a zadejte název pro uživatele přidat.
1. Klikněte na tlačítko **Uložit** tlačítko pro přidání uživatele do role.


> [!IMPORTANT]
> Snímky mohou obsahovat osobní a dalších citlivých informací v hodnoty proměnných a parametrů.

## <a name="view-snapshots-in-the-portal"></a>Zobrazení snímků na portálu

Poté, co došlo k výjimce ve vaší aplikaci a vytvoření snímku, měli byste snímky k zobrazení. Může trvat 5 až 10 minut od výjimku, ke kterým dochází na snímek připravený a zobrazit na portálu. Chcete-li zobrazit snímky v **selhání** vyberte **operace** tlačítko při prohlížení **operace** kartě nebo vyberte **výjimky**tlačítko při prohlížení **výjimky** kartu:

![Chyby stránky](./media/snapshot-debugger/failures-page.png)

Vyberte operaci nebo výjimka v pravém podokně otevřete **podrobnosti transakce začátku do konce** podokně a pak vyberte události výjimky. Pokud je k dispozici pro danou výjimku snímku **otevřít snímek ladění** se zobrazí tlačítko v pravém podokně s podrobnostmi o [výjimka](../../azure-monitor/app/asp-net-exceptions.md).

![Tlačítko Otevřít snímek ladění výjimek](./media/snapshot-debugger/e2e-transaction-page.png)

V zobrazení ladění snímku uvidíte zásobník volání a podokno proměnných. Když vyberete rámce zásobníku volání v panelu zásobníku volání, můžete zobrazovat místní proměnné a parametry pro tuto funkci volat v podokně proměnné.

![Zobrazit snímek ladění na portálu](./media/snapshot-debugger/open-snapshot-portal.png)

Snímky mohou obsahovat citlivé informace a ve výchozím nastavení nejsou možné zobrazit. Chcete-li zobrazit snímky, musíte mít `Application Insights Snapshot Debugger` role, které jsou vám přiřazeny.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Zobrazit snímky v sadě Visual Studio Enterprise 2017 nebo novější
1. Klikněte na tlačítko **Stáhnout snímek** tlačítko a stáhněte si `.diagsession` soubor, který lze otevřít v sadě Visual Studio Enterprise.

2. Chcete-li otevřít `.diagsession` souboru, musíte mít nainstalována součást snímku ladicího programu Visual Studio. Komponenty ladicího programu snímků je požadovaná součást sady funkcí technologie ASP.net v sadě Visual Studio a můžete vybrat ze seznamu jednotlivých komponent v instalačním programu sady Visual Studio. Pokud používáte verzi sady Visual Studio před Visual Studio 2017 verze 15.5, budete muset nainstalovat rozšíření z [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otevření souboru snímku, zobrazí se stránka s minimálním výpisem ladění v sadě Visual Studio. Klikněte na tlačítko **ladění spravovaného kódu** pro spuštění ladění snímku. Snímek se otevře na řádek kódu, kde byla vyvolána výjimka, takže můžete ladit aktuální stav procesu.

    ![Zobrazit snímek ladění v sadě Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Stažený snímek zahrnuje všechny soubory symbolů, které se nacházejí na webovém serveru aplikace. Tyto soubory symbolů je potřeba přidružit data snímku se zdrojovým kódem. Pro aplikace služby App Service Ujistěte se, že má povolit nasazení symbolů při publikování webové aplikace.

## <a name="how-snapshots-work"></a>Jak fungují snímky

Snapshot Collector je implementovaný jako [procesoru Telemetrie Application Insights](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po spuštění aplikace procesoru snímku kolektor Telemetrie se přidá do kanálu telemetrie vaší aplikace.
Pokaždé, když vaše aplikace volání [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), ID problému z typu výjimky a hází metoda vypočítá Snapshot Collector.
Pokaždé, když vaše aplikace volá TrackException, hodnota čítače se zvýší pro odpovídající ID problému. Dosáhne-li čítač `ThresholdForSnapshotting` hodnotu, ID problému se přidá do kolekce plánu.

Snapshot Collector také sleduje výjimky, jako jsou vyvolány prostřednictvím přihlášení k odběru [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) událostí. Když se aktivuje tuto událost, ID problému výjimky je počítaný porovná s ID problému v plánu shromažďování dat
Pokud se zjistí shoda, je vytvořen snímek spuštěného procesu. Snímek je přiřazen jedinečný identifikátor a výjimky je označený tímto identifikátorem. Po vrácení obslužnou rutinu FirstChanceException vyvolané výjimky se zpracovává jako obvykle. Nakonec výjimku dosáhne metoda TrackException znovu, kde, společně s identifikátorem snímku se hlásí do Application Insights.

Hlavní proces bude pokračovat a obsluhuje provoz pro uživatele s malou přerušení. Mezitím snímku je předávána procesu uživatele Nahrávajícího snímku. Snímek uživatele Nahrávajícího minimálního výpisu vytvoří a nahraje ho do Application Insights spolu se soubory relevantní symbolů (PDB).

> [!TIP]
> - Proces snímku je pozastavené klon spuštěnému procesu.
> - Vytvoření snímku trvá asi 10 až 20 MS.
> - Výchozí hodnota pro `ThresholdForSnapshotting` 1. Toto je také minimální hodnota. Proto má aplikace aktivovat stejná výjimka **dvakrát** před vytvořením snímku.
> - Nastavte `IsEnabledInDeveloperMode` na hodnotu true, pokud chcete generovat snímky při ladění v sadě Visual Studio.
> - Vytvoření rychlost pořizování snímků je omezena `SnapshotsPerTenMinutesLimit` nastavení. Výchozí limit je, že jeden snímek každých deset minut.
> - Více než 50 snímky za den mohou být odeslány.

## <a name="limitations"></a>Omezení

Výchozí dobu uchování dat je 15 dnů. Pro každou instanci Application Insights je povolený maximálně 50 snímky za den.

### <a name="publish-symbols"></a>Publikovat symboly
Snapshot Debugger vyžaduje soubory symbolů na provozním serveru k dekódování proměnných a ladicího prostředí sady Visual Studio.
Verze 15.2 (nebo novější) sady Visual Studio 2017 publikuje symboly pro verzi sestavení ve výchozím nastavení, když se publikuje do služby App Service. V předchozích verzích, budete muset přidat následující řádek na svůj profil publikování `.pubxml` souboru tak, aby v režimu vydání jsou publikovány symboly:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pro Azure Compute a ostatními typy, ujistěte se, že jsou soubory symbolů ve stejné složce hlavní aplikaci knihovny DLL (obvykle `wwwroot/bin`) nebo jsou k dispozici v aktuální cestě.

### <a name="optimized-builds"></a>Optimalizovaná sestavení
V některých případech se lokální proměnné nelze zobrazit, v sestaveních pro vydání z důvodu optimalizace, které se použijí kompilátorem JIT.
Ve službě Azure App Services, ale můžete Snapshot Collector deoptimize aktivační metody, které jsou součástí jeho plánu shromažďování dat.

> [!TIP]
> Ve službě App Service, jak získat podporu deoptimization nainstalujte rozšíření Application Insights Site Extension.

## <a name="next-steps"></a>Další postup
Povolte Application Insights Snapshot debuggeru pro aplikaci:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a virtuálních počítačů škálovací sady](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Nad rámec Application Insights Snapshot debuggeru:
 
* [Nastavit snímkovací body ve vašem kódu](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) získat snímky bez čekání na výjimku.
* [Diagnostika výjimek ve vašich webových aplikacích](../../azure-monitor/app/asp-net-exceptions.md) vysvětluje, jak zviditelnit více výjimek Application Insights.
* [Inteligentní zjišťování](../../azure-monitor/app/proactive-diagnostics.md) automaticky zjišťuje anomálie výkonu.
