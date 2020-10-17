---
title: Oprava chyb HTTP 502 a HTTP 503
description: Řešení 502 chybných chyb služby Gateway a 503 nedostupných ve vaší aplikaci hostované v Azure App Service.
tags: top-support-issue
keywords: 502 Chybná brána, služba 503 není dostupná, chyba 503, Chyba 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 6dd67ff4f7346daf7a590ab4fb45a32d227f1c3e
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147549"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Řešení chyb HTTP z "502 špatné brány" a "503 Služba není dostupná" v Azure App Service
"502 Chybná brána" a "503 Služba není k dispozici" jsou běžné chyby v aplikaci hostované v [Azure App Service](./overview.md). Tento článek vám pomůže vyřešit tyto chyby.

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure na [webu MSDN Azure a ve Stack Overflowch fórech](https://azure.microsoft.com/support/forums/). Případně můžete také použít incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="symptom"></a>Příznak
Když přejdete na aplikaci, vrátí chybu HTTP "502 chybné brány" nebo "nedostupnou službu HTTP" 503 ".

## <a name="cause"></a>Příčina
Tento problém je často způsoben problémy na úrovni aplikace, například:

* žádosti trvající dlouhou dobu
* aplikace s využitím vysoké paměti nebo procesoru
* došlo k chybě aplikace kvůli výjimce.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Postup řešení potíží s řešením "502 špatné brány" a "503" nedostupné služby "
Řešení potíží se dá rozdělit na tři různé úkoly v sekvenčním pořadí:

1. [Sledování a monitorování chování aplikace](#observe)
2. [Shromažďování dat](#collect)
3. [Zmírnit problém](#mitigate)

[App Service](overview.md) poskytuje v každém kroku různé možnosti.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sledujte a sledujte chování aplikace
#### <a name="track-service-health"></a>Sledovat stav služby
Microsoft Azure publicizes pokaždé, když dojde k přerušení služby nebo snížení výkonu. Stav služby můžete sledovat na webu [Azure Portal](https://portal.azure.com/). Další informace najdete v tématu [sledování stavu služby](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Monitorování aplikace
Tato možnost umožňuje zjistit, jestli má aplikace nějaké problémy. V okně vaší aplikace klikněte na dlaždici **požadavky a chyby** . V okně **metriky** se zobrazí všechny metriky, které můžete přidat.

Některé metriky, které můžete chtít monitorovat pro vaši aplikaci, jsou

* Průměrná pracovní sada paměti
* Průměrná doba odezvy
* Čas procesoru
* Pracovní sada paměti
* Žádosti

![monitorování aplikace pro řešení chyb HTTP 502 chybných bran a 503 Služba není k dispozici](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Další informace naleznete v tématech:

* [Monitorování aplikací v Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../azure-monitor/platform/alerts-overview.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2. shromažďování dat
#### <a name="use-the-diagnostics-tool"></a>Použití nástroje pro diagnostiku
App Service poskytuje inteligentní a interaktivní prostředí, které vám umožní řešit problémy s aplikací bez nutnosti konfigurace. Když narazíte na problémy s vaší aplikací, nástroj pro diagnostiku zjistí, co je špatné, aby vás převedl na správné informace pro snadnější a rychlé řešení potíží a vyřešení problému.

Pokud chcete získat přístup k diagnostice App Service, přejděte v [Azure Portal](https://portal.azure.com)do aplikace App Service nebo App Service Environment. V levém navigačním panelu klikněte na **diagnostikovat a řešte problémy**.

#### <a name="use-the-kudu-debug-console"></a>Použití konzoly ladění Kudu
App Service se dodává s konzolou ladění, kterou můžete použít pro ladění, prozkoumávání, nahrávání souborů a také pro koncové body JSON pro získání informací o vašem prostředí. To se označuje jako *Konzola Kudu* nebo *řídicí panel SCM* pro vaši aplikaci.

K tomuto řídicímu panelu se dostanete tak, že na odkaz **https:// &lt; název vaší aplikace>. SCM.azurewebsites.NET/**.

K dispozici jsou některé z věcí, které Kudu poskytuje:

* nastavení prostředí pro vaši aplikaci
* Stream protokolu
* výpis stavu diagnostiky
* ladit konzolu, ve které můžete spouštět rutiny prostředí PowerShell a základní příkazy systému DOS.

Další užitečnou funkcí Kudu je to, že v případě, že aplikace vyvolává první výjimky, můžete k vytváření výpisů paměti použít Kudu a ProcDump nástrojů Sysinternals. Tyto výpisy paměti jsou snímky procesu a často vám můžou pomoct řešit složitější problémy s vaší aplikací.

Další informace o funkcích dostupných v Kudu najdete v tématu [online nástroje Azure websites, které byste měli znát](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. zmírnění problému
#### <a name="scale-the-app"></a>Škálování aplikace
V Azure App Service můžete pro zvýšení výkonu a propustnosti upravit měřítko, na kterém aplikaci spouštíte. Horizontální navýšení kapacity aplikace zahrnuje dvě související akce: Změna plánu App Service na vyšší cenovou úroveň a konfiguraci určitého nastavení po přepnutí na vyšší cenovou úroveň.

Další informace o škálování najdete v tématu horizontální navýšení [kapacity aplikace v Azure App Service](manage-scale-up.md).

Kromě toho můžete zvolit spuštění aplikace na více než jedné instanci. Tato možnost nejen vám poskytne více funkcí zpracování, ale také poskytuje určitou úroveň odolnosti proti chybám. Pokud proces skončí s jednou instancí, ostatní instance nadále nadále obsluhuje požadavky.

Můžete nastavit škálování na ruční nebo automatické.

#### <a name="use-autoheal"></a>Použít reretušovací
Automatické zacele recykluje pracovní proces vaší aplikace na základě nastavení, které zvolíte (například změny konfigurace, požadavky, limity založené na paměti nebo doba potřebná ke spuštění žádosti). Ve většině případů je recyklací procesu nejrychlejší způsob, jak se zotavit z problému. I když aplikaci můžete kdykoli znovu spustit přímo na portálu Azure Portal, automatické zaretušování ji provede automaticky. Stačí přidat některé triggery do kořenového web.config vaší aplikace. Všimněte si, že tato nastavení budou fungovat stejným způsobem i v případě, že vaše aplikace není rozhraní .NET One.

Další informace najdete v tématu věnovaném [automatickému retušování webů Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Restartujte aplikaci.
To je často nejjednodušší způsob, jak provést zotavení při jednorázových potížích. Na [portálu Azure Portal](https://portal.azure.com/)v okně vaší aplikace máte možnost zastavit nebo restartovat aplikaci.

 ![Restartujte aplikaci, aby se vyřešily chyby HTTP 502 Chybná brána a služba 503 není dostupná.](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Svou aplikaci můžete spravovat i pomocí Azure PowerShellu. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md).