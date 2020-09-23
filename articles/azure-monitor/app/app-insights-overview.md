---
title: Co je Azure Application Insights? | Dokumentace Microsoftu
description: Správa výkonu aplikací a monitorování využití živých webových aplikací.  Rozpoznávání, třídění a diagnostika problémů a náhled na to, jak ostatní využívají vaše aplikace.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: 4576c8a7657ea3da1f8a732c2efd38302f33300c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974048"
---
# <a name="what-is-application-insights"></a>Co je Application Insights?
Application Insights, funkce [Azure monitor](../overview.md), je rozšiřitelná služba pro správu výkonu aplikací (APM) pro vývojáře a odborníky v oblasti DevOps. Použijte ho k monitorování svých živých aplikací. Automaticky detekuje anomálie výkonu a obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací skutečně dělají.  Je navržená tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje pro aplikace na nejrůznějších platformách, jako jsou .NET, Node.js, Java a Python hostované místně, hybridním nebo jakýmkoli veřejným cloudem. Integruje se s vaším procesem DevOps a obsahuje body připojení k celé řadě dalších vývojářských nástrojů. Díky integraci s Visual Studio App Center může monitorovat a analyzovat telemetrii z mobilních aplikací.

## <a name="how-does-application-insights-work"></a>Jak Application Insights funguje?
Nainstalujete do své aplikace malý balíček instrumentace (SDK) nebo povolíte Application Insights pomocí agenta Application Insights, pokud se [podporuje](./platforms.md). Instrumentace monitoruje vaši aplikaci a směruje data telemetrie do prostředku Azure Application Insights pomocí jedinečného identifikátoru GUID, který označujeme jako klíč instrumentace.

Instrumentovat můžete nejenom aplikaci webové služby, ale také libovolné komponenty na pozadí a JavaScript na samotných webových stránkách. Aplikace a její součásti můžou běžet kdekoli – nemusí být hostované v Azure.

![Instrumentace Application Insights ve vaší aplikaci odesílá telemetrii prostředku Application Insights.](./media/app-insights-overview/diagram.png)

Kromě toho můžete využít telemetrii z hostitelských prostředí, jako jsou třeba čítače výkonu, diagnostika Azure nebo protokoly Dockeru. Můžete také nastavit webové testy, které webové službě pravidelně odesílají syntetické požadavky.

Všechny tyto streamy telemetrie jsou integrované do Azure Monitor. V Azure Portal můžete použít výkonné analytické a vyhledávací nástroje na nezpracovaná data.

### <a name="whats-the-overhead"></a>Jaká je s tím spojená režie?
Dopad na výkon vaší aplikace je malý. Sledovací volání neblokují, spojují se do dávek a odesílají v samostatném vlákně.

## <a name="what-does-application-insights-monitor"></a>Co Application Insights monitoruje?

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

* **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky. 
* **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.
* **Výjimky** – analyzuje agregované statistiky nebo si vyberou konkrétní instance a podrobí se k trasování zásobníku a souvisejícím žádostem. Hlásí se výjimky serveru i prohlížeče.
* **Zobrazení a načítání stránek** – Tyto informace hlásí prohlížeče uživatelů.
* **Volání AJAX** z webových stránek – frekvence, doby odezvy a míry selhání.
* **Počty uživatelů a relací**.
* **Čítače výkonu** ze serverových počítačů s Windows nebo Linuxem, jako je třeba CPU, paměť a využití sítě. 
* **Diagnostika hostitele** z Dockeru nebo Azure. 
* **Protokoly trasování diagnostiky** z vaší aplikace – umožňují zjistit korelaci mezi požadavky a událostmi trasování.
* **Vlastní události a metriky**, které napíšete sami v kódu klienta nebo serveru, ke sledování obchodních událostí, jako například jsou třeba prodané položky nebo vyhrané hry.

## <a name="where-do-i-see-my-telemetry"></a>Kde se zobrazuje telemetrie?

Data se dají prozkoumávat mnoha způsoby. Podívejte se na tyto články:

