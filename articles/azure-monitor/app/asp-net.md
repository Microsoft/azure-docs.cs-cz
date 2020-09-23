---
title: Nastavení analýzy webových aplikací pro ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Nakonfigurujte nástroje pro analýzu výkonu, dostupnosti a chování uživatelů pro váš web ASP.NET, který je hostovaný místně nebo v Azure.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: c07e7c8e7bd710cb591719fe8d53a3bad6ca2ee0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973793"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Nastavení Application Insights pro web ASP.NET

Tímto postupem je možné konfigurovat webovou aplikaci ASP.NET tak, aby odesílala telemetrická data do služby [Azure Application Insights](./app-insights-overview.md). Funguje pro aplikace ASP.NET, které jsou hostované buď na vašem vlastním serveru služby IIS v místním prostředí, nebo v cloudu. Můžete získat grafy a výkonný dotazovací jazyk, který vám pomůže porozumět výkonu vaší aplikace a způsobu, jakým ji uživatelé používají, a také automatické výstrahy v případě selhání nebo problémů s výkonem. Celá řada vývojářů považuje tyto funkce za skvělé (a ony jsou), v případě potřeby ale můžete telemetrická data také rozšířit a přizpůsobit.

Nastavení je otázkou několika kliknutí v sadě Visual Studio. Máte možnost vyhnout se placení poplatků, pokud objem telemetrických dat omezíte. Tato funkce umožňuje experimentovat a ladit nebo monitorovat lokalitu, která nemá mnoho uživatelů. Pokud se později rozhodnete, že chcete pokračovat a monitorovat svůj provozní server, můžete limit snadno zvýšit.

## <a name="prerequisites"></a>Požadavky
Pro přidání Application Insights na web ASP.NET potřebujete:

