---
title: Nastavení analýzy webových aplikací pro ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Nakonfigurujte výkon, dostupnost a nástroje analýzy chování uživatelů pro váš web ASP.NET hostovaný místně nebo v Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: mbullwin
ms.openlocfilehash: 86c0343a3492bf91eedda9303e3c6ac9cf86c4c3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947315"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Nastavení Application Insights pro web ASP.NET

Tímto postupem je možné konfigurovat webovou aplikaci ASP.NET tak, aby odesílala telemetrická data do služby [Azure Application Insights](app-insights-overview.md). Funguje pro aplikace ASP.NET, které jsou hostované buď na vašem vlastním serveru služby IIS v místním prostředí, nebo v cloudu. Můžete získat grafy a výkonný dotazovací jazyk, který vám pomůže porozumět výkonu vaší aplikace a způsobu, jakým ji uživatelé používají, a také automatické výstrahy v případě selhání nebo problémů s výkonem. Celá řada vývojářů považuje tyto funkce za skvělé (a ony jsou), v případě potřeby ale můžete telemetrická data také rozšířit a přizpůsobit.

Nastavení je otázkou několika kliknutí v sadě Visual Studio. Máte možnost vyhnout se placení poplatků, pokud objem telemetrických dat omezíte. Díky tomu můžete experimentovat a ladit nebo monitorovat server s menším množstvím uživatelů. Pokud se později rozhodnete, že chcete pokračovat a monitorovat svůj provozní server, můžete limit snadno zvýšit.

## <a name="prerequisites"></a>Požadavky
Pro přidání Application Insights na web ASP.NET potřebujete:

