---
title: Co je Azure Application Insights? | Dokumenty Microsoft
description: Správa výkonu aplikací a monitorování využití živých webových aplikací.  Rozpoznávání, třídění a diagnostika problémů a náhled na to, jak ostatní využívají vaše aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: overview
ms.custom: mvc
ms.date: 06/03/2019
ms.author: mbullwin
ms.openlocfilehash: cdaae4e539d5216cf4950c15349f01b54ae8acd2
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496376"
---
# <a name="what-is-application-insights"></a>Co je Application Insights?
Application Insights je rozšiřitelná služba pro správu výkonu aplikací (APM). Je určena webovým vývojářům na více platformách. Slouží k monitorování živé webové aplikace. Automaticky zjišťuje anomálie ve výkonu. Obsahuje výkonné analytické nástroje pro diagnostiku problémů a pomáhá porozumět, jak vlastně uživatelé vaši aplikaci používají.  Je navržená tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití. Funguje na nejrůznějších platformách, včetně .NET, Node.js a platformě Java EE určený pro aplikace hostované v místním, hybridním nebo jakémkoli veřejném cloudu. Integruje se s vaším procesem DevOps a obsahuje body připojení k celé řadě dalších vývojářských nástrojů. Díky integraci s Visual Studio App Center může monitorovat a analyzovat telemetrii z mobilních aplikací.

## <a name="how-does-application-insights-work"></a>Jak Application Insights funguje?
Do aplikace nainstalujete malý instrumentační balíček a vytvoříte prostředek Application Insights na webu Microsoft Azure Portal. Instrumentace monitoruje vaši aplikaci a odesílá telemetrická data na portál. (Aplikace se dá spustit kdekoli, nemusí být hostovaná v Azure.)

Instrumentovat můžete nejenom aplikaci webové služby, ale také libovolné komponenty na pozadí a JavaScript na samotných webových stránkách. 

![Instrumentace Application Insights ve vaší aplikaci odesílá telemetrii prostředku Application Insights.](./media/app-insights-overview/01-scheme.png)

Kromě toho můžete využít telemetrii z hostitelských prostředí, jako jsou třeba čítače výkonu, diagnostika Azure nebo protokoly Dockeru. Můžete také nastavit webové testy, které webové službě pravidelně odesílají umělé požadavky.

Všechny tyto telemetrické datové proudy jsou integrované na webu Azure Portal, kde můžete nezpracovaná data využít výkonné analytické a vyhledávací nástroje.


### <a name="whats-the-overhead"></a>Jaká je s tím spojená režie?
Dopad na výkon vaší aplikace je velmi malý. Sledovací volání neblokují, spojují se do dávek a odesílají v samostatném vlákně.

## <a name="what-does-application-insights-monitor"></a>Co Application Insights monitoruje?

Služba Application Insights je zaměřená na vývojový tým. Pomáhá pochopit, jaký je výkon vaší aplikace a jak se využívá. Monitoruje tyto parametry:

