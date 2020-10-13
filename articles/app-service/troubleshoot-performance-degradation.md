---
title: Řešení potíží s snížením výkonu
description: Zjistěte, jak řešit potíže s pomalými problémy s výkonem aplikace v Azure App Service, včetně monitorování chování aplikace, shromažďování dat a zmírnění problému.
tags: top-support-issue
keywords: výkon webové aplikace, pomalá aplikace, aplikace je pomalá.
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: a9d3de5d4a88c782ad541ceb4916ec90a3bdd7b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958248"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Řešení potíží s výkonem pomalých aplikací v Azure App Service
Tento článek vám pomůže při řešení problémů s výkonem pomalých aplikací v [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a ve Stack Overflowch fórech](https://azure.microsoft.com/support/forums/). Případně můžete také použít incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="symptom"></a>Příznak
Při procházení aplikace se stránky načítají pomalu a někdy vypršel časový limit.

## <a name="cause"></a>Příčina
Tento problém je často způsoben problémy na úrovni aplikace, například:

* síťové požadavky trvá dlouhou dobu
* neefektivní kód aplikace nebo databázové dotazy
* aplikace s využitím vysoké paměti nebo procesoru
* selhání aplikace z důvodu výjimky

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Řešení potíží se dá rozdělit na tři různé úkoly v sekvenčním pořadí:

1. [Sledování a monitorování chování aplikace](#observe)
2. [Shromažďování dat](#collect)
3. [Zmírnit problém](#mitigate)

[App Service](overview.md) poskytuje v každém kroku různé možnosti.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sledujte a sledujte chování aplikace
#### <a name="track-service-health"></a>Sledovat stav služby
Microsoft Azure publicizes pokaždé, když dojde k přerušení služby nebo snížení výkonu. Stav služby můžete sledovat na [Azure Portal](https://portal.azure.com/). Další informace najdete v tématu [sledování stavu služby](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Monitorování aplikace
Tato možnost umožňuje zjistit, jestli má aplikace nějaké problémy. V okně vaší aplikace klikněte na dlaždici **požadavky a chyby** . Okno **metrika** zobrazuje všechny metriky, které můžete přidat.

Některé metriky, které můžete chtít monitorovat pro vaši aplikaci, jsou

* Průměrná pracovní sada paměti
* Průměrná doba odezvy
* Čas procesoru
* Pracovní sada paměti
* Žádosti

![monitorování výkonu aplikace](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Další informace naleznete v tématech:

* [Monitorování aplikací v Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../azure-monitor/platform/alerts-overview.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorovat stav webového koncového bodu
Pokud používáte aplikaci v cenové úrovni **Standard** , App Service vám umožní monitorovat dva koncové body ze tří geografických umístění.

Monitorování koncového bodu konfiguruje webové testy z geograficky distribuovaných umístění, která testují dobu odezvy a dobu provozu webových adres URL. Test provede operaci HTTP GET na webové adrese URL a určí dobu odezvy a dobu provozu z každého umístění. Každé nakonfigurované umístění spouští test každých pět minut.

Doba provozu se monitoruje pomocí kódů HTTP Response a doba odezvy se měří v milisekundách. Test monitorování se nezdařil, pokud je kód odpovědi HTTP větší nebo roven 400, nebo pokud odpověď trvá déle než 30 sekund. Koncový bod se považuje za dostupný, pokud se jeho testy monitorování zdaří ze všech zadaných umístění.

Pokud ho chcete nastavit, přečtěte si téma [monitorování aplikací v Azure App Service](web-sites-monitor.md).

Podívejte se také na téma [udržování webů Azure a monitorování koncových bodů – s Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) pro video o monitorování koncového bodu.

#### <a name="application-performance-monitoring-using-extensions"></a>Application Performance Monitoring pomocí rozšíření
Můžete také monitorovat výkon aplikace pomocí *rozšíření webu*.

Každá aplikace App Service poskytuje rozšiřitelný koncový bod správy, který umožňuje používat výkonné sady nástrojů nasazených jako rozšíření webu. Mezi rozšíření patří: 

- Editory zdrojového kódu, jako je například [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Nástroje pro správu připojených prostředků, například databáze MySQL připojené k aplikaci.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) je rozšíření webu pro monitorování výkonu, které je dostupné taky. Chcete-li použít Application Insights, znovu sestavte kód pomocí sady SDK. Můžete také nainstalovat rozšíření, které poskytuje přístup k dalším datům. Sada SDK umožňuje psát kód pro sledování využití a výkonu vaší aplikace podrobněji. Další informace najdete v tématu [monitorování výkonu ve webových aplikacích](../azure-monitor/app/web-monitor-performance.md).

<a name="collect"></a>

### <a name="2-collect-data"></a>2. shromažďování dat
App Service poskytuje diagnostické funkce pro protokolování informací z webového serveru i z webové aplikace. Tyto informace jsou rozdělené na diagnostiku webových serverů a Application Diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Povolit diagnostiku webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

* **Podrobné protokolování chyb** – podrobné informace o chybě pro stavové kódy http indikující selhání (Stavový kód 400 nebo vyšší). Může to obsahovat informace, které vám pomůžou určit, proč Server vrátil kód chyby.
* **Trasování chybných požadavků** – podrobné informace o neúspěšných žádostech, včetně trasování komponent služby IIS použitých ke zpracování žádosti a doby trvání jednotlivých komponent. To může být užitečné, pokud se pokoušíte zvýšit výkon aplikace nebo izolovat, co způsobuje konkrétní chybu protokolu HTTP.
* **Protokolování webového serveru** – informace o transakcích HTTP pomocí formátu souboru protokolu W3C Extended. To je užitečné při určování celkových metrik aplikace, jako je počet zpracovaných požadavků nebo kolik požadavků pochází z konkrétní IP adresy.

#### <a name="enable-application-diagnostics"></a>Povolit diagnostiku aplikace
K dispozici je několik možností, jak shromažďovat data o výkonu aplikací z App Service, profilovat aplikaci v reálném čase ze sady Visual Studio nebo upravit kód aplikace a Protokolovat další informace a trasování. Můžete zvolit možnosti na základě toho, kolik přístupu máte k aplikaci a co jste si vyznamenali v nástrojích pro monitorování.

##### <a name="use-application-insights-profiler"></a>Použít Application Insights Profiler
Můžete povolit, aby Application Insights Profiler spouštěla zachytávání podrobných trasování výkonu. Pokud potřebujete prozkoumat problémy, ke kterým došlo v minulosti, můžete získat přístup k trasováním až k pěti dnům. Tuto možnost můžete zvolit, pokud máte přístup k prostředkům Application Insights aplikace na Azure Portal.

Application Insights Profiler poskytuje statistiku doby odezvy pro každé webové volání a trasování, které označují, který řádek kódu způsobil pomalé odezvy. Někdy je aplikace App Service pomalá, protože určitý kód není zapsaný způsobem. Mezi příklady patří sekvenční kód, který lze spustit paralelně a nepožadovánými spory zámků databáze. Odebrání těchto kritických bodů v kódu zvyšuje výkon aplikace, ale je obtížné je detekovat bez nastavování zpracovaných trasování a protokolů. Trasování shromážděná pomocí Application Insights Profiler pomáhají identifikovat řádky kódu, které zpomalují aplikaci a překonat tuto výzvu pro App Service aplikace.

 Další informace najdete v tématu [profilace živých aplikací v Azure App Service s](../azure-monitor/app/profiler.md)využitím Application Insights.

##### <a name="use-remote-profiling"></a>Použít vzdálené profilování
V Azure App Service můžou být vzdáleně profilované webové aplikace, aplikace API, mobilní back-endy a WebJobs. Tuto možnost vyberte, pokud máte přístup k prostředku aplikace a víte, jak problém reprodukován, nebo pokud znáte přesný časový interval, po který dojde k problému s výkonem.

Vzdálené profilování je užitečné v případě, že je využití CPU vysoké a proces běží pomaleji, než se čekalo nebo je latence požadavků HTTP vyšší než normální, můžete vzdáleně profilovat svůj proces a získat zásobníky volání pro vzorkování procesoru pro analýzu aktivity procesu a cest k Hot Code.

Další informace najdete v tématu [Podpora vzdáleného profilování v Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ruční nastavení diagnostických trasování
Máte-li přístup ke zdrojovému kódu webové aplikace, nástroj Application Diagnostics umožňuje zachytit informace vytvářené webovou aplikací. Aplikace ASP.NET můžou použít `System.Diagnostics.Trace` třídu k protokolování informací do protokolu diagnostiky aplikace. Je však nutné změnit kód a znovu nasadit aplikaci. Tato metoda se doporučuje, pokud je vaše aplikace spuštěná v testovacím prostředí.

Podrobné pokyny týkající se konfigurace aplikace pro protokolování najdete v tématu [Povolení protokolování diagnostiky pro aplikace v Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Použití nástroje pro diagnostiku
App Service poskytuje inteligentní a interaktivní prostředí, které vám umožní řešit problémy s aplikací bez nutnosti konfigurace. Když narazíte na problémy s vaší aplikací, nástroj pro diagnostiku zjistí, co je špatné, aby vás převedl na správné informace pro snadnější a rychlé řešení potíží a vyřešení problému.

Pokud chcete získat přístup k diagnostice App Service, přejděte v [Azure Portal](https://portal.azure.com)do aplikace App Service nebo App Service Environment. V levém navigačním panelu klikněte na **diagnostikovat a řešte problémy**.

#### <a name="use-the-kudu-debug-console"></a>Použití konzoly ladění Kudu
App Service se dodává s konzolou ladění, kterou můžete použít pro ladění, prozkoumávání, nahrávání souborů a také pro koncové body JSON pro získání informací o vašem prostředí. Tato konzola se nazývá *Konzola Kudu* nebo *řídicí panel SCM* pro vaši aplikaci.

K tomuto řídicímu panelu se dostanete tak, že na odkaz **https:// &lt; název vaší aplikace>. SCM.azurewebsites.NET/**.

K dispozici jsou některé z věcí, které Kudu poskytuje:

* nastavení prostředí pro vaši aplikaci
* Stream protokolu
* výpis stavu diagnostiky
* ladit konzolu, ve které můžete spouštět rutiny prostředí PowerShell a základní příkazy systému DOS.

Další užitečnou funkcí Kudu je to, že v případě, že aplikace vyvolává první výjimky, můžete k vytváření výpisů paměti použít Kudu a ProcDump nástrojů Sysinternals. Tyto výpisy paměti jsou snímky procesu a často vám můžou pomoct řešit složitější problémy s vaší aplikací.

Další informace o funkcích dostupných v Kudu najdete v tématu [nástroje Azure DevOps, které byste měli znát](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. zmírnění problému
#### <a name="scale-the-app"></a>Škálování aplikace
V Azure App Service můžete pro zvýšení výkonu a propustnosti upravit měřítko, na kterém aplikaci spouštíte. Horizontální navýšení kapacity aplikace zahrnuje dvě související akce: Změna plánu App Service na vyšší cenovou úroveň a konfiguraci určitého nastavení po přepnutí na vyšší cenovou úroveň.

Další informace o škálování najdete v tématu horizontální navýšení [kapacity aplikace v Azure App Service](manage-scale-up.md).

Kromě toho můžete zvolit spuštění aplikace na více než jedné instanci. Horizontální navýšení kapacity vám poskytuje více funkcí zpracování, ale také poskytuje určitou úroveň odolnosti proti chybám. Pokud proces přejde na jednu instanci, ostatní instance budou dál obsluhovat požadavky.

Můžete nastavit škálování na ruční nebo automatické.

#### <a name="use-autoheal"></a>Použít reretušovací
Automatické zacele recykluje pracovní proces vaší aplikace na základě nastavení, které zvolíte (například změny konfigurace, požadavky, limity založené na paměti nebo doba potřebná ke spuštění žádosti). Ve většině případů je recyklací procesu nejrychlejší způsob, jak se zotavit z problému. I když aplikaci můžete kdykoli znovu spustit přímo v rámci Azure Portal, automatické automatický opravování ji provede automaticky. Stačí přidat některé triggery do kořenového web.config vaší aplikace. Tato nastavení budou fungovat stejným způsobem i v případě, že vaše aplikace není aplikací .NET.

Další informace najdete v tématu věnovaném [automatickému retušování webů Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Restartujte aplikaci.
Restartování je často nejjednodušší způsob, jak provést zotavení při jednorázových potížích. V okně [Azure Portal](https://portal.azure.com/)máte v okně aplikace možnosti, jak aplikaci zastavit nebo restartovat.

 ![restartování aplikace za účelem řešení problémů s výkonem](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Svou aplikaci můžete spravovat i pomocí Azure PowerShellu. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md).