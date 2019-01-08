---
title: Azure Application Insights pro server Windows a role pracovního procesu | Dokumentace Microsoftu
description: Ručně přidejte do aplikace ASP.NET sadu SDK Application Insights k analýze využití, dostupnosti a výkonu.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: c7273a1ef6ef4eab1d8bfadec33eeaacf4d222e9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082678"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>Ruční konfigurace služby Application Insights pro aplikace .NET

Službu [Application Insights](../../application-insights/app-insights-overview.md) můžete nakonfigurovat tak, aby monitorovala celou řadu aplikací nebo aplikačních rolí, komponent a mikroslužeb. Pro webové aplikace a služby nabízí Visual Studio [konfiguraci v jednom kroku](../../azure-monitor/app/asp-net.md). V případě jiných typů aplikací .NET, například u rolí back-end serveru nebo u desktopových aplikací, můžete nakonfigurovat službu Application Insights ručně.

![Příklady tabulek sledování výkonu](./media/windows-services/10-perf.png)

#### <a name="before-you-start"></a>Než začnete

Budete potřebovat:

* Předplatné [Microsoft Azure](https://azure.com). Pokud váš tým nebo společnost má předplatné Azure, vlastník vás do něj může přidat pomocí vašeho [účtu Microsoft](https://live.com).
* Visual Studio 2013 nebo novější.

## <a name="add"></a>1. Volba prostředku služby Application Insights

Prostředkem je místo na webu Azure Portal, kde jsou shromažďována a zobrazena vaše data. Musíte se rozhodnout, jestli chcete vytvořit nový nebo sdílet existující prostředek.

### <a name="part-of-a-larger-app-use-existing-resource"></a>Součást větší aplikace: Použijte existující prostředek

Pokud má vaše webová aplikace několik komponent – například front-end webovou aplikaci a jednu nebo více back-end služeb – pak byste měli telemetrická data ze všech komponent odesílat do stejného prostředku. To umožní jejich zobrazení v rámci jedné Mapy aplikace a bude možné trasovat požadavky mezi jednotlivými komponentami.

Pokud už tedy monitorujete jiné komponenty této aplikace, pak použijte stejný prostředek.

Prostředek otevřete na webu [Azure Portal](https://portal.azure.com/). 

### <a name="self-contained-app-create-a-new-resource"></a>Samostatná aplikace: Vytvoření nového prostředku

Pokud není nová aplikace vázána k ostatním aplikacím, měla by mít svůj vlastní prostředek.

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a vytvořte nový prostředek Application Insights. Vyberte jako typ aplikace ASP.NET.

![Klikněte na tlačítko Nový, Application Insights](./media/windows-services/01-new-asp.png)

Volba typu aplikace nastaví výchozí obsah oken prostředků.

## <a name="2-copy-the-instrumentation-key"></a>2. Zkopírování klíče instrumentace
Klíč identifikuje prostředek. Brzy ho nainstalujete v sadě SDK, aby bylo možné směrovat data do prostředku.

![Klikněte na tlačítko Vlastnosti, vyberte klíč a stiskněte klávesy ctrl + C](./media/windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Instalace balíčku Application Insights v aplikaci
Instalace a konfigurace balíčku Application Insights se liší podle platformy, se kterou právě pracujete. 

1. Ve Visual Studiu klikněte pravým tlačítkem na projekt a zvolte **Spravovat balíčky NuGet**.
   
    ![Kliknutí pravým tlačítkem na projekt a výběr možnosti Spravovat balíčky Nuget](./media/windows-services/03-nuget.png)
2. Nainstalujte balíček služby Application Insights pro aplikace Windows Serveru – Microsoft.ApplicationInsights.WindowsServer.
   
    ![Vyhledání Application Insights](./media/windows-services/04-ai-nuget.png)
   
    *Kterou verzi?*

    Pokud chcete vyzkoušet nejnovější funkce, zaškrtněte políčko u možnosti **Zahrnout předběžné verze**. Informace o tom, jestli potřebujete předběžnou verzi, najdete v příslušné dokumentaci nebo v blozích.
    
    *Mohu použít jiné balíčky?*
   
    Ano. Pokud chcete rozhraní API používat pouze k odesílání vlastních telemetrických dat, zvolte Microsoft.ApplicationInsights. Balíček Windows Serveru zahrnuje rozhraní API spolu s dalšími balíčky, jako jsou například kolekce čítačů výkonu a monitorování závislostí. 

### <a name="to-upgrade-to-future-package-versions"></a>Postup upgradu na budoucí verze balíčku
Čas od času vydáváme nové verze sady SDK.

K upgradu [novou verzi balíčku](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/) otevřete znovu Správce balíčků NuGet a filtrujte nainstalované balíčky. Vyberte **Microsoft.ApplicationInsights.WindowsServer** a zvolte **Upgradovat**.

Pokud jste provedli jakékoli úpravy souboru ApplicationInsights.config, uložte jeho kopii před upgradem a následně slučte změny do nové verze.

## <a name="4-send-telemetry"></a>4. Odeslání telemetrie
**Pokud jste nainstalovali pouze balíček rozhraní API:**

* Nastavte v kódu klíč instrumentace, například v metodě `main()`: 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "`*váš klíč*`";` 
* [Napište si vlastní telemetrii pomocí rozhraní API](../../azure-monitor/app/api-custom-events-metrics.md#ikey).

**Pokud jste nainstalovali jiné balíčky Application Insights**, můžete k nastavení klíče instrumentace použít soubor .config, pokud tomu dáváte přednost:

* Upravit soubor ApplicationInsights.config (který byl nainstalován nástrojem NuGet). Vložte tuto položku těsně před uzavírací značku:
  
    `<InstrumentationKey>`*zkopírovaný klíč instrumentace*`</InstrumentationKey>`
* Ujistěte se, že jsou vlastnosti souboru ApplicationInsights.config v Průzkumníku řešení nastavené na: **Build Action = Content, Copy to Output Directory = Copy**.

Nastavení klíče instrumentace v kódu je užitečné v případě, že chcete [přepínat mezi klíči pro různé konfigurace sestavení](../../azure-monitor/app/separate-resources.md). Pokud klíč nastavíte v kódu, nemusíte ho nastavovat v souboru `.config`.

## <a name="run"></a> Spuštění projektu
Použijte **F5** ke spuštění aplikace a vyzkoušejte: otevření různých stránek k vygenerování nějaké telemetrie.

V sadě Visual Studio zobrazí počet událostí, které byly odeslány.

![Počet událostí v sadě Visual Studio](./media/windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> Zobrazení telemetrických dat
Přihlaste se na portál [Azure Portal](https://portal.azure.com/) a vyhledejte prostředek Application Insights.

Vyhledejte data v tabulkách Přehled. Na první pohled uvidíte pouze jeden nebo dva body. Příklad:

![Proklikejte se k dalším datům](./media/windows-services/12-first-perf.png)

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších metrik. [Další informace o metrikách.](../../azure-monitor/app/web-monitor-performance.md)

### <a name="no-data"></a>Žádná data?
* Použijte aplikaci a otevřete různé stránky tak, aby došlo k vygenerování nějaké telemetrie.
* Otevřete dlaždici [Vyhledávání](../../azure-monitor/app/diagnostic-search.md) a zobrazte jednotlivé události. Někdy trvá událostem trochu déle, než se dostanou skrz kanály metriky.
* Počkejte několik sekund a klikněte na tlačítko **Aktualizovat**. Grafy se pravidelně samy aktualizují, ale můžete je aktualizovat ručně, pokud čekáte na zobrazení některých dat.
* Viz [Poradce při potížích](../../azure-monitor/app/troubleshoot-faq.md).

## <a name="publish-your-app"></a>Publikování aplikace
Nyní nasaďte aplikaci na server nebo do Azure a sledujte shromažďování dat.

![K publikování aplikace použijte sadu Visual Studio](./media/windows-services/15-publish.png)

Při spuštění v režimu ladění prochází telemetrie skrz kanál, takže byste měli vidět zobrazení dat během několika sekund. Při nasazení aplikace v rámci konfigurace verze se data hromadí pomaleji.

### <a name="no-data-after-you-publish-to-your-server"></a>Žádná data po publikování na serveru?
Otevřete v bráně firewall serveru porty pro odchozí provoz. Na [této stránce](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses) najdete seznam požadovaných adres. 

### <a name="trouble-on-your-build-server"></a>Potíže na vašem serveru sestavení?
Podívejte se na [tuto položku Řešení potíží](../../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild).

> [!NOTE]
> Pokud vaše aplikace generuje mnoho telemetrických dat, sníží modul adaptivního vzorkování automaticky objem dat odesílaných na portál tím, že budou odesílány pouze reprezentativní vzorky událostí. Události, které se vztahují ke stejnému požadavku, však budou vybrány nebo nevybrány jako skupina, takže mohou procházet mezi souvisejícími událostmi. 
> [Další informace o vzorkování](../../azure-monitor/app/sampling.md).
> 
> 

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup
* [Přidejte více telemetrie](../../azure-monitor/app/asp-net-more.md) a získejte komplexní náhled na svoji aplikaci.

