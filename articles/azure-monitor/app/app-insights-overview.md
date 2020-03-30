---
title: Co je Azure Application Insights? | Dokumentace Microsoftu
description: Správa výkonu aplikací a monitorování využití živých webových aplikací.  Rozpoznávání, třídění a diagnostika problémů a náhled na to, jak ostatní využívají vaše aplikace.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: e7cde3ca47afd4db072b734c797a42ecd5a053aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136795"
---
# <a name="what-is-application-insights"></a>Co je Application Insights?
Application Insights, funkce [Azure Monitor](../overview.md), je rozšiřitelná služba Správy výkonu aplikací (APM) pro vývojáře a profesionály DevOps. Použijte jej ke sledování živých aplikací. Automaticky detekuje anomálie výkonu a obsahuje výkonné analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací skutečně dělají.  Je navržená tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje pro aplikace na široké škále platforem, včetně .NET, Node.js a Java EE, hostované místně, hybridní nebo jakýkoli veřejný cloud. Integruje se s vaším procesem DevOps a obsahuje body připojení k celé řadě dalších vývojářských nástrojů. Díky integraci s Visual Studio App Center může monitorovat a analyzovat telemetrii z mobilních aplikací.

## <a name="how-does-application-insights-work"></a>Jak Application Insights funguje?
Do aplikace nainstalujete malý instrumentační balíček a vytvoříte prostředek Application Insights na webu Microsoft Azure Portal. Instrumentace monitoruje vaši aplikaci a odesílá telemetrická data do Azure Monitoru. (Aplikace se dá spustit kdekoli, nemusí být hostovaná v Azure.)

Instrumentovat můžete nejenom aplikaci webové služby, ale také libovolné komponenty na pozadí a JavaScript na samotných webových stránkách. 

![Instrumentace Application Insights ve vaší aplikaci odesílá telemetrii prostředku Application Insights.](./media/app-insights-overview/diagram.png)

Kromě toho můžete využít telemetrii z hostitelských prostředí, jako jsou třeba čítače výkonu, diagnostika Azure nebo protokoly Dockeru. Můžete také nastavit webové testy, které webové službě pravidelně odesílají umělé požadavky.

Všechny tyto telemetrické datové proudy jsou integrované do Azure Monitoru. Na webu Azure Portal můžete použít výkonné analytické a vyhledávací nástroje pro nezpracovaná data.

### <a name="whats-the-overhead"></a>Jaká je s tím spojená režie?
Dopad na výkon vaší aplikace je velmi malý. Sledovací volání neblokují, spojují se do dávek a odesílají v samostatném vlákně.

## <a name="what-does-application-insights-monitor"></a>Co Application Insights monitoruje?

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

