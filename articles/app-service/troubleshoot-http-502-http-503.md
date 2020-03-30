---
title: Oprava chyb HTTP 502 a HTTP 503
description: Poradce při potížích 502 chybné brány a 503 služby nedostupné chyby ve vaší aplikaci hostované ve službě Azure App Service.
tags: top-support-issue
keywords: 502 chybná brána, služba 503 není k dispozici, chyba 503, chyba 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688331"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Poradce při potížích s chybami HTTP "502 chybná brána" a "503 služba není k dispozici" v Azure App Service
"502 chybná brána" a "503 služba není k dispozici" jsou běžné chyby ve vaší aplikaci hostované ve [službě Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Tento článek vám pomůže tyto chyby vyřešit.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete také soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na Získat **podporu**.

## <a name="symptom"></a>Příznak
Při procházení aplikace vrátí chybu HTTP "502 Bad Gateway" nebo chybu HTTP "503 Service Unavailable".

## <a name="cause"></a>Příčina
Tento problém je často způsoben problémy na úrovni aplikace, například:

* žádosti, které trvá dlouhou dobu
* aplikace s vysokou pamětí/CPU
* aplikace shazovat z důvodu výjimky.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Řešení potíží s postupy řešení chybné brány 502 a "služba 503 není k dispozici"
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
Tato možnost umožňuje zjistit, zda vaše aplikace má nějaké problémy. V okně aplikace klikněte na dlaždici **Požadavky a chyby.** Okno **Metrika** zobrazí všechny metriky, které můžete přidat.

Některé metriky, které budete chtít pro svou aplikaci sledovat, jsou

* Průměrná pracovní sada paměti
* Průměrná doba odezvy
* Čas procesoru
* Pracovní sada paměti
* Žádosti

![sledovat aplikaci k řešení chyb HTTP 502 chybných bran a služby 503 není k dispozici](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Další informace naleznete v tématu:

* [Monitorování aplikací ve službě Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Shromažďování údajů
#### <a name="use-the-diagnostics-tool"></a>Použití diagnostického nástroje
Služba App Service poskytuje inteligentní a interaktivní prostředí, které vám pomůže vyřešit potíže s aplikací bez nutnosti konfigurace. Když narazíte na problémy s vaší aplikací, diagnostický nástroj poukáže na to, co je špatné, aby vás vedl ke správným informacím snadněji a rychleji řešit a vyřešit problém.

Chcete-li získat přístup k diagnostice služby App Service, přejděte do aplikace App Service nebo prostředí služby App Service na [webu Azure Portal](https://portal.azure.com). V levém navigačním panelu klikněte na **Diagnostikovat a vyřešit problémy**.

#### <a name="use-the-kudu-debug-console"></a>Použití konzoly ladění Kudu
Služba App Service je dodávána s ladicí konzolou, kterou můžete použít pro ladění, zkoumání, nahrávání souborů a také koncové body JSON pro získání informací o vašem prostředí. Tento panel se nazývá *konzola Kudu* nebo *řídicí panel SCM* pro vaši aplikaci.

K tomuto řídicímu panelu se dostanete na odkaz **https://&lt;Název aplikace>.scm.azurewebsites.net/**.

Některé z věcí, které Kudu poskytuje, jsou:

* nastavení prostředí pro vaši aplikaci
* datový proud protokolu
* diagnostický výpis
* ladicí konzola, ve které můžete spouštět rutiny Powershellu a základní příkazy DOSu.

Další užitečnou funkcí Kudu je, že v případě, že vaše aplikace je vyvolání první šance výjimky, můžete použít Kudu a Nástroj SysInternals Procdump k vytvoření výpisy stavu paměti. Tyto výpisy stavu paměti jsou snímky procesu a často vám mohou pomoci vyřešit složitější problémy s vaší aplikací.

Další informace o funkcích dostupných v Kudu najdete v [tématu Online nástroje webů Azure, o kterých byste měli vědět](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Zmírnit problém
#### <a name="scale-the-app"></a>Škálování aplikace
Ve službě Azure App Service můžete pro zvýšení výkonu a propustnost, můžete upravit měřítko, ve kterém spouštěte aplikaci. Škálování aplikace zahrnuje dvě související akce: změna plánu služby App Service na vyšší cenovou úroveň a konfigurace určitých nastavení po přepnutí na vyšší cenovou úroveň.

Další informace o škálování najdete v [tématu Škálování aplikace ve službě Azure App Service](manage-scale-up.md).

Kromě toho můžete spustit aplikaci na více než jednu instanci . To nejen poskytuje více možností zpracování, ale také vám dává určité množství odolnosti proti chybám. Pokud proces přejde dolů na jednu instanci, druhá instance bude i nadále zobrazovat požadavky.

Měřítko můžete nastavit tak, aby bylo ruční nebo automatické.

#### <a name="use-autoheal"></a>Použít automatické uzdravování
AutoHeal recykluje pracovní proces pro vaši aplikaci na základě nastavení, které zvolíte (jako jsou změny konfigurace, požadavky, limity založené na paměti nebo čas potřebný k provedení požadavku). Většinu času, recyklovat proces je nejrychlejší způsob, jak se zotavit z problému. I když můžete aplikaci restartovat vždy přímo z portálu Azure Portal, AutoHeal to udělá automaticky za vás. Vše, co musíte udělat, je přidat některé aktivační události v kořenovém web.config pro vaši aplikaci. Všimněte si, že tato nastavení bude fungovat stejným způsobem i v případě, že vaše aplikace není .NET jeden.

Další informace naleznete [v tématu Auto-Healing Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Restartování aplikace
To je často nejjednodušší způsob, jak se zotavit z jednorázových problémů. Na [webu Azure Portal](https://portal.azure.com/)máte v okně aplikace možnost i zastavení nebo restartování aplikace.

 ![restartovat aplikaci k řešení chyb HTTP 502 chybná brána a 503 služba není k dispozici](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Aplikaci můžete taky spravovat pomocí Azure Powershellu. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