- Nainstalovat sadu [Visual Studio 2017 pro Windows](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="ide"></a>Krok 1: Přidání sady Application Insights SDK

> [!IMPORTANT]
> Postup pro přidání Application Insights se liší podle typu šablony ASP.NET. Pokud používáte **prázdnou** šablonu nebo šablonu **Mobilní aplikace Azure**, vyberte **Projekt** > **Přidat Telemetrii Application Insights**. Pokyny pro všechny ostatní šablony ASP.NET najdete níže. 

Klikněte pravým tlačítkem na název vaší webové aplikace v Průzkumníku řešení a zvolte **Konfigurovat Application Insights**.

![Snímek obrazovky Průzkumníka řešení se zvýrazněnou možností Konfigurovat Application Insights](./media/app-insights-asp-net/0001-configure-application-insights.png)

(V závislosti na verzi Application Insights SDK se může zobrazit výzva k upgradu na nejnovější verzi SDK. Pokud se tato výzva zobrazí, vyberte **Aktualizovat sadu SDK**.)

![Snímek obrazovky: Je dostupná nová verze sady Microsoft Application Insights SDK. Zvýrazněná možnost Aktualizovat sadu SDK](./media/app-insights-asp-net/0002-update-sdk.png)

Obrazovka Konfigurace Application Insights:

Vyberte **Začít zdarma**.

![Snímek obrazovky stránky registrace vaší aplikace v Application Insights](./media/app-insights-asp-net/0004-start-free.png)

Pokud chcete nastavit skupinu prostředků nebo umístění, kde jsou vaše data uložená, klikněte na **Konfigurovat nastavení**. Skupiny prostředků slouží k řízení přístupu k datům. Pokud například máte několik aplikací, které tvoří součást stejného systému, můžete jejich data Application Insights ukládat do stejné skupiny prostředků.

 Vyberte **Zaregistrovat**. 

![Snímek obrazovky stránky registrace vaší aplikace v Application Insights](./media/app-insights-asp-net/0005-register-ed.png)

 Telemetrie se bude posílat na web [Azure Portal](https://portal.azure.com), jak během ladění aplikace, tak po jejím publikování.
> [!NOTE]
> Pokud během ladění nechcete na portál odesílat telemetrická data, stačí přidat do aplikace sadu SDK Application Insights, ale nekonfigurovat prostředek na portálu. Během ladění se telemetrická data zobrazují v sadě Visual Studio. Později se můžete na tuto stránku konfigurace vrátit, nebo počkat až po nasazení aplikace a [přepnout na telemetrie za běhu](app-insights-monitor-performance-live-website-now.md).

## <a name="run"></a>Krok 2: Spuštění aplikace
Spusťte aplikaci pomocí F5. Otevřete různé stránky k vygenerování nějaké telemetrie.

V sadě Visual Studio se zobrazí počet zaprotokolovaných událostí.

![Snímek obrazovky sady Visual Studio. Během ladění se zobrazí tlačítko Application Insights.](./media/app-insights-asp-net/0006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Krok 3: Zobrazení vašich telemetrických dat
Telemetrii můžete zobrazit v sadě Visual Studio nebo na webovém portálu Application Insights. Hledáním v rámci telemetrických dat v sadě Visual Studio si můžete usnadnit ladění aplikace. Když bude váš systém v provozu, můžete monitorovat výkon a využití na webovém portálu. 

### <a name="see-your-telemetry-in-visual-studio"></a>Zobrazení telemetrických dat v sadě Visual Studio

Pokud chcete v sadě Visual Studio zobrazit data Application Insights:  Vyberte **Průzkumník řešení** > **Připojené služby** > klikněte pravým tlačítkem na **Application Insights** a potom klikněte na **Hledat aktivní telemetrii**.

V okně Visual Studio Application Insights Search se zobrazí telemetrie vygenerovaná na straně serveru vaší aplikace. Experimentujte s filtry a klikněte na události, které chcete zobrazit podrobněji.

![Snímek obrazovky zobrazení Data z relace ladění v okně Application Insights.](./media/app-insights-asp-net/55.png)

> [!Tip]
> Pokud se žádná data nezobrazí, ujistěte se, že je časový rozsah správný, a klikněte na ikonu Search.

[Další informace týkající se nástrojů Application Insights v sadě Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Zobrazení telemetrických dat na webovém portálu

Telemetrická data můžete zobrazit také na webovém portálu Application Insights (pokud jste se nerozhodli nainstalovat pouze sadu SDK). Portál obsahuje více grafů, analytických nástrojů a zobrazení nad několika součástmi než sada Visual Studio. Portál poskytuje také výstrahy.

Otevřete prostředek Application Insights. Buď se přihlaste na [Azure Portal](https://portal.azure.com/) a najděte ho tady, nebo vyberte **Průzkumník řešení** > **Připojené služby** > klikněte pravým tlačítkem na **Application Insights** > **Otevřít portál Application Insights** a přejděte na něj.

Portál otevře zobrazení telemetrie z vaší aplikace.

![Snímek obrazovky stránky s přehledem Application Insights](./media/app-insights-asp-net/66.png)

Po kliknutí na kteroukoli dlaždici nebo graf se zobrazí podrobnější údaje.

[Další informace o používání Application Insights na portálu Azure Portal](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Krok 4: Publikování aplikace
Publikování aplikace na serveru služby IIS nebo do Azure. Sledujte [Živé vysílání metrik](app-insights-metrics-explorer.md#live-metrics-stream) a ověřte, zda vše běží hladce.

Telemetrie vzniká na portálu Application Insights, kde můžete monitorovat metriky, vyhledávat telemetrii a nastavovat [řídicí panely](app-insights-dashboards.md). Můžete také použít výkonný [dotazovací jazyk Log Analytics](https://aka.ms/LogAnalyticsLanguage) k analýze využití a výkonu nebo k hledání konkrétních událostí.

Můžete také dále analyzovat telemetrii v sadě [Visual Studio](app-insights-visual-studio.md) pomocí nástrojů jako vyhledávání diagnostiky a [Trendy](app-insights-visual-studio-trends.md).

> [!NOTE]
> Pokud vaše aplikace odesílá dostatek telemetrie k dosažení [limitů omezení](app-insights-pricing.md#limits-summary), zapne se automatické [vzorkování](app-insights-sampling.md). Vzorkování snižuje množství telemetrie odesílané z vaší aplikace při zachování korelovaných dat k diagnostickým účelům.
>
>

## <a name="land"></a> Nyní je vše připraveno.

Blahopřejeme! Nainstalovali jste do aplikace balíček Application Insights a nakonfigurovali jste pro ni odesílání telemetrických dat do služby Application Insights v Azure.

![Diagram přesunu telemetrie](./media/app-insights-asp-net/01-scheme.png)

Prostředek Azure, který přijímá telemetrická data aplikace, je určen *instrumentačním klíčem*. Tento klíč najdete v souboru ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Upgrade na budoucí verze sady SDK
Pokud chcete upgradovat na [novou verzi sady SDK](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), otevřete **Správce balíčků NuGet** a filtrujte nainstalované balíčky. Vyberte **Microsoft.ApplicationInsights.Web** a zvolte **Upgradovat**.

Pokud jste provedli jakékoli úpravy souboru ApplicationInsights.config, před upgradem si uložte jeho kopii. Potom slučte změny do nové verze.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Další postup

K dispozici jsou i alternativní témata, na která se v případě zájmu můžete podívat:

* [Instrumentace webové aplikace za běhu](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

### <a name="more-telemetry"></a>Další telemetrická data

* **[Údaje o prohlížečích a o načítání stránek](app-insights-javascript.md)** – Vložte do svých webových stránek fragment kódu.
* **[Dosažení podrobnějšího monitorování závislostí a výjimek](app-insights-monitor-performance-live-website-now.md)** – Nainstalujte si na server Monitorování stavu.
* **[Naprogramujte vlastní události](app-insights-api-custom-events-metrics.md)**, které počítají a měří čas nebo akce uživatelů.
* **[Získání dat protokolu](app-insights-asp-net-trace-logs.md)** – Zjišťujte korelaci dat protokolu s telemetrickými daty.

### <a name="analysis"></a>Analýza

* **[Práce s Application Insights v sadě Visual Studio](app-insights-visual-studio.md)**<br/>Zahrnuje informace o ladění pomocí telemetrie, diagnostických hledáních a podrobném procházení kódem.
* **[Práce s portálem Application Insights](app-insights-dashboards.md)**<br/> Zahrnuje informace o řídicích panelech, výkonných nástrojích pro diagnostiku a analýzy, výstrahách, aktivních mapách závislostí vaší aplikace a exportu telemetrie.
* **[Analytics](app-insights-analytics-tour.md)** – Výkonný dotazovací jazyk.

### <a name="alerts"></a>Výstrahy

* [Testy dostupnosti:](app-insights-monitor-web-app-availability.md) Vytvářejte testy, abyste ověřili viditelnost svého webu na internetu.
* [Inteligentní diagnostika:](app-insights-proactive-diagnostics.md) Tyto testy se spouštějí automaticky, takže je nemusíte nijak nastavovat. Upozorní vás, pokud má aplikace nezvykle velký podíl neúspěšných požadavků.
* [Upozornění na metriku:](app-insights-alerts.md) Nastavte si je a nechte se upozornit, pokud metrika překročí mezní hodnotu. Upozornění můžete nastavit u vlastních metrik, které v aplikaci naprogramujete.

### <a name="automation"></a>Automation

* [Automatizace vytvoření prostředku Application Insights](app-insights-powershell.md)