* **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky. 
* **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.
* **Výjimky** – analyzujte agregované statistiky nebo vyberte konkrétní instance a projděte si trasování zásobníku a související požadavky. Hlásí se výjimky serveru i prohlížeče.
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
| [**Inteligentní zjišťování a ruční výstrahy**](../../azure-monitor/app/proactive-diagnostics.md)<br/>Nastavte automatická upozornění, která se přizpůsobí normálním vzorům telemetrie vaší aplikace a aktivují se, když je něco mimo obvyklý vzor. Můžete také [nastavit výstrahy](../../azure-monitor/app/alerts.md) na konkrétní úrovně vlastních nebo standardních metrik. |![Ukázka výstrah](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa aplikace**](../../azure-monitor/app/app-map.md)<br/>Prozkoumejte součásti aplikace pomocí klíčových metrik a upozornění. |![Mapa aplikace](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](../../azure-monitor/app/profiler.md)<br/>Zkontrolujte profily spouštění u vzorků požadavků. |![Profiler](./media/app-insights-overview/profiler.png) |
| [**Analýza využití**](../../azure-monitor/app/usage-overview.md)<br/>Analyzujte segmentaci a udržení uživatelů.|![Nástroj Udržení](./media/app-insights-overview/retention.png) |
| [**Diagnostické vyhledávání dat instancí**](../../azure-monitor/app/diagnostic-search.md)<br/>Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.  |![Telemetrie vyhledávání](./media/app-insights-overview/search-tn.png) |
| [**Průzkumník metrik pro agregovaná data**](../../azure-monitor/app/metrics-explorer.md)<br/>Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek. |![Metriky](./media/app-insights-overview/metrics-tn.png) |
| [**Řídicích panelů**](../../azure-monitor/app/overview-dashboard.md)<br/>Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti. |![Ukázka řídicího panelu](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](../../azure-monitor/app/live-stream.md)<br/>Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání. |![Ukázka živé metriky](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analýza**](../../azure-monitor/app/analytics.md)<br/>Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace. |![Ukázka analýzy](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Viz údaje o výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Ladicí program snímků**](../../azure-monitor/app/snapshot-debugger.md)<br/>Laďte vzorky snímků z operací za provozu s hodnotami parametrů.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Integrujte metriky využití s ostatními funkcemi business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**ROZHRANÍ API PRO ODPOČINEK**](https://dev.applicationinsights.io/)<br/>Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Nepřetržitý export**](../../azure-monitor/app/export-telemetry.md)<br/>Nezpracovaná data se dají hromadně exportovat do úložiště, jakmile dorazí. |![Export](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Jak se Application Insights dá využít?

### <a name="monitor"></a>Monitorování
Nainstalujte Application Insights ve vaší aplikaci, nastavte [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) a:

* Podívejte se na výchozí [řídicí panel aplikace](../../azure-monitor/app/overview-dashboard.md) pro týmovou místnost, abyste sledovali zatížení, odezvu a výkon vašich závislostí, načítání stránek a volání AJAX.
* Zjistěte, které požadavky jsou nejpomalejší a u kterých nejčastěji dochází k selhání.
* Při nasazování nové verze sledujte [živý datový proud](../../azure-monitor/app/live-stream.md), abyste se o případném snížená výkonu dozvěděli okamžitě.

### <a name="detect-diagnose"></a>Detekce a diagnostika
Když dostanete výstrahu nebo zjistíte problém:

* Posuďte, kolika uživatelů se top týká.
* Korelovat selhání s výjimkami, volání závislostí a trasování.
* Prozkoumejte profiler, snímky, výpisy zásobníků a protokoly trasování.

### <a name="build-measure-learn"></a>Vytváření, měření a učení
[Změřte efektivitu](../../azure-monitor/app/usage-overview.md) každé nové funkce, kterou nasazujete.

* Naplánujte měření toho, jak nové obchodní nebo UIX funkce využívají zákazníci.
* Napište do svého kódu vlastní telemetrii.
* Další vývojový cyklus založte na faktických důkazech z vaší telemetrie.

## <a name="get-started"></a>Začínáme
Application Insights je jedna z mnoha služeb hostovaných v Microsoft Azure. Odesílá se do ní telemetrie pro účely analýzy a prezentace. Proto ještě než se pustíte do práce, budete potřebovat předplatné [Microsoft Azure](https://azure.com). Registrace je zdarma. Pokud zvolíte základní [cenový plán](https://azure.microsoft.com/pricing/details/application-insights/) služby Application Insights, nebudou se vám účtovat žádné poplatky, dokud se vaše aplikace nezačne významněji využívat. Pokud vaše organizace už předplatné má, je možné do něj přidat účet Microsoft.

Existuje několik způsobů, jak začít. Použijte ten, který vám nejvíce vyhovuje. K ostatním se můžete vrátit později.

* **Během spuštění: Instrumentujte vaši webové aplikace na serveru.** Ideální pro již nasazené aplikace. Vyhněte se jakýmkoli aktualizacím kódu.
  * [**ASP.NET nebo ASP.NET základních aplikací hostovaných v Azure Web Apps**](../../azure-monitor/app/azure-web-apps.md)
  * [**ASP.NET aplikace hostované ve Službě IIS na škálovací sadě virtuálních virtuálních zařízení Azure nebo virtuálního počítače Azure**](../../azure-monitor/app/azure-vm-vmss-apps.md)
  * [**ASP.NET aplikací hostovaných v místním virtuálním počítači iIS**](../../azure-monitor/app/monitor-performance-live-website-now.md)
* **Během vývoje: Přidejte Application Insights do vašeho kódu.** Umožňuje přizpůsobit kolekci telemetrie a odeslat další telemetrii.
  * [ASP.NET aplikace](../../azure-monitor/app/asp-net.md)
  * [ASP.NET základní aplikace](../../azure-monitor/app/asp-net-core.md)
  * [Konzolové aplikace ROZHRANÍ .NET](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Python](../../azure-monitor/app/opencensus-python.md)
  * [Jiné platformy](../../azure-monitor/app/platforms.md)
* **[Instrumentujte webové stránky](../../azure-monitor/app/javascript.md)** pro zobrazení stránky, AJAX a další telemetrii na straně klienta.
* **[Analýza využití mobilních aplikací](../../azure-monitor/learn/mobile-center-quickstart.md)** díky integraci s Visual Studio App Center.
* **[Testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)** – Příkazem ping z našich serverů pravidelně testujte váš web.

## <a name="next-steps"></a>Další kroky
Začínáme při spuštění:

* [Škálovací sady azure virtuálních počítačů a škálovací sady virtuálních strojů Azure hostované v aplikacích hostovaných ve službě IIS](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Server IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Začínáme při vývoji:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)


## <a name="support-and-feedback"></a>Podpora a zpětná vazba
* Otázky a problémy:
  * [Řešení potíží][qna]
  * [Fórum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Vaše návrhy:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../../azure-monitor/learn/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ../../azure-monitor/app/javascript.md
[desktop]: ../../azure-monitor/app/windows-desktop.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[ios]: ../../azure-monitor/learn/mobile-center-quickstart.md
[java]: ../../azure-monitor/app/java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ../../azure-monitor/app/platforms.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
