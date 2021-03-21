---
title: Azure Application Insights Snapshot Debugger pro aplikace .NET
description: Snímky ladění se automaticky shromažďují, pokud jsou výjimky vyvolané v produkčních aplikacích .NET.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
author: cweining
ms.author: cweining
ms.reviewer: cweining
ms.openlocfilehash: bd196e60fed8a18064bfa59e343e51a878a11237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217393"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ladicí snímky pro výjimky v aplikacích .NET
Pokud dojde k výjimce, můžete automaticky shromáždit snímek ladění z živé webové aplikace. Snímek zobrazuje stav zdrojového kódu a proměnných v okamžiku, kdy byla vyvolána výjimka. Snapshot Debugger v [Azure Application Insights](./app-insights-overview.md) monitorují telemetrii výjimek z vaší webové aplikace. Shromažďuje snímky na vašich vyvolané výjimce, takže budete mít k dispozici informace potřebné k diagnostice problémů v produkčním prostředí. Zahrňte do aplikace [balíček NuGet pro kolektor snímků](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) a volitelně nakonfigurujte parametry kolekce v [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Snímky se zobrazí na [výjimkách](./asp-net-exceptions.md) na portálu Application Insights.

Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Chcete-li získat výkonnější možnosti ladění pomocí zdrojového kódu, otevřete snímky v aplikaci Visual Studio 2019 Enterprise. V sadě Visual Studio můžete také [nastavit snímkovací body pro interaktivní pořizování snímků](/visualstudio/debugger/debug-live-azure-applications) bez čekání na výjimku.

Snímky ladění se ukládají po dobu 15 dnů. Tyto zásady uchovávání informací se nastavují na základě jednotlivých aplikací. Pokud potřebujete tuto hodnotu zvýšit, můžete požádat o zvýšení otevřením případu podpory v Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Povolit pro aplikaci Application Insights Snapshot Debugger
Kolekce snímků je k dispozici pro:
* Aplikace .NET Framework a ASP.NET spuštěné .NET Framework 4,5 nebo novější.
* Aplikace .NET Core 2,0 a ASP.NET Core 2,0, které běží v systému Windows.

Podporují se následující prostředí:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Funkce Azure Functions](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) s operačním systémem 4 nebo novějším.
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) spuštěné v systému Windows Server 2012 R2 nebo novějším
* [Azure Virtual Machines a sada škálování virtuálních počítačů](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) s Windows Serverem 2012 R2 nebo novějším
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) se systémem Windows Server 2012 R2 nebo novějším nebo Windows 8.1 nebo novějším

> [!NOTE]
> Klientské aplikace (například WPF, model Windows Forms nebo UWP) nejsou podporovány.

