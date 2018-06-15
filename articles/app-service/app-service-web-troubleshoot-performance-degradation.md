---
title: Pomalý výkon webové aplikace ve službě App Service | Microsoft Docs
description: Tento článek vám pomůže vyřešit problémy s výkonem pomalé webových aplikací ve službě Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: webové aplikace výkonu, pomalé aplikace, aplikace pomalé
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 6b71aa004095a94bea84623fd2b5dbdfc1f81af0
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29461838"
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Řešení potíží s výkonem pomalé webové aplikace v Azure App Service
Tento článek vám pomůže vyřešit problémy s výkonem pomalé webové aplikace v [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [MSDN Azure a fóra Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="symptom"></a>Příznaky
Když přejdete webové aplikace, zatížení stránky pomalu a někdy časový limit.

## <a name="cause"></a>Příčina
Tento problém je často způsoben problémy na úrovni aplikací, například:

* požadavky sítě trvá příliš dlouho
* kód nebo databáze dotazy aplikace probíhá neefektivní
* aplikace pomocí vysoké paměti nebo procesoru
* aplikace chybám kvůli výjimce

## <a name="troubleshooting-steps"></a>Řešení potíží
Řešení potíží s jde rozdělit na tři samostatné úkoly v sekvenčním pořadí:

1. [Sledovat a monitorovat chování aplikace](#observe)
2. [Shromažďování dat](#collect)
3. [Zmírnění problém](#mitigate)

[App Service Web Apps](/services/app-service/web/) nabízí různé možnosti v každém kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sledovat a monitorovat chování aplikace
#### <a name="track-service-health"></a>Sledování stavu služby
Microsoft Azure publicizes pokaždé, když je služba došlo k přerušení nebo výkonu snížení. Stav služby můžete sledovat na [portál Azure](https://portal.azure.com/). Další informace najdete v tématu [sledovat stav služeb](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorování webové aplikace
Tato možnost vám umožňuje zjistit, pokud se žádné problémy s vaší aplikace. V okně vaší webové aplikace, klikněte **požadavky a chyby** dlaždici. **Metrika** okno ukazuje všechny metriky můžete přidat.

Některé z metriky, které můžete chtít monitorování pro webovou aplikaci

* Průměrná pracovní sada paměti
* Průměrná doba odezvy
* Čas procesoru
* Pracovní sada paměti
* Požadavky

![sledování výkonu webové aplikace](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Další informace naleznete v tématu:

* [Monitorování webové aplikace v Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Sledování stavu webových koncový bod
Pokud používáte vaší webové aplikace **standardní** cenovou úroveň, webových aplikací umožňuje monitorovat dva koncové body ze tří zeměpisné umístění.

Monitorování koncového bodu nakonfiguruje webové testy z geograficky distribuovaná umístění, které doba odezvy a provozu adres URL webových testů. Test provádí operaci HTTP GET na adresu URL webového k určení doby odezvy a doby provozu z každé umístění. Každé nakonfigurovaná umístění spouští test každých pět minut.

Doba provozu je monitorován pomocí kódů odpovědi HTTP a doba odezvy se měří v milisekundách. Monitorování test se nezdaří, pokud kód odpovědi HTTP je větší než nebo rovno 400 nebo pokud odpověď trvá déle než 30 sekund. Koncový bod je považován za dostupný, pokud jeho monitorovací testy úspěšné z určitých umístění.

Pokud chcete ji nastavit, najdete v části [monitorování aplikací v Azure App Service](web-sites-monitor.md).

Další informace naleznete v [zachování weby Azure až plus monitorování koncového bodu - s Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) video o monitorování koncového bodu.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorování výkonu aplikací pomocí rozšíření
Můžete taky sledovat výkon aplikací pomocí *lokality rozšíření*.

Každý webové aplikace App Service poskytuje rozšiřitelný správy koncový bod, umožňuje používat jako rozšíření lokality výkonnou sadu nástrojů pro nasazení. Rozšíření zahrnují: 

- Jako zdrojový kód editory [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Nástroje pro správu pro připojené prostředkům, například databáze MySQL připojen do webové aplikace.

[Azure Application Insights](/services/application-insights/) je rozšíření webu, který je k dispozici také monitorování výkonu. Pokud chcete použít Application Insights, znovu sestavit kódu pomocí sady SDK. Můžete také nainstalovat rozšíření, které poskytuje přístup k dalším datům. Sada SDK umožňuje napsat kód pro sledování využití a výkonu vaší aplikace podrobněji. Další informace najdete v tématu [sledování výkonu ve webových aplikacích](../application-insights/app-insights-web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Shromažďování dat
Prostředí webové aplikace poskytuje diagnostické funkce pro protokolování informací z webového serveru a webové aplikace. Informace je rozdělené na webový server diagnostics a application diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Povolte diagnostiku webového serveru
Můžete povolit nebo zakázat následující typy protokolů:

* **Podrobné protokolování chyb** -podrobné informace o chybě pro stavové kódy HTTP, které indikují chybu (kód stavu 400 nebo vyšší). To může obsahovat informace, které vám mohou pomoci určit, proč server vrátil kód chyby.
* **Se nezdařilo, trasování požadavku** -podrobné informace o chybných žádostech, včetně trasování pro součásti služby IIS používá ke zpracování žádostí a doba trvání v jednotlivých součástí. To může být užitečné, pokud se pokoušíte zlepšení výkonu webové aplikace nebo izolovat, co ho způsobuje. konkrétní chyba protokolu HTTP.
* **Webový Server protokolování** -informace o použití rozšířeném formátu protokolu W3C souboru transakce HTTP. To je užitečné, když chcete určit celkový metriky webové aplikace, jako je počet požadavků zpracovaných nebo je počet požadavků z konkrétní IP adresu.

#### <a name="enable-application-diagnostics"></a>Povolit rozhraní application diagnostics
Existuje několik možností shromažďovat údaje o výkonu aplikací z webové aplikace, profil aplikace za provozu ze sady Visual Studio nebo upravit kód aplikace do protokolu Další informace a trasování. Můžete zvolit možnosti založené na tom, kolik přístupu k aplikaci a zjištěnými z monitorování nástroje.

##### <a name="use-application-insights-profiler"></a>Pomocí nástroje Application Insights Profiler
Můžete povolit profileru Statistika aplikace zahájíte zaznamenávání podrobné výkonu trasování. Můžete získat přístup k trasování zachytit až před pět dní když potřebujete k prozkoumání problémů došlo v minulosti. Tuto možnost můžete tak dlouho, dokud máte přístup k prostředku Application Insights webovou aplikaci na portálu Azure.

Application Insights profileru obsahuje statistiky doba odezvy pro každé volání webové a trasování, která určuje, které řádek kódu způsobila pomalé odezvy. Aplikace služby App Service je někdy pomalé, protože není v původce určitý kód způsobem. Mezi příklady patří sekvenční kód, který se může spouštět ve kolizí zámků paralelní a nežádoucí databáze. Odebrání těchto kritická místa v kódu zvyšuje výkon aplikace, ale je obtížné zjistit bez nastavování vypracovala trasování a protokoly. Trasování získané nástrojem Application Insights profileru pomáhá identifikace řádky kódu, který zpomaluje aplikace a vyřešit tento problém pro aplikace služby App Service.

 Další informace najdete v tématu [profilování za provozu Azure web apps s Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Použití vzdálené profilování
Ve službě Azure App Service Web Apps, aplikace API a webové úlohy může být vzdáleně profilovaným. Tuto možnost zvolte, pokud máte přístup k webovým prostředkům aplikace a budete vědět, jak reprodukujte problém nebo pokud znáte přesnou časový interval se stane problémy s výkonem.

Vzdálené profilování je užitečné, pokud je vysoké využití procesoru procesu a váš proces běží něco pomalejší, než se očekávalo, nebo latence požadavky HTTP jsou vyšší než normální, můžete vzdáleně profilu váš proces a získat zásobníky volání vzorkování procesoru k analýze proces aktivity a kód aktivní cesty.

Další informace najdete v tématu [vzdálené profilování podpory v Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Ručně nastavit diagnostické trasování
Pokud máte přístup ke zdrojovému kódu webové aplikace, rozhraní Application diagnostics umožňuje zaznamenat informace o vytvořil webovou aplikací. Aplikace ASP.NET můžete použít `System.Diagnostics.Trace` třída do protokolu informace o protokolu diagnostiky aplikace. Však musíte změnit kód a znovu nasaďte aplikaci. Tato metoda se doporučuje, když aplikace běží v testovacím prostředí.

Podrobné pokyny o tom, jak nakonfigurovat svoji aplikaci pro protokolování najdete v tématu [povolit protokolování diagnostiky pro webové aplikace v Azure App Service](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Použití portálu Azure App Service podpory
Web Apps poskytuje možnost řešení problémů s prohlížením HTTP protokoly, protokoly událostí, výpisy procesů a další související s vaší webové aplikace. Dostanete tyto informace pomocí náš portál podpory v **http://&lt;název aplikace >.scm.azurewebsites.net/Support**

Podpora portálu služby Azure App Service nabízí tři samostatné karty pro podporu tři kroky běžné scénáře řešení potíží:

1. Sledovat aktuální chování
2. Analýza shromažďování diagnostické informace a spuštěním předdefinované analyzátory
3. Oprava

Pokud tento problém se děje nyní, klikněte na tlačítko **analyzovat** > **diagnostiky** > **diagnostikovat teď** k vytvoření relace diagnostiky pro vás, který shromažďuje HTTP protokoly, protokoly Prohlížeče událostí paměti výpisy, protokoly chyb PHP a PHP, zpracování sestavy.

Jakmile data jsou shromažďována, portálu podporu spouští analýzu dat a vám poskytne sestavu ve formátu HTML.

V případě, že chcete stáhnout data, ve výchozím nastavení, by je uložená ve složce D:\home\data\DaaS.

Další informace na podporu portálu služby Azure App Service najdete v tématu [nové aktualizace ke rozšíření lokality podporu pro weby sady Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Použít konzolu pro ladění modulu Kudu
Webové aplikace se dodává s konzolou pro ladění, který můžete použít pro ladění, prohlížení, nahrávání souborů a také koncové body JSON pro získání informací o vašem prostředí. Tato konzola se nazývá *Kudu konzoly* nebo *řídicí panel SCM* pro vaši webovou aplikaci.

Dostanete tento řídicí panel tak, že přejdete na odkaz **https://&lt;název aplikace >.scm.azurewebsites.net/**.

Některé možnosti, které Kudu poskytuje jsou:

* nastavení prostředí pro vaši aplikaci
* datový proud protokolu
* diagnostické výpis
* ladění konzoly, ve kterém můžete spouštět rutiny prostředí Powershell a základních příkazů DOS.

Další užitečné funkce Kudu je, že v případě, že aplikace je vyvolání first chance výjimek, můžete použít Kudu a vypíše nástroj SysInternals Procdump vytvořit paměti. Tyto výpisy paměti jsou snímky procesu a často může pomoci při odstraňování složitějších problémů s vaší webové aplikace.

Další informace o funkcích, které jsou k dispozici v Kudu, najdete v části [nástroje Azure weby Team Services byste měli vědět o](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Zmírnění problém
#### <a name="scale-the-web-app"></a>Škálování webové aplikace
Ve službě Azure App Service pro vyšší výkon a propustnost, můžete upravit škálování, ve kterém je spuštěná vaše aplikace. Škálování webovou aplikaci zahrnuje dvě souvisejících akcích: Změna plánu služby App Service na používat vyšší cenová úroveň a konfigurace určitá nastavení po jste přepnuli do vyšší cenová úroveň.

Další informace o škálování najdete v tématu [škálování webové aplikace v Azure App Service](web-sites-scale.md).

Kromě toho můžete spustit aplikaci na více než jednu instanci. Horizontální navýšení kapacity pouze vám poskytne další schopnosti zpracování, ale také vám dává některé množství odolnost proti chybám. Pokud proces přestane fungovat na jednu instanci, ostatní instance dál obsluhovat požadavky.

Můžete nastavit škálování ručně nebo automaticky.

#### <a name="use-autoheal"></a>Pomocí funkce AutoHeal
Funkce AutoHeal recykluje pracovní proces pro vaši aplikaci na základě nastavení, které zvolíte (například změny konfigurace, požadavky, omezení na základě paměti nebo doba potřebná k provedení požadavku). Ve většině případů, recyklaci proces je nejrychlejší způsob, jak obnovit z problém. I když můžete vždy restartování webové aplikace z přímo v portálu Azure, funkce AutoHeal provede se automaticky. Všechny, které musíte udělat, je přidat některé aktivační události v kořenovém souboru web.config pro vaši webovou aplikaci. Tato nastavení by fungovat stejným způsobem, i když vaše aplikace není aplikace .net.

Další informace najdete v tématu [automatické opravy weby Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Restartování webové aplikace
Restartování je často nejjednodušší způsob, jak obnovit z jednorázových problémů. Na [portál Azure](https://portal.azure.com/), v okně vaší webové aplikace, zobrazí se možnosti pro zastavení nebo restartování aplikace.

 ![restartování webové aplikace k řešení problémů s výkonem](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Můžete také spravovat webové aplikace pomocí Azure Powershell. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).
