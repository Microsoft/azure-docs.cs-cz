---
title: Poradce při potížích se snížením výkonu
description: Zjistěte, jak řešit problémy s pomalým výkonem aplikací ve službě Azure App Service, včetně monitorování chování aplikací, shromažďování dat a zmírnění problému.
tags: top-support-issue
keywords: výkon webové aplikace, pomalá aplikace, aplikace pomalá
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688302"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Řešení problémů s pomalým výkonem aplikací ve službě Azure App Service
Tento článek vám pomůže vyřešit problémy s pomalým výkonem aplikací ve [službě Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete také soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na Získat **podporu**.

## <a name="symptom"></a>Příznak
Při procházení aplikace se stránky načítají pomalu a někdy i časový majestát.

## <a name="cause"></a>Příčina
Tento problém je často způsoben problémy na úrovni aplikace, například:

* síťové požadavky, které trvá dlouhou dobu
* neefektivní kód aplikace nebo databázové dotazy
* aplikace s vysokou pamětí/CPU
* shazovat aplikaci kvůli výjimce

## <a name="troubleshooting-steps"></a>Postup při řešení potíží
Řešení problémů lze rozdělit do tří různých úkolů v sekvenčním pořadí:

1. [Sledování a sledování chování aplikací](#observe)
2. [Shromažďování dat](#collect)
3. [Zmírnění problému](#mitigate)

[Služba App Service](overview.md) nabízí různé možnosti v každém kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sledujte a sledujte chování aplikací
#### <a name="track-service-health"></a>Sledování stavu služby
Microsoft Azure zveřejňuje pokaždé, když dojde k přerušení služby nebo snížení výkonu. Můžete sledovat stav služby na [webu Azure Portal](https://portal.azure.com/). Další informace naleznete v [tématu Sledování stavu služby](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Sledování aplikace
Tato možnost umožňuje zjistit, zda vaše aplikace má nějaké problémy. V okně aplikace klikněte na dlaždici **Požadavky a chyby.** Okno **Metrika** zobrazuje všechny metriky, které můžete přidat.

Některé metriky, které budete chtít pro svou aplikaci sledovat, jsou

* Průměrná pracovní sada paměti
* Průměrná doba odezvy
* Čas procesoru
* Pracovní sada paměti
* Žádosti

![sledování výkonu aplikace](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Další informace naleznete v tématu:

* [Monitorování aplikací ve službě Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Sledování stavu koncového serveru webu
Pokud aplikaci spouštěte v cenové úrovni **Standard,** služba App Service vám umožní sledovat dva koncové body ze tří geografických umístění.

Monitorování koncových bodů konfiguruje webové testy z geograficky distribuovaných umístění, která testují dobu odezvy a dobu provozu webových adres URL. Test provádí operaci HTTP GET na webové adrese URL k určení doby odezvy a doby provozu z každého umístění. Každé nakonfigurované umístění spustí test každých pět minut.

Doba odezvy je sledována pomocí kódů odezvy HTTP a doba odezvy se měří v milisekundách. Monitorovací test se nezdaří, pokud je kód odpovědi HTTP větší nebo roven 400 nebo pokud odpověď trvá déle než 30 sekund. Koncový bod je považován za dostupný, pokud jeho monitorování testy úspěšné ze všech zadaných umístění.

Pokud chcete to nastavit, přečtěte si informace [o sledování aplikací ve službě Azure App Service](web-sites-monitor.md).

Taky najdete v tématu [Udržování webů Azure a sledování koncových bodů – se Stefanem Schackowem,](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) kde najdete video o monitorování koncových bodů.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorování výkonu aplikací pomocí rozšíření
Výkon aplikace můžete také sledovat pomocí *rozšíření webu*.

Každá aplikace App Service poskytuje rozšiřitelný koncový bod pro správu, který umožňuje používat výkonnou sadu nástrojů nasazených jako rozšíření webu. Rozšíření zahrnují: 

- Editory zdrojového kódu, jako [je Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Nástroje pro správu připojených prostředků, jako je databáze MySQL připojená k aplikaci.

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) je rozšíření webu monitorování výkonu, které je také k dispozici. Chcete-li použít Application Insights, znovu sestavte kód pomocí sady SDK. Můžete také nainstalovat rozšíření, které poskytuje přístup k dalším datům. Sada SDK umožňuje psát kód pro podrobnější sledování využití a výkonu aplikace. Další informace naleznete [v tématu Sledování výkonu ve webových aplikacích](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Shromažďování údajů
Služba App Service poskytuje diagnostické funkce pro protokolování informací z webového serveru i webové aplikace. Informace jsou rozděleny do diagnostiky webového serveru a diagnostiky aplikací.

#### <a name="enable-web-server-diagnostics"></a>Povolení diagnostiky webového serveru
Můžete povolit nebo zakázat následující druhy protokolů:

* **Podrobné protokolování chyb** – podrobné informace o chybě pro stavové kódy HTTP, které označují selhání (stavový kód 400 nebo vyšší). To může obsahovat informace, které mohou pomoci určit, proč server vrátil kód chyby.
* **Trasování neúspěšných požadavků** – podrobné informace o neúspěšných požadavcích, včetně trasování součástí služby IIS použitých ke zpracování požadavku a času v jednotlivých součástech. To může být užitečné, pokud se pokoušíte zlepšit výkon aplikace nebo izolovat, co je příčinou konkrétní chyby PROTOKOLU HTTP.
* **Protokolování webového serveru** – informace o transakcích PROTOKOLU HTTP pomocí rozšířeného formátu souboru protokolu W3C. To je užitečné při určování celkové metriky aplikace, jako je například počet zpracovaných požadavků nebo kolik požadavků jsou z konkrétní IP adresy.

#### <a name="enable-application-diagnostics"></a>Povolení diagnostiky aplikací
Existuje několik možností, jak shromažďovat data o výkonu aplikace ze služby App Service, profilovat aplikaci živě z sady Visual Studio nebo upravit kód aplikace pro protokolování dalších informací a trasování. Můžete si vybrat možnosti na základě toho, jak velký přístup máte k aplikaci a co jste pozorovali z monitorovacích nástrojů.

##### <a name="use-application-insights-profiler"></a>Použití profileru Přehledů aplikací
Profileru přehledů aplikací můžete povolit tak, aby začal zachycovat podrobné sledování výkonu. Můžete přistupovat k trasám zachycených až před pěti dny, když potřebujete prozkoumat problémy, ke nini došlo v minulosti. Tuto možnost můžete zvolit, pokud máte přístup k prostředku Application Insights aplikace na webu Azure Portal.

Profiler application insights poskytuje statistiky o době odezvy pro každé webové volání a trasování, které označuje, který řádek kódu způsobil pomalé odpovědi. Někdy je aplikace App Service pomalá, protože určitý kód není napsán výkonným způsobem. Příklady zahrnují sekvenční kód, který lze spustit v paralelní a nežádoucí tvrzení uzamčení databáze. Odebrání těchto kritických míst v kódu zvyšuje výkon aplikace, ale je obtížné je zjistit bez nastavení komplikovaných trasování a protokolů. Trasování shromážděné profilerem Application Insights pomáhají identifikovat řádky kódu, které zpomalují aplikaci a překonávají tuto výzvu pro aplikace služby App Service.

 Další informace najdete [v tématu Profilování živých aplikací ve službě Azure App Service s Application Insights](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Použití vzdáleného profilování
Ve službě Azure App Service lze vzdáleně profilovat webové aplikace, aplikace api, mobilní back-endy a webjobs. Tuto možnost zvolte, pokud máte přístup k prostředku aplikace a víte, jak problém reprodukovat, nebo pokud znáte přesný časový interval, kdy k problému s výkonem dochází.

Vzdálené profilování je užitečné, pokud je využití procesoru procesu vysoké a proces běží pomaleji, než se očekávalo, nebo latence požadavků HTTP jsou vyšší než normální, můžete vzdáleně profilovat proces a získat zásobníky volání vzorkování procesoru k analýze procesu aktivity a kódovat horké cesty.

Další informace najdete [v tématu Podpora vzdáleného profilování ve službě Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ruční nastavení diagnostických trasování
Pokud máte přístup ke zdrojovému kódu webové aplikace, diagnostika aplikací umožňuje zachytit informace vytvořené webovou aplikací. ASP.NET aplikace mohou `System.Diagnostics.Trace` třídu používat k protokolování informací do protokolu diagnostiky aplikací. Je však nutné změnit kód a znovu nasadit aplikaci. Tato metoda se doporučuje, pokud vaše aplikace běží v testovacím prostředí.

Podrobné pokyny ke konfiguraci aplikace pro protokolování najdete [v tématu Povolení protokolování diagnostiky pro aplikace ve službě Azure App Service](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>Použití diagnostického nástroje
Služba App Service poskytuje inteligentní a interaktivní prostředí, které vám pomůže vyřešit potíže s aplikací bez nutnosti konfigurace. Když narazíte na problémy s vaší aplikací, diagnostický nástroj poukáže na to, co je špatné, aby vás vedl ke správným informacím snadněji a rychleji řešit a vyřešit problém.

Chcete-li získat přístup k diagnostice služby App Service, přejděte do aplikace App Service nebo prostředí služby App Service na [webu Azure Portal](https://portal.azure.com). V levém navigačním panelu klikněte na **Diagnostikovat a vyřešit problémy**.

#### <a name="use-the-kudu-debug-console"></a>Použití konzoly ladění Kudu
Služba App Service je dodávána s ladicí konzolou, kterou můžete použít pro ladění, zkoumání, nahrávání souborů a také koncové body JSON pro získání informací o vašem prostředí. Tato konzola se nazývá *Kudu Console* nebo *Řídicí panel SCM* pro vaši aplikaci.

K tomuto řídicímu panelu se dostanete na odkaz **https://&lt;Název aplikace>.scm.azurewebsites.net/**.

Některé z věcí, které Kudu poskytuje, jsou:

* nastavení prostředí pro vaši aplikaci
* datový proud protokolu
* diagnostický výpis
* ladicí konzola, ve které můžete spouštět rutiny Powershellu a základní příkazy DOSu.

Další užitečnou funkcí Kudu je, že v případě, že vaše aplikace je vyvolání první šance výjimky, můžete použít Kudu a Nástroj SysInternals Procdump k vytvoření výpisy stavu paměti. Tyto výpisy stavu paměti jsou snímky procesu a často vám mohou pomoci vyřešit složitější problémy s vaší aplikací.

Další informace o funkcích dostupných v Kudu najdete v [tématu nástroje Azure DevOps, o kterých byste měli vědět](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Zmírnit problém
#### <a name="scale-the-app"></a>Škálování aplikace
Ve službě Azure App Service můžete pro zvýšení výkonu a propustnost, můžete upravit měřítko, ve kterém spouštěte aplikaci. Škálování aplikace zahrnuje dvě související akce: změna plánu služby App Service na vyšší cenovou úroveň a konfigurace určitých nastavení po přepnutí na vyšší cenovou úroveň.

Další informace o škálování najdete v [tématu Škálování aplikace ve službě Azure App Service](manage-scale-up.md).

Kromě toho můžete spustit aplikaci na více než jednu instanci. Horizontální navýšení kapacity poskytuje nejen více možností zpracování, ale také poskytuje určité množství odolnosti proti chybám. Pokud proces přejde dolů na jednu instanci, ostatní instance nadále obsluhovat požadavky.

Měřítko můžete nastavit tak, aby bylo ruční nebo automatické.

#### <a name="use-autoheal"></a>Použít automatické uzdravování
AutoHeal recykluje pracovní proces pro vaši aplikaci na základě nastavení, které zvolíte (jako jsou změny konfigurace, požadavky, limity založené na paměti nebo čas potřebný k provedení požadavku). Většinu času, recyklovat proces je nejrychlejší způsob, jak se zotavit z problému. I když můžete aplikaci restartovat vždy přímo z portálu Azure, AutoHeal to udělá automaticky za vás. Vše, co musíte udělat, je přidat některé aktivační události v kořenovém web.config pro vaši aplikaci. Tato nastavení by fungovala stejným způsobem i v případě, že vaše aplikace není aplikace .NET.

Další informace naleznete [v tématu Auto-Healing Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Restartování aplikace
Restartování je často nejjednodušší způsob, jak se zotavit z jednorázových problémů. Na [portálu Azure](https://portal.azure.com/)máte v okně aplikace možnost i zastavení nebo restartování aplikace.

 ![restartování aplikace pro řešení problémů s výkonem](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Aplikaci můžete taky spravovat pomocí Azure Powershellu. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).