Pokud jste povolili Snapshot Debugger, ale nevidíte snímky, podívejte se na naši [příručku pro řešení potíží](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Udělení oprávnění

Přístup k snímkům je chráněn řízením přístupu na základě role Azure (Azure RBAC). Abyste mohli prozkoumat snímek, vlastník předplatného vás nejprve musí přidat do potřebné role.

> [!NOTE]
> Vlastníci a přispěvatelé tuto roli automaticky nemají. Pokud chtějí snímky zobrazit, musí se do této role přidat sami.

Vlastníci předplatného by měli přiřadit `Application Insights Snapshot Debugger` roli uživatelům, kteří budou kontrolovat snímky. Tato role se dá přiřadit jednotlivým uživatelům nebo skupinám podle vlastníků předplatného pro cílový Application Insights prostředek nebo jeho skupinu prostředků nebo předplatné.

1. V Azure Portal přejděte na prostředek Application Insights.
1. Klikněte na **Řízení přístupu (IAM)**.
1. Klikněte na tlačítko **+ Přidat přiřazení role** .
1. V rozevíracím seznamu **role** vyberte **Application Insights Snapshot Debugger** .
1. Vyhledejte a zadejte jméno uživatele, kterého chcete přidat.
1. Kliknutím na tlačítko **Uložit** přidáte uživatele do role.


> [!IMPORTANT]
> Snímky můžou potenciálně obsahovat osobní a další citlivé informace v hodnotách proměnných a parametrů.

## <a name="view-snapshots-in-the-portal"></a>Zobrazení snímků na portálu

Po výskytu výjimky v aplikaci a vytvoření snímku byste měli mít snímky k zobrazení. Výjimka může trvat 5 až 10 minut, než se dostanou do snímku připraveného a zobrazit na portálu. Chcete-li zobrazit snímky, vyberte v podokně **selhání** tlačítko **operace** při zobrazení karty **operace** , nebo při zobrazení karty **výjimky** vyberte tlačítko **výjimky** :

![Stránka selhání](./media/snapshot-debugger/failures-page.png)

V pravém podokně vyberte operaci nebo výjimku, čímž otevřete podokno **podrobnosti transakce** , a pak vyberte událost výjimky. Pokud je pro danou výjimku k dispozici snímek, v pravém podokně se zobrazí tlačítko **otevřít snímek ladění** s podrobnostmi o [výjimce](./asp-net-exceptions.md).

![Otevřít tlačítko pro vytvoření snímku ladění na výjimce](./media/snapshot-debugger/e2e-transaction-page.png)

V zobrazení snímku ladění se zobrazí zásobník volání a podokno proměnné. Když vyberete rámce zásobníku volání v podokně zásobník volání, můžete zobrazit místní proměnné a parametry pro volání funkce v podokně proměnné.

![Zobrazit snímek ladění na portálu](./media/snapshot-debugger/open-snapshot-portal.png)

Snímky můžou obsahovat citlivé informace a ve výchozím nastavení nejsou dostupné. Chcete-li zobrazit snímky, musíte mít `Application Insights Snapshot Debugger` přiřazenou roli.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Zobrazit snímky v aplikaci Visual Studio 2017 Enterprise nebo vyšší
1. Klikněte na tlačítko **Stáhnout snímek** a stáhněte `.diagsession` soubor, který může být otevřen Visual Studio Enterprise.

2. Pokud chcete `.diagsession` soubor otevřít, musíte mít nainstalovanou součást Snapshot Debugger sady Visual Studio. Komponenta Snapshot Debugger je požadovaná součást úlohy ASP.net v aplikaci Visual Studio a lze ji vybrat ze seznamu jednotlivých komponent v instalačním programu sady Visual Studio. Pokud používáte verzi sady Visual Studio starší než Visual Studio 2017 verze 15,5, bude nutné nainstalovat rozšíření z [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otevření souboru snímku se zobrazí stránka ladění S minimálním výpisem v aplikaci Visual Studio. Kliknutím na **ladění spravovaného kódu** spusťte ladění snímku. Snímek se otevře na řádku kódu, kde byla vyvolána výjimka, aby bylo možné ladit aktuální stav procesu.

    ![Zobrazit snímek ladění v aplikaci Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Stažený snímek obsahuje všechny soubory symbolů, které byly nalezeny na vašem serveru webové aplikace. Tyto soubory symbolů jsou vyžadovány pro přidružení dat snímků ke zdrojovému kódu. Pro App Service aplikace Nezapomeňte povolit nasazení symbolů při publikování webových aplikací.

## <a name="how-snapshots-work"></a>Jak fungují snímky

Snapshot Collector se implementuje jako [procesor telemetrie Application Insights](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Po spuštění aplikace se do kanálu telemetrie vaší aplikace přidá procesor telemetrie Snapshot Collector.
Pokaždé, když vaše aplikace volá [TrackException](./asp-net-exceptions.md#exceptions), Snapshot COLLECTOR vypočítá ID problému z typu vyvolané výjimky a metody throw.
Pokaždé, když vaše aplikace volá TrackException, se pro příslušné ID problému zvýší hodnota čítače. Když čítač dosáhne `ThresholdForSnapshotting` hodnoty, ID problému se přidá do plánu kolekce.

Snapshot Collector také monitoruje výjimky, když jsou vyvolány přihlášením k odběru události [AppDomain. CurrentDomain. FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) . Když se tato událost aktivuje, vypočítává se ID problému výjimky a porovná se s ID problémů v plánu shromažďování.
Pokud se zobrazí shoda, vytvoří se snímek běžícího procesu. Snímku je přiřazen jedinečný identifikátor a tato výjimka je opatřena tímto identifikátorem. Po návratu obslužné rutiny FirstChanceException je vyvolána výjimka zpracována jako normální. Nakonec výjimka dosáhne TrackException metody znovu tam, kde je, společně s identifikátorem snímku, je hlášena Application Insights.

Hlavní proces pokračuje v běhu a obsluhuje přenosy pro uživatele s malým přerušením. Mezitím se snímek předává procesu odeslání snímku. Odeslání snímku odešle s minimálním výpisem a nahraje ho, aby Application Insights společně se všemi relevantními soubory symbolů (. pdb).

> [!TIP]
> - Snímek procesu je pozastavený klon spuštěného procesu.
> - Vytváření snímku trvá přibližně 10 až 20 milisekund.
> - Výchozí hodnota pro `ThresholdForSnapshotting` je 1. To je také minimální hodnota. Proto musí vaše aplikace aktivovat stejnou výjimku **dvakrát** před vytvořením snímku.
> - Nastavte `IsEnabledInDeveloperMode` na hodnotu true, pokud chcete generovat snímky během ladění v sadě Visual Studio.
> - Frekvence vytváření snímků je omezená `SnapshotsPerTenMinutesLimit` nastavením. Ve výchozím nastavení je limit jeden snímek každých deset minut.
> - Nelze nahrávat více než 50 snímků za den.

## <a name="limitations"></a>Omezení

Výchozí doba uchovávání dat je 15 dní. Pro každou instanci Application Insights je povolený maximální počet 50 snímků za den.

### <a name="publish-symbols"></a>Publikovat symboly
Snapshot Debugger vyžaduje, aby soubory symbolů na provozním serveru dekódují proměnné a poskytovaly prostředí ladění v aplikaci Visual Studio.
Verze 15,2 (nebo vyšší) sady Visual Studio 2017 publikuje ve výchozím nastavení symboly pro sestavení vydaných verzí při publikování do App Service. V předchozích verzích musíte do souboru publikačního profilu přidat následující řádek, `.pubxml` aby se symboly publikovaly v režimu vydání:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

U výpočetních a dalších typů Azure se ujistěte, že se soubory symbolů nacházejí ve stejné složce hlavní aplikace. dll (obvykle `wwwroot/bin` ) nebo jsou k dispozici na aktuální cestě.

> [!NOTE]
> Další informace o různých možnostech symbolu, které jsou k dispozici, najdete v dokumentaci k sadě [Visual Studio](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Pro dosažení nejlepších výsledků doporučujeme použít "úplné", "přenosné" nebo "vložené".

### <a name="optimized-builds"></a>Optimalizovaná sestavení
V některých případech nelze místní proměnné zobrazit v sestaveních vydaných verzí z důvodu optimalizace, které jsou aplikovány kompilátorem JIT.
V Azure App Services však Snapshot Collector může deoptimalizovat vyvolání metod, které jsou součástí plánu shromažďování dat.

> [!TIP]
> Pokud chcete získat podporu deoptimalizace, nainstalujte do App Service rozšíření Application Insights lokality.

## <a name="next-steps"></a>Další kroky
Povolit pro aplikaci Application Insights Snapshot Debugger:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Funkce Azure Functions](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Nad Application Insights Snapshot Debugger:
 
* [Nastavte snímkovací body v kódu](/visualstudio/debugger/debug-live-azure-applications) pro získání snímků bez čekání na výjimku.
* [Diagnostika výjimek ve vašich webových aplikacích](./asp-net-exceptions.md) vysvětluje, jak je možné Application Insights zobrazit další výjimky.
* [Inteligentní zjišťování](./proactive-diagnostics.md) automaticky zjišťuje anomálie výkonu.