* **Frekvence požadavků, doby odezvy a míra selhání** – Zjistěte, které stránky jsou nejoblíbenější a v kterou denní dobu a kde jsou vaši uživatelé. Zjistíte, která stránka si vede nejlépe. Pokud se při zvýšení počtu požadavků zvýší i doba odezvy a míra selhání, máte pravděpodobně potíže s prostředky. 
* **Míra závislosti, doby odezvy a míra selhání** – Zjistěte, jestli vás nezpomalují externí služby.
* **Výjimky** – analýza souhrnné statistiky nebo vyberte konkrétní instance a přejít k podrobnostem trasování zásobníku a souvisejících požadavcích. Hlásí se výjimky serveru i prohlížeče.
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
| [**Inteligentní zjišťování a ruční výstrahy**](../../azure-monitor/app/proactive-diagnostics.md)<br/>Automatické výstrahy se přizpůsobí běžným vzorům telemetrie vaší aplikace a aktivují se, když se stane něco neobvyklého. Můžete také [nastavit výstrahy](../../azure-monitor/app/alerts.md) pro konkrétní úrovně vlastních nebo standardních metrik. |![Ukázka výstrah](./media/app-insights-overview/alerts-tn.png) |
| [**Mapa aplikace**](../../azure-monitor/app/app-map.md)<br/>Komponenty vaší aplikace, včetně klíčových metrik a výstrah. |![Mapa aplikace](./media/app-insights-overview/appmap-tn.png)  |
| [**Profiler**](../../azure-monitor/app/profiler.md)<br/>Zkontrolujte profily spouštění u vzorků požadavků. |![Profiler](./media/app-insights-overview/profiler.png) |
| [**Analýza využití**](../../azure-monitor/app/usage-overview.md)<br/>Analyzujte segmentaci a udržení uživatelů.|![Nástroj Udržení](./media/app-insights-overview/retention.png) |
| [**Diagnostické vyhledávání dat instancí**](../../azure-monitor/app/diagnostic-search.md)<br/>Události vyhledávání a filtrování, jako jsou třeba požadavky, výjimky, volání závislosti, trasování protokolů a zobrazení stránek.  |![Telemetrie vyhledávání](./media/app-insights-overview/search-tn.png) |
| [**Průzkumník metrik pro agregovaná data**](../../azure-monitor/app/metrics-explorer.md)<br/>Prozkoumání, filtrování a segmentace agregovaných dat, jako jsou třeba frekvence požadavků, selhání a výjimek, doby odezvy a časy načtení stránek. |![Metriky](./media/app-insights-overview/metrics-tn.png) |
| [**Řídicí panely**](../../azure-monitor/app/overview-dashboard.md)<br/>Propojení dat z různých zdrojů a jejich sdílení s ostatními. Ideální pro aplikace s více komponentami a pro nepřetržité zobrazení v týmové místnosti. |![Ukázka řídicího panelu](./media/app-insights-overview/dashboard-tn.png) |
| [**Live Metrics Stream**](../../azure-monitor/app/live-stream.md)<br/>Při nasazování nového sestavení sledujte tyto ukazatele výkonu téměř v reálném čase, abyste se ujistili, že všechno funguje podle očekávání. |![Ukázka živé metriky](./media/app-insights-overview/live-metrics-tn.png) |
| [**Analýzy**](../../azure-monitor/app/analytics.md)<br/>Tento výkonný dotazovací jazyk umožňuje odpovědět na složité dotazy týkající se využití a výkonu vaší aplikace. |![Ukázka analýzy](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](../../azure-monitor/app/visual-studio.md)<br/>Viz údaje o výkonu v kódu. Ke kódu je možné přejít z trasování zásobníku.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Ladicí program snímků**](../../azure-monitor/app/snapshot-debugger.md)<br/>Laďte vzorky snímků z operací za provozu s hodnotami parametrů.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](../../azure-monitor/app/export-power-bi.md )<br/>Integrujte metriky využití s ostatními funkcemi business intelligence.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Napište kód pro spouštění dotazů nad daty metrik a nezpracovanými daty.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Průběžný export**](../../azure-monitor/app/export-telemetry.md)<br/>Nezpracovaná data se dají hromadně exportovat do úložiště, jakmile dorazí. |![Export](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Jak se Application Insights dá využít?

### <a name="monitor"></a>Monitorování
Nainstalujte Application Insights ve vaší aplikaci, nastavte [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) a:

* Výchozí rezervace [řídicí panel aplikací](../../azure-monitor/app/overview-dashboard.md) pro vaši týmovou místnost a sledujte zatížení, rychlost odezvy a výkon vašich závislostí, načtení stránek a volání AJAX.
* Zjistěte, které požadavky jsou nejpomalejší a u kterých nejčastěji dochází k selhání.
* Při nasazování nové verze sledujte [živý datový proud](../../azure-monitor/app/live-stream.md), abyste se o případném snížená výkonu dozvěděli okamžitě.

### <a name="detect-diagnose"></a>Detekce a diagnostika
Když dostanete výstrahu nebo zjistíte problém:

* Posuďte, kolika uživatelů se top týká.
* Dejte selhání do souvislosti s výjimkami, voláními závislosti a trasováními.
* Prozkoumejte profiler, snímky, výpisy zásobníků a protokoly trasování.

### <a name="build-measure-learn"></a>Vytváření, měření a učení
[Změřte efektivitu](../../azure-monitor/app/usage-overview.md) každé nové funkce, kterou nasadíte.

* Naplánujte měření toho, jak nové obchodní nebo UIX funkce využívají zákazníci.
* Napište do svého kódu vlastní telemetrii.
* Další vývojový cyklus založte na faktických důkazech z vaší telemetrie.

## <a name="get-started"></a>Začínáme
Application Insights je jedna z mnoha služeb hostovaných v Microsoft Azure. Odesílá se do ní telemetrie pro účely analýzy a prezentace. Proto ještě než se pustíte do práce, budete potřebovat předplatné [Microsoft Azure](https://azure.com). Registrace je zdarma. Pokud zvolíte základní [cenový plán](https://azure.microsoft.com/pricing/details/application-insights/) služby Application Insights, nebudou se vám účtovat žádné poplatky, dokud se vaše aplikace nezačne významněji využívat. Pokud vaše organizace už předplatné má, je možné do něj přidat účet Microsoft.

Existuje několik způsobů, jak začít. Použijte ten, který vám nejvíce vyhovuje. K ostatním se můžete vrátit později.

* **Během spuštění: Instrumentujte vaši webové aplikace na serveru.** Ideální pro aplikace, které jsou už nasazené. Vyhněte se jakýmkoli aktualizacím kódu.
  * [**Aplikace ASP.NET hostované ve službě IIS místně nebo na virtuálním počítači**](../../azure-monitor/app/monitor-performance-live-website-now.md)
  * [**Technologie ASP.NET nebo ASP.NET Core aplikacemi hostovanými na Azure Web Apps**](../../azure-monitor/app/azure-web-apps.md)
* **Během vývoje: Přidejte Application Insights do vašeho kódu.** Můžete přizpůsobit shromažďování telemetrie a odesílání další telemetrické údaje.
  * [Aplikace ASP.NET](../../azure-monitor/app/asp-net.md)
  * [Aplikace ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
  * [Konzolové aplikace .NET](../../azure-monitor/app/console.md)
  * [Java](../../azure-monitor/app/java-get-started.md)
  * [Node.js](../../azure-monitor/app/nodejs.md)
  * [Jiné platformy](../../azure-monitor/app/platforms.md)
* **[Instrumentace webových stránek](../../azure-monitor/app/javascript.md)**  pro zobrazení stránek, AJAX a další telemetrie na straně klienta.
* **[Analýza využití mobilních aplikací](../../azure-monitor/learn/mobile-center-quickstart.md)** díky integraci s Visual Studio App Center.
* **[Testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)** – Příkazem ping z našich serverů pravidelně testujte váš web.

## <a name="next-steps"></a>Další postup
Začínáme při spuštění:

* [Server IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Začínáme při vývoji:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)

## <a name="support-and-feedback"></a>Podpora a zpětná vazba
* Otázky a problémy:
  * [Řešení potíží][qna]
  * [Fórum MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Vaše návrhy:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Blog Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Videa

- Externí video: [konfigurace Application Insights s aplikací v ASP.NET](https://www.youtube.com/watch?v=blnGAVgMAfA).
- Externí video: [konfigurace Application Insights pomocí ASP.NET Core a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t).
- Externí video: [konfigurace Application Insights pomocí ASP.NET Core a Visual Studio Code](https://youtu.be/ygGt84GDync).

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
