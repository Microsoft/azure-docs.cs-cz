---
title: Azure Application Insights Snapshot Debugger pro aplikace .NET
description: Ladění snímků se automaticky shromažďují, pokud jsou výjimky vyvolány v produkčních aplikacích .NET
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275760"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ladicí snímky pro výjimky v aplikacích .NET
Když dojde k výjimce, můžete automaticky shromažďovat snímky ladění z vaší živé webové aplikace. Snímek zobrazuje stav zdrojového kódu a proměnné v okamžiku, kdy byla vyvolána výjimka. Snapshot Debugger v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitorují telemetrii výjimek z vaší webové aplikace. Shromažďuje snímky na vaše horní vyvolání výjimky, abyste měli informace, které potřebujete k diagnostice problémů v produkčním prostředí. Zahrňte do aplikace [balíček NuGet pro kolektor snímků](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) a volitelně nakonfigurujte parametry kolekce v [souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Snímky se zobrazí na [výjimkách](../../azure-monitor/app/asp-net-exceptions.md) na portálu Application Insights.

Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Chcete-li získat výkonnější možnosti ladění pomocí zdrojového kódu, otevřete snímky v aplikaci Visual Studio 2019 Enterprise. V sadě Visual Studio můžete také [nastavit snímkovací body pro interaktivní pořizování snímků](https://aka.ms/snappoint) bez čekání na výjimku.

Snímky ladění se ukládají po dobu 15 dnů. Tyto zásady uchování je nastavena na základě jednotlivých aplikací. Pokud je potřeba tuto hodnotu zvýšit, můžete požádat o zvýšení tak, že otevřete případ podpory na webu Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Povolit pro aplikaci Application Insights Snapshot Debugger
Shromažďování snímků je k dispozici pro:
* Aplikace rozhraní .NET framework a ASP.NET rozhraním .NET Framework 4.5 nebo novější.
* Aplikace .NET core 2.0 a ASP.NET Core 2.0 běžící na Windows.

Podporují se následující prostředí:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) s operačním systémem 4 nebo novějším.
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) spuštěné v systému Windows Server 2012 R2 nebo novějším
* [Azure Virtual Machines a sada škálování virtuálních počítačů](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) s Windows Serverem 2012 R2 nebo novějším
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) se systémem Windows Server 2012 R2 nebo novějším nebo Windows 8.1 nebo novějším

> [!NOTE]
> Klientské aplikace (například WPF, Windows Forms nebo UPW) nejsou podporovány.