- Nainstalujte [Visual Studio 2019 pro Windows](https://www.visualstudio.com/downloads/) s následujícími úlohami:
    - Vývoj pro ASP.NET a web (zrušení kontroly volitelných součástí)
    - Vývoj pro Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="step-1-add-the-application-insights-sdk"></a><a name="ide"></a>Krok 1: Přidání sady Application Insights SDK

> [!IMPORTANT]
> Snímky obrazovky v tomto příkladu vycházejí ze sady Visual Studio 2017 verze 15.9.9 a novější. Prostředí pro přidání Application Insights se liší ve verzích sady Visual Studio i typu šablony ASP.NET. Starší verze můžou mít alternativní text, například "konfigurace Application Insights".

V Průzkumník řešení klikněte pravým tlačítkem myši na název vaší webové aplikace a vyberte **Přidat**  >  **telemetrie Application Insights**

![Snímek obrazovky Průzkumníka řešení se zvýrazněnou možností Konfigurovat Application Insights](./media/asp-net/add-telemetry-new.png)

(V závislosti na verzi Application Insights SDK se může zobrazit výzva k upgradu na nejnovější verzi SDK. Pokud se tato výzva zobrazí, vyberte **Aktualizovat sadu SDK**.)

![Snímek obrazovky: Je dostupná nová verze sady Microsoft Application Insights SDK. Zvýrazněná možnost Aktualizovat sadu SDK](./media/asp-net/0002-update-sdk.png)

Obrazovka Konfigurace Application Insights:

Vyberte **Začínáme**.

![Snímek obrazovky se zobrazenou stránkou Application Insights a tlačítkem Začínáme.](./media/asp-net/00004-start-free.png)

Pokud chcete nastavit skupinu prostředků nebo umístění, kde jsou vaše data uložená, klikněte na **Konfigurovat nastavení**. Skupiny prostředků slouží k řízení přístupu k datům. Pokud například máte několik aplikací, které tvoří součást stejného systému, můžete jejich data Application Insights ukládat do stejné skupiny prostředků.

 Vyberte **Zaregistrovat**.

![Snímek obrazovky stránky registrace vaší aplikace v Application Insights](./media/asp-net/00005-register-ed.png)

 Vyberte **projekt**  >  **Spravovat balíčky balíčků NuGet**  >  **Zdroj: NuGet.org** > ověřte, že máte nejnovější stabilní verzi sady Application Insights SDK.

 Telemetrie se bude posílat na web [Azure Portal](https://portal.azure.com), jak během ladění aplikace, tak po jejím publikování.
> [!NOTE]
> Pokud během ladění nechcete na portál odesílat telemetrická data, stačí přidat do aplikace sadu SDK Application Insights, ale nekonfigurovat prostředek na portálu. Během ladění se telemetrická data zobrazují v sadě Visual Studio. Později se můžete na tuto stránku konfigurace vrátit, nebo počkat až po nasazení aplikace a [přepnout na telemetrie za běhu](./status-monitor-v2-overview.md).

## <a name="step-2-run-your-app"></a><a name="run"></a> Krok 2: spuštění aplikace
Spusťte aplikaci pomocí F5. Otevřete různé stránky k vygenerování nějaké telemetrie.

V sadě Visual Studio se zobrazí počet zaprotokolovaných událostí.

![Snímek obrazovky sady Visual Studio. Během ladění se zobrazí tlačítko Application Insights.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Krok 3: Zobrazení vašich telemetrických dat
Telemetrii můžete zobrazit v sadě Visual Studio nebo na webovém portálu Application Insights. Hledáním v rámci telemetrických dat v sadě Visual Studio si můžete usnadnit ladění aplikace. Když bude váš systém v provozu, můžete monitorovat výkon a využití na webovém portálu. 

### <a name="see-your-telemetry-in-visual-studio"></a>Zobrazení telemetrických dat v sadě Visual Studio

Pokud chcete v sadě Visual Studio zobrazit data Application Insights:  Vyberte **Průzkumník řešení**  >  **připojené služby** > klikněte pravým tlačítkem na **Application Insights**a pak klikněte na **Hledat živou telemetrii**.

V okně Visual Studio Application Insights Search se zobrazí telemetrie vygenerovaná na straně serveru vaší aplikace. Experimentujte s filtry a klikněte na události, které chcete zobrazit podrobněji.

![Snímek obrazovky zobrazení Data z relace ladění v okně Application Insights.](./media/asp-net/55.png)

> [!Tip]
> Pokud se žádná data nezobrazí, ujistěte se, že je časový rozsah správný, a klikněte na ikonu Search.

[Další informace týkající se nástrojů Application Insights v sadě Visual Studio](./visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Zobrazení telemetrických dat na webovém portálu

Telemetrická data můžete zobrazit také na webovém portálu Application Insights (pokud jste se nerozhodli nainstalovat pouze sadu SDK). Portál obsahuje více grafů, analytických nástrojů a zobrazení nad několika součástmi než sada Visual Studio. Portál poskytuje také výstrahy.

Otevřete prostředek Application Insights. Buď se přihlaste na [Azure Portal](https://portal.azure.com/) a najděte ho tady, nebo vyberte **Průzkumník řešení** > **Připojené služby** > klikněte pravým tlačítkem na **Application Insights** > **Otevřít portál Application Insights** a přejděte na něj.

Portál otevře zobrazení telemetrie z vaší aplikace.

![Snímek obrazovky stránky s přehledem Application Insights](./media/asp-net/007.png)

Po kliknutí na kteroukoli dlaždici nebo graf se zobrazí podrobnější údaje.

## <a name="step-4-publish-your-app"></a>Krok 4: Publikování aplikace
Publikování aplikace na serveru služby IIS nebo do Azure. Sledujte [Živé vysílání metrik](./live-stream.md) a ověřte, zda vše běží hladce.

Vaše telemetrie se vytváří na portálu Application Insights, kde můžete monitorovat metriky, prohledávat telemetrii. Můžete také použít výkonný [dotazovací jazyk Kusto](/azure/kusto/query/) k analýze využití a výkonu nebo k hledání konkrétních událostí.

Můžete také dále analyzovat telemetrii v sadě [Visual Studio](./visual-studio.md) pomocí nástrojů jako vyhledávání diagnostiky a [Trendy](./visual-studio-trends.md).

> [!NOTE]
> Pokud vaše aplikace odesílá dostatek telemetrie k dosažení [limitů omezení](./pricing.md#limits-summary), zapne se automatické [vzorkování](./sampling.md). Vzorkování snižuje množství telemetrie odesílané z vaší aplikace při zachování korelovaných dat k diagnostickým účelům.
>
>

## <a name="youre-all-set"></a><a name="land"></a> Všechno je nastavené.

Gratulujeme! Nainstalovali jste do aplikace balíček Application Insights a nakonfigurovali jste pro ni odesílání telemetrických dat do služby Application Insights v Azure.

Prostředek Azure, který přijímá telemetrická data aplikace, je určen *instrumentačním klíčem*. Tento klíč najdete v souboru ApplicationInsights.config.


## <a name="upgrade-to-future-sdk-versions"></a>Upgrade na budoucí verze sady SDK

* [Zpráva k vydání verze](./release-notes.md)

Chcete-li upgradovat na novou verzi sady SDK, otevřete **Správce balíčků NuGet**a filtrujte nainstalované balíčky. Vyberte **Microsoft. ApplicationInsights. Web**a zvolte **upgradovat**.

Pokud jste provedli jakékoli úpravy souboru ApplicationInsights.config, před upgradem si uložte jeho kopii. Potom slučte změny do nové verze.

## <a name="next-steps"></a>Další kroky

K dispozici jsou i alternativní témata, na která se v případě zájmu můžete podívat:

* [Instrumentace webové aplikace za běhu](./monitor-performance-live-website-now.md)
* [Azure Cloud Services](./cloudservices.md)

### <a name="more-telemetry"></a>Další telemetrická data

* **[Údaje o prohlížečích a o načítání stránek](./javascript.md)** – Vložte do svých webových stránek fragment kódu.
* **[Dosažení podrobnějšího monitorování závislostí a výjimek](./monitor-performance-live-website-now.md)** – Nainstalujte si na server Monitorování stavu.
* **[Vlastní události kódu](./api-custom-events-metrics.md)** pro počet, čas nebo měření akcí uživatele.
* **[Získání dat protokolu](./asp-net-trace-logs.md)** – Zjišťujte korelaci dat protokolu s telemetrickými daty.

### <a name="analysis"></a>Analýza

* **[Práce s Application Insights v sadě Visual Studio](./visual-studio.md)**<br/>Zahrnuje informace o ladění pomocí telemetrie, diagnostických hledáních a podrobném procházení kódem.
* **[Analytics](../log-query/get-started-portal.md)** – Výkonný dotazovací jazyk.

### <a name="alerts"></a>Výstrahy

* [Testy dostupnosti:](./monitor-web-app-availability.md) Vytvářejte testy, abyste ověřili viditelnost svého webu na internetu.
* [Inteligentní diagnostika:](./proactive-diagnostics.md) Tyto testy se spouštějí automaticky, takže je nemusíte nijak nastavovat. Upozorní vás, pokud má aplikace nezvykle velký podíl neúspěšných požadavků.
* [Výstrahy metriky](../platform/alerts-log.md): Nastavte výstrahy, které vás upozorní, pokud metrika překračuje prahovou hodnotu. Upozornění můžete nastavit u vlastních metrik, které v aplikaci naprogramujete.

### <a name="automation"></a>Automation

* [Automatizace vytvoření prostředku Application Insights](./powershell.md)