|  |  |
| --- | --- |
| [**Inteligentní zjišťování a ruční výstrahy**](./proactive-diagnostics.md)<br/>Nastavte automatické výstrahy, které se přizpůsobí normálním vzorům telemetrie a triggeru vaší aplikace, když je něco mimo obvyklý vzor. Můžete také [nastavit výstrahy](../platform/alerts-log.md) na konkrétní úrovně vlastních nebo standardních metrik. |![Ukázka výstrah](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa aplikace**](./app-map.md)<br/>Prozkoumejte komponenty vaší aplikace s klíčovými metrikami a výstrahami. |![Mapa aplikace](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](./profiler.md)<br/>Zkontrolujte profily spouštění u vzorků požadavků. |![Snímek obrazovky ukazuje profily spuštění vzorků požadavků.](./media/app-insights-overview/profiler.png) |
| [**Analýza využití**](./usage-overview.md)<br/>Analyzujte segmentaci a udržení uživatelů.|![Nástroj Udržení](./media/app-insights-overview/retention.png) |
| [**Diagnostické vyhledávání dat instancí**](./diagnostic-search.md)<br/>Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.  |![Telemetrie vyhledávání](./media/app-insights-overview/search-tn.png) |
| [**Průzkumník metrik pro agregovaná data**](../platform/metrics-charts.md)<br/>Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek. |![Metriky](./media/app-insights-overview/metrics-tn.png) |
| [**Řídicí panely**](./overview-dashboard.md)<br/>Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti. |![Ukázka řídicího panelu](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](./live-stream.md)<br/>Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání. |![Ukázka živé metriky](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analýzy**](../log-query/log-query-overview.md)<br/>Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace. |![Ukázka analýzy](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>Viz údaje o výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.|![Snímek obrazovky ukazuje podrobnosti o výjimce v sadě Visual Studio a příklad přechodu na kód z trasování zásobníku.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Snapshot Debugger**](./snapshot-debugger.md)<br/>Laďte vzorky snímků z operací za provozu s hodnotami parametrů.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integrujte metriky využití s ostatními funkcemi business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Průběžný export**](./export-telemetry.md)<br/>Nezpracovaná data se dají hromadně exportovat do úložiště, jakmile dorazí. |![Export](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Jak se Application Insights dá využít?

### <a name="monitor"></a>Monitor
Nainstalujte Application Insights ve vaší aplikaci, nastavte [webové testy dostupnosti](./monitor-web-app-availability.md) a:

* Podívejte se na výchozí [řídicí panel aplikace](./overview-dashboard.md) pro svou týmovou místnost, abyste měli přehled o zatížení, odezvě a výkonu vašich závislostí, načtení stránek a voláních AJAX.
* Zjistěte, které požadavky jsou nejpomalejší a u kterých nejčastěji dochází k selhání.
* Při nasazování nové verze sledujte [živý datový proud](./live-stream.md), abyste se o případném snížená výkonu dozvěděli okamžitě.

### <a name="detect-diagnose"></a>Detekce a diagnostika
Když dostanete výstrahu nebo zjistíte problém:

* Posuďte, kolika uživatelů se top týká.
* Proveďte korelaci selhání s výjimkami, voláními závislostí a trasování.
* Prozkoumejte profiler, snímky, výpisy zásobníků a protokoly trasování.

### <a name="build-measure-learn"></a>Vytváření, měření a učení
[Změřte efektivitu](./usage-overview.md) každé nové funkce, kterou nasazujete.

* Naplánujte měření toho, jak nové obchodní nebo UIX funkce využívají zákazníci.
* Napište do svého kódu vlastní telemetrii.
* Další vývojový cyklus založte na faktických důkazech z vaší telemetrie.

## <a name="get-started"></a>Začínáme
Application Insights je jedna z mnoha služeb hostovaných v Microsoft Azure. Odesílá se do ní telemetrie pro účely analýzy a prezentace. Proto ještě než se pustíte do práce, budete potřebovat předplatné [Microsoft Azure](https://azure.com). Registrace je zdarma. Pokud zvolíte základní [cenový plán](https://azure.microsoft.com/pricing/details/application-insights/) služby Application Insights, nebudou se vám účtovat žádné poplatky, dokud se vaše aplikace nezačne významněji využívat. Pokud vaše organizace už předplatné má, je možné do něj přidat účet Microsoft.

Existuje několik způsobů, jak začít. Použijte ten, který vám nejvíce vyhovuje. K ostatním se můžete vrátit později.

* **Během spuštění: Instrumentujte vaši webové aplikace na serveru.** Ideální pro aplikace, které jsou již nasazeny. Vyhněte se jakýmkoli aktualizacím kódu.
  * [**ASP.NET nebo ASP.NET Core aplikace hostované v Azure Web Apps**](./azure-web-apps.md)
  * [**ASP.NET aplikací hostovaných ve službě IIS na virtuálním počítači Azure nebo v sadě Azure Virtual Machine scaleing**](./azure-vm-vmss-apps.md)
  * [**ASP.NET aplikací hostovaných na místním virtuálním počítači IIS**](./monitor-performance-live-website-now.md)
* **Během vývoje: Přidejte Application Insights do vašeho kódu.** Umožňuje přizpůsobit shromažďování telemetrie a odesílat další telemetrie.
  * [ASP.NET aplikace](./asp-net.md)
  * [ASP.NET Core aplikací](./asp-net-core.md)
  * [Konzolové aplikace .NET](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Jiné platformy](./platforms.md)
* **[Instrumentujte webové stránky](./javascript.md)** pro zobrazení stránky, AJAX a další telemetrie na straně klienta.
* **[Analýza využití mobilních aplikací](../learn/mobile-center-quickstart.md)** díky integraci s Visual Studio App Center.
* **[Testy dostupnosti](./monitor-web-app-availability.md)** – Příkazem ping z našich serverů pravidelně testujte váš web.

## <a name="next-steps"></a>Další kroky
Začínáme při spuštění:

* [Virtuální počítače Azure a škálování sady virtuálních počítačů Azure – aplikace hostované službou IIS](./azure-vm-vmss-apps.md)
* [Server IIS](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

Začínáme při vývoji:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)


## <a name="support-and-feedback"></a>Podpora a zpětná vazba
* Otázky a problémy:
  * [Řešení potíží][qna]
  * [Stránka s otázkou Microsoft Q&](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Vaše návrhy:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../learn/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