Pokud jste povolili Snapshot Debugger, ale nevidíte snímky, podívejte se na naši [příručku pro řešení potíží](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Udělit oprávnění

Přístup k snímků je chráněn řízení přístupu na základě role (RBAC). Ke kontrole snímku, je musíte nejprve přidat do potřebné role vlastník předplatného.

> [!NOTE]
> Vlastníci a přispěvatelé automaticky není nutné tuto roli. Aby bylo možné zobrazit snímky, musí si sami přidají do role.

Vlastníci předplatného by měli přiřadit roli `Application Insights Snapshot Debugger` uživatelům, kteří budou kontrolovat snímky. Tato role může být přiřazena na jednotlivé uživatele nebo skupiny podle vlastníků předplatných pro cílový prostředek Application Insights nebo jeho skupina prostředků nebo předplatného.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
1. Klikněte na **Řízení přístupu (IAM)** .
1. Klikněte na tlačítko **+ Přidat přiřazení role** .
1. V rozevíracím seznamu **role** vyberte **Application Insights Snapshot Debugger** .
1. Vyhledejte a zadejte název pro uživatele přidat.
1. Kliknutím na tlačítko **Uložit** přidáte uživatele do role.


> [!IMPORTANT]
> Snímky mohou obsahovat osobní a dalších citlivých informací v hodnoty proměnných a parametrů.

## <a name="view-snapshots-in-the-portal"></a>Zobrazení snímků na portálu

Po výskytu výjimky v aplikaci a vytvoření snímku byste měli mít snímky k zobrazení. Výjimka může trvat 5 až 10 minut, než se dostanou do snímku připraveného a zobrazit na portálu. Chcete-li zobrazit snímky, vyberte v podokně **selhání** tlačítko **operace** při zobrazení karty **operace** , nebo při zobrazení karty **výjimky** vyberte tlačítko **výjimky** :

![Stránka selhání](./media/snapshot-debugger/failures-page.png)

V pravém podokně vyberte operaci nebo výjimku, čímž otevřete podokno **podrobnosti transakce** , a pak vyberte událost výjimky. Pokud je pro danou výjimku k dispozici snímek, v pravém podokně se zobrazí tlačítko **otevřít snímek ladění** s podrobnostmi o [výjimce](../../azure-monitor/app/asp-net-exceptions.md).

![Tlačítko Otevřít snímek ladění výjimek](./media/snapshot-debugger/e2e-transaction-page.png)

V zobrazení ladění snímku uvidíte zásobník volání a podokno proměnných. Když vyberete rámce zásobníku volání v panelu zásobníku volání, můžete zobrazovat místní proměnné a parametry pro tuto funkci volat v podokně proměnné.

![Zobrazit snímek ladění na portálu](./media/snapshot-debugger/open-snapshot-portal.png)

Snímky mohou obsahovat citlivé informace a ve výchozím nastavení nejsou možné zobrazit. Chcete-li zobrazit snímky, musíte mít přiřazenou roli `Application Insights Snapshot Debugger`.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Zobrazit snímky v aplikaci Visual Studio 2017 Enterprise nebo vyšší
1. Klikněte na tlačítko **Stáhnout snímek** a stáhněte soubor `.diagsession`, který lze otevřít Visual Studio Enterprise.

2. Chcete-li otevřít soubor `.diagsession`, je nutné mít nainstalovanou Snapshot Debugger součást sady Visual Studio. Komponenta Snapshot Debugger je požadovaná součást úlohy ASP.net v aplikaci Visual Studio a lze ji vybrat ze seznamu jednotlivých komponent v instalačním programu sady Visual Studio. Pokud používáte verzi sady Visual Studio starší než Visual Studio 2017 verze 15,5, bude nutné nainstalovat rozšíření z [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otevření souboru snímku, zobrazí se stránka s minimálním výpisem ladění v sadě Visual Studio. Kliknutím na **ladění spravovaného kódu** spusťte ladění snímku. Snímek se otevře na řádek kódu, kde byla vyvolána výjimka, takže můžete ladit aktuální stav procesu.

    ![Zobrazit snímek ladění v sadě Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Stažený snímek zahrnuje všechny soubory symbolů, které se nacházejí na webovém serveru aplikace. Tyto soubory symbolů je potřeba přidružit data snímku se zdrojovým kódem. Pro aplikace služby App Service Ujistěte se, že má povolit nasazení symbolů při publikování webové aplikace.

## <a name="how-snapshots-work"></a>Jak fungují snímky

Snapshot Collector se implementuje jako [procesor telemetrie Application Insights](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po spuštění aplikace procesoru snímku kolektor Telemetrie se přidá do kanálu telemetrie vaší aplikace.
Pokaždé, když vaše aplikace volá [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), Snapshot COLLECTOR vypočítá ID problému z typu vyvolané výjimky a metody throw.
Pokaždé, když vaše aplikace volá TrackException, hodnota čítače se zvýší pro odpovídající ID problému. Když čítač dosáhne `ThresholdForSnapshotting` hodnoty, ID problému se přidá do plánu kolekce.

Snapshot Collector také monitoruje výjimky, když jsou vyvolány přihlášením k odběru události [AppDomain. CurrentDomain. FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) . Když se aktivuje tuto událost, ID problému výjimky je počítaný porovná s ID problému v plánu shromažďování dat
Pokud se zjistí shoda, je vytvořen snímek spuštěného procesu. Snímek je přiřazen jedinečný identifikátor a výjimky je označený tímto identifikátorem. Po vrácení obslužnou rutinu FirstChanceException vyvolané výjimky se zpracovává jako obvykle. Nakonec výjimku dosáhne metoda TrackException znovu, kde, společně s identifikátorem snímku se hlásí do Application Insights.

Hlavní proces bude pokračovat a obsluhuje provoz pro uživatele s malou přerušení. Mezitím snímku je předávána procesu uživatele Nahrávajícího snímku. Snímek uživatele Nahrávajícího minimálního výpisu vytvoří a nahraje ho do Application Insights spolu se soubory relevantní symbolů (PDB).

> [!TIP]
> - Proces snímku je pozastavené klon spuštěnému procesu.
> - Vytvoření snímku trvá asi 10 až 20 MS.
> - Výchozí hodnota pro `ThresholdForSnapshotting` je 1. Toto je také minimální hodnota. Proto musí vaše aplikace aktivovat stejnou výjimku **dvakrát** před vytvořením snímku.
> - Nastavte `IsEnabledInDeveloperMode` na hodnotu true, pokud chcete vygenerovat snímky během ladění v sadě Visual Studio.
> - Frekvence vytváření snímků je omezená nastavením `SnapshotsPerTenMinutesLimit`. Výchozí limit je, že jeden snímek každých deset minut.
> - Více než 50 snímky za den mohou být odeslány.

## <a name="limitations"></a>Omezení

Výchozí doba uchovávání dat je 15 dní. Pro každou instanci Application Insights je povolený maximální počet 50 snímků za den.

### <a name="publish-symbols"></a>Publikovat symboly
Snapshot Debugger vyžaduje soubory symbolů na provozním serveru k dekódování proměnných a ladicího prostředí sady Visual Studio.
Verze 15.2 (nebo novější) sady Visual Studio 2017 publikuje symboly pro verzi sestavení ve výchozím nastavení, když se publikuje do služby App Service. V předchozích verzích musíte do svého publikačního profilu přidat následující řádek `.pubxml` souboru, aby se symboly publikovaly v režimu vydání:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

U výpočetních a dalších typů Azure se ujistěte, že jsou soubory symbolů ve stejné složce hlavní aplikace. dll (obvykle `wwwroot/bin`), nebo jsou k dispozici na aktuální cestě.

> [!NOTE]
> Další informace o různých možnostech symbolu, které jsou k dispozici, najdete v dokumentaci k sadě [Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Pro dosažení nejlepších výsledků doporučujeme použít "úplné", "přenosné" nebo "vložené".

### <a name="optimized-builds"></a>Optimalizovaná sestavení
V některých případech se lokální proměnné nelze zobrazit, v sestaveních pro vydání z důvodu optimalizace, které se použijí kompilátorem JIT.
Ve službě Azure App Services, ale můžete Snapshot Collector deoptimize aktivační metody, které jsou součástí jeho plánu shromažďování dat.

> [!TIP]
> Ve službě App Service, jak získat podporu deoptimization nainstalujte rozšíření Application Insights Site Extension.

## <a name="next-steps"></a>Další kroky
Povolit pro aplikaci Application Insights Snapshot Debugger:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Nad Application Insights Snapshot Debugger:
 
* [Nastavte snímkovací body v kódu](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) pro získání snímků bez čekání na výjimku.
* [Diagnostika výjimek ve vašich webových aplikacích](../../azure-monitor/app/asp-net-exceptions.md) vysvětluje, jak je možné Application Insights zobrazit další výjimky.
* [Inteligentní zjišťování](../../azure-monitor/app/proactive-diagnostics.md) automaticky zjišťuje anomálie výkonu.
