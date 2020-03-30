---
title: Ladicí program snímek přehledů aplikací Azure pro aplikace .NET
description: Ladění snímků jsou automaticky shromažďovány při vyvolání výjimek v produkčních aplikacích .NET
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 18f43ba90157d71ec9488b6858fa9f41b2ee42a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275760"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ladicí snímky pro výjimky v aplikacích .NET
Pokud dojde k výjimce, můžete automaticky shromažďovat snímek ladění z živé webové aplikace. Snímek zobrazuje stav zdrojového kódu a proměnných v okamžiku, kdy byla vyvolána výjimka. Ladicí program snímek v [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoruje telemetrii výjimek z vaší webové aplikace. Shromažďuje snímky na horní vyvolání výjimky tak, že máte informace, které potřebujete k diagnostice problémů v produkčním prostředí. Zahrnout [snímek kolektoru NuGet balíček](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) ve vaší aplikaci a volitelně konfigurovat parametry kolekce v [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Snímky se zobrazí na [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na portálu Application Insights.

Snímky ladění můžete zobrazit na portálu a podívat se do zásobníku volání a zkontrolovat proměnné v každém rámci zásobníku volání. Chcete-li získat výkonnější ladění se zdrojovým kódem, otevřete snímky s Visual Studio 2019 Enterprise. V sadě Visual Studio můžete také [nastavit snappoints interaktivně pořizovat snímky](https://aka.ms/snappoint) bez čekání na výjimku.

Ladění snímky jsou uloženy po dobu 15 dnů. Tato zásada uchovávání informací je nastavena na základě pro aplikaci. Pokud potřebujete zvýšit tuto hodnotu, můžete požádat o zvýšení otevřením případu podpory na webu Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Povolení ladicího programu snímek přehledů aplikací pro vaši aplikaci
Kolekce snímků je k dispozici pro:
* Rozhraní .NET Framework a ASP.NET aplikace mise .NET Framework 4.5 nebo novější.
* .NET Core 2.0 a ASP.NET core 2.0 aplikace spuštěné v systému Windows.

Jsou podporována následující prostředí:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Cloudové služby Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) se systémem OS řady 4 nebo novějším
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) spuštěné na Windows Server 2012 R2 nebo novějším
* [Virtuální počítače Azure a škálovací sady virtuálních počítačů](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) se systémem Windows Server 2012 R2 nebo novějším
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) se systémem Windows Server 2012 R2 nebo novějším nebo Windows 8.1 nebo novějším

> [!NOTE]
> Klientské aplikace (například WPF, Windows Forms nebo UPW) nejsou podporovány.

Pokud jste povolili debugger snímků, ale snímky se nezobrazují, podívejte se do našeho [průvodce odstraňováním potíží](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Udělení oprávnění

Přístup ke snímkům je chráněn řízením přístupu na základě rolí (RBAC). Chcete-li zkontrolovat snímek, musíte být nejprve přidáni do role nezbytné vlastníkem předplatného.

> [!NOTE]
> Vlastníci a přispěvatelé tuto roli automaticky nemají. Pokud chtějí zobrazit snímky, musí přidat sami do role.

Vlastníci předplatného `Application Insights Snapshot Debugger` by měli přiřadit roli uživatelům, kteří budou kontrolovat snímky. Tuto roli můžou jednotlivým uživatelům nebo skupinám přiřadit vlastníci předplatného pro cílový prostředek Application Insights nebo jeho skupinu prostředků nebo předplatné.

1. Přejděte na prostředek Application Insights na webu Azure Portal.
1. Klikněte na **Řízení přístupu (IAM)**.
1. Klikněte na tlačítko **+Přidat přiřazení role.**
1. V rozevíracím seznamu **Role** vyberte **debugger snímků přehledů aplikací.**
1. Vyhledejte a zadejte jméno, které má uživatel přidat.
1. Kliknutím na tlačítko **Uložit** přidáte uživatele do role.


> [!IMPORTANT]
> Snímky mohou potenciálně obsahovat osobní a jiné citlivé informace v hodnotách proměnných a parametrů.

## <a name="view-snapshots-in-the-portal"></a>Zobrazit snímky na portálu

Po dojde k výjimce ve vaší aplikaci a snímek byl vytvořen, měli byste mít snímky k zobrazení. Může trvat 5 až 10 minut od výjimky, která se vyskytuje na snímek připravený a zobrazitelný z portálu. Chcete-li zobrazit snímky, vyberte v podokně **Selhání** při zobrazení karty **Operace** tlačítko **Operace** nebo při zobrazení karty **Výjimky** vyberte tlačítko **Výjimky:**

![Stránka Selhání](./media/snapshot-debugger/failures-page.png)

Vyberte operaci nebo výjimku v pravém podokně, chcete-li otevřít podokno **Podrobnosti o transakcích mezi koncovými místy,** vyberte událost výjimky. Pokud je pro danou výjimku k dispozici snímek, zobrazí se v pravém podokně tlačítko **Otevřít snímek ladění** s podrobnostmi o [výjimce](../../azure-monitor/app/asp-net-exceptions.md).

![Otevřít tlačítko Snímek ladění na výjimce](./media/snapshot-debugger/e2e-transaction-page.png)

V zobrazení Snímek ladění se zobrazí zásobník volání a podokno proměnných. Když vyberete rámce zásobníku volání v podokně zásobníku volání, můžete zobrazit místní proměnné a parametry pro toto volání funkce v podokně proměnných.

![Zobrazit snímek ladění na portálu](./media/snapshot-debugger/open-snapshot-portal.png)

Snímky mohou obsahovat citlivé informace a ve výchozím nastavení nejsou viditelné. Chcete-li zobrazit snímky, `Application Insights Snapshot Debugger` musíte mít roli přiřazenou.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Zobrazení snímků v sadě Visual Studio 2017 Enterprise nebo vyšší
1. Kliknutím na tlačítko Stáhnout `.diagsession` **snímek** stáhněte soubor, který může otevřít Visual Studio Enterprise.

2. Chcete-li `.diagsession` soubor otevřít, musíte mít nainstalovanou součást Ladič snímků Visual Studio. Komponenta Debugger snímek je požadovanou součástí ASP.net zatížení v sadě Visual Studio a lze ji vybrat ze seznamu jednotlivé součásti v instalačním programu sady Visual Studio. Pokud používáte verzi sady Visual Studio před Visual Studio 2017 verze 15.5, budete muset nainstalovat rozšíření z [webu Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otevření souboru snímku se zobrazí stránka Ladění minidump v sadě Visual Studio. Chcete-li spustit ladění snímku, klepněte na tlačítko **Ladit spravovaný kód.** Snímek se otevře řádku kódu, kde byla vyvolána výjimka, takže můžete ladit aktuální stav procesu.

    ![Zobrazit snímek ladění v sadě Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Stažený snímek obsahuje všechny soubory symbolů, které byly nalezeny na serveru webové aplikace. Tyto soubory symbolů jsou nutné pro přidružení dat snímku ke zdrojovému kódu. U aplikací Služby App Service nezapomeňte při publikování webových aplikací povolit nasazení symbolů.

## <a name="how-snapshots-work"></a>Jak snímky fungují

Kolektor snímků je implementován jako [procesor telemetrie Application Insights](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Při spuštění aplikace je do kanálu telemetrie aplikace přidán procesor sběratele snímků.
Pokaždé, když vaše aplikace volá [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), snímek kolektoru vypočítá ID problému z typu výjimky je vyvolána a metody vyvolání.
Pokaždé, když vaše aplikace volá TrackException, čítač je přírůstek pro příslušné ID problému. Když čítač `ThresholdForSnapshotting` dosáhne hodnoty, ID problému je přidán do plánu kolekce.

Sběratel snímků také sleduje výjimky, protože jsou vyvolány přihlášením k odběru události [AppDomain.CurrentDomain.FirstChanceException.](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) Když tato událost požáry, id problému výjimky je vypočítána a porovnána s ID problému v plánu kolekce.
Pokud je shoda, pak je vytvořen snímek spuštěného procesu. Snímek je přiřazen jedinečný identifikátor a výjimka je označena tímto identifikátorem. Po FirstChanceException obslužná rutina vrátí, vyvolána výjimka je zpracována jako normální. Nakonec výjimka dosáhne TrackException metoda znovu, kde je spolu s identifikátor snímku, je hlášena Application Insights.

Hlavní proces pokračuje v běhu a slouží provozu uživatelům s malým přerušením. Mezitím je snímek předán procesu nahrávání snímků. Nástroj pro nahrávání snímků vytvoří minidump a nahraje jej do application insights spolu se všemi příslušnými soubory symbolu (.pdb).

> [!TIP]
> - Snímek procesu je pozastavený klon spuštěného procesu.
> - Vytvoření snímku trvá přibližně 10 až 20 milisekund.
> - Výchozí hodnota `ThresholdForSnapshotting` pro je 1. To je také minimální hodnota. Proto vaše aplikace musí aktivovat stejnou výjimku **dvakrát** před vytvořením snímku.
> - Nastavte `IsEnabledInDeveloperMode` na hodnotu true, pokud chcete generovat snímky při ladění v sadě Visual Studio.
> - Rychlost vytváření snímků je `SnapshotsPerTenMinutesLimit` omezena nastavením. Ve výchozím nastavení je limit jeden snímek každých deset minut.
> - Nelze nahrát více než 50 snímků za den.

## <a name="limitations"></a>Omezení

Výchozí doba uchovávání dat je 15 dní. Pro každou instanci Application Insights je povolen maximální počet 50 snímků za den.

### <a name="publish-symbols"></a>Publikování symbolů
Ladicí program snímků vyžaduje soubory symbolů na produkčním serveru k dekódování proměnných a k zajištění ladění v sadě Visual Studio.
Verze 15.2 (nebo vyšší) Visual Studia 2017 publikuje symboly pro sestavení verze ve výchozím nastavení při publikování do služby App Service. V předchozích verzích je třeba do souboru `.pubxml` profilu publikování přidat následující řádek, aby byly symboly publikovány v režimu vydání:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pro Azure Compute a další typy se ujistěte, že soubory symbolů jsou ve `wwwroot/bin`stejné složce hlavní aplikace .dll (obvykle) nebo jsou k dispozici na aktuální cestě.

> [!NOTE]
> Další informace o různých možnostech symbolu, které jsou k dispozici, naleznete v [dokumentaci k sadě Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Pro dosažení nejlepších výsledků doporučujeme použít "Full", "Portable" nebo "Embedded".

### <a name="optimized-builds"></a>Optimalizované sestavení
V některých případech místní proměnné nelze zobrazit v sestavení verze z důvodu optimalizace, které jsou použity kompilátorem JIT.
Však ve službě Azure App Services kolekci snímků můžete deoptimalizovat metody vyvolání, které jsou součástí jeho plánu kolekce.

> [!TIP]
> Nainstalujte rozšíření webu Application Insights ve službě App Service, abyste získali podporu deoptimalizace.

## <a name="next-steps"></a>Další kroky
Povolit ladicí program snímek přehledů aplikací pro vaši aplikaci:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače Azure a škálovací sady virtuálních počítačů](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Kromě debuggeru snímků přehledů aplikací:
 
* [Nastavte snappoints v kódu](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) získat snímky bez čekání na výjimku.
* [Diagnostika výjimek ve webových aplikacích](../../azure-monitor/app/asp-net-exceptions.md) vysvětluje, jak zviditelnit další výjimky pro Application Insights.
* [Inteligentní detekce](../../azure-monitor/app/proactive-diagnostics.md) automaticky zjišťuje anomálie výkonu.
