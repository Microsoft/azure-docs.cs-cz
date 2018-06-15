---
title: Opravte 502 Chybná brána, 503 Služba nedostupná chyby | Microsoft Docs
description: Vyřešte potíže Chybná brána 502 a 503 Služba není k dispozici chyby ve vaší webové aplikace hostované v Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 Chybná brána 503 Služba nedostupná, chyby 503, chyby 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836515"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Řešení chyb HTTP "502 Chybná brána" a "503 Služba není k dispozici" ve službě Azure web apps
"502 Chybná brána" a "503 Služba nedostupná" jsou běžné chyby ve vaší webové aplikace hostované v [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Tento článek vám pomůže vyřešit tyto chyby.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na Azure odborníky na [MSDN Azure a fóra Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte na [podporu Azure lokality](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="symptom"></a>Příznaky
Když přejdete do webové aplikace, vrátí HTTP "502 Chybná brána" Chyba nebo HTTP Chyba "503 Služba není k dispozici".

## <a name="cause"></a>Příčina
Tento problém je často způsoben problémy na úrovni aplikací, například:

* požadavky trvá příliš dlouho
* aplikace pomocí vysoké paměti nebo procesoru
* aplikace chybám kvůli výjimce.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Řešení potíží s kroky pro řešení "502 Chybná brána" a "503 Služba nedostupná" chyb
Řešení potíží s jde rozdělit na tři samostatné úkoly v sekvenčním pořadí:

1. [Sledovat a monitorovat chování aplikace](#observe)
2. [Shromažďování dat](#collect)
3. [Zmírnění problém](#mitigate)

[App Service Web Apps](/services/app-service/web/) nabízí různé možnosti v každém kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sledovat a monitorovat chování aplikace
#### <a name="track-service-health"></a>Sledování stavu služby
Microsoft Azure publicizes pokaždé, když je služba došlo k přerušení nebo výkonu snížení. Stav služby můžete sledovat na [portálu Azure](https://portal.azure.com/). Další informace najdete v tématu [sledovat stav služeb](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorování webové aplikace
Tato možnost vám umožňuje zjistit, pokud se žádné problémy s vaší aplikace. V okně vaší webové aplikace, klikněte **požadavky a chyby** dlaždici. **Metrika** okno se zobrazí všechny metriky můžete přidat.

Některé z metriky, které můžete chtít monitorování pro webovou aplikaci

* Průměrná paměti pracovní sady
* Průměrná doba odezvy
* Čas procesoru
* Paměť pracovní sady
* Požadavky

![monitorování webové aplikace k řešení chyb HTTP 502 Chybná brána a 503 Služba nedostupná](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Další informace naleznete v tématu:

* [Monitorování webové aplikace v Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Shromažďování dat
#### <a name="use-the-azure-app-service-support-portal"></a>Použití portálu podporu služby Azure App Service
Web Apps poskytuje možnost řešení problémů s prohlížením HTTP protokoly, protokoly událostí, výpisy procesů a další související s vaší webové aplikace. Dostanete tyto informace pomocí náš portál podpory v **http://&lt;název aplikace >.scm.azurewebsites.net/Support**

Portálu pro podporu Azure App Service nabízí tři samostatné karty pro podporu tři kroky běžné scénáře řešení potíží:

1. Sledovat aktuální chování
2. Analýza shromažďování diagnostické informace a spuštěním předdefinované analyzátory
3. Zmírnění

Pokud tento problém se děje nyní, klikněte na tlačítko **analyzovat** > **diagnostiky** > **diagnostikovat teď** k vytvoření relace diagnostiky, které bude shromažďovat protokoly HTTP, protokoly Prohlížeče událostí, paměti, že výpisy, protokoly chyb PHP a PHP, zpracování sestavy.

Jakmile data jsou shromažďována, bude také spustit analýzu na datech a poskytnout sestavu ve formátu HTML.

V případě, že chcete stáhnout data, ve výchozím nastavení, by je uložená ve složce D:\home\data\DaaS.

Další informace o portálu podpora služby Azure App Service naleznete v tématu [nové aktualizace ke rozšíření lokality podporu pro weby sady Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Použít konzolu pro ladění modulu Kudu
Webové aplikace se dodává s konzolou pro ladění, který můžete použít pro ladění, prohlížení, nahrávání souborů a také koncové body JSON pro získání informací o vašem prostředí. To se označuje jako *Kudu konzoly* nebo *řídicí panel SCM* pro vaši webovou aplikaci.

Dostanete tento řídicí panel tak, že přejdete na odkaz **https://&lt;název aplikace >.scm.azurewebsites.net/**.

Některé možnosti, které Kudu poskytuje jsou:

* nastavení prostředí pro vaši aplikaci
* datový proud protokolu
* diagnostické výpis
* ladění konzoly, ve kterém můžete spouštět rutiny prostředí Powershell a základních příkazů DOS.

Další užitečné funkce Kudu je, že v případě, že aplikace je vyvolání first chance výjimek, můžete použít Kudu a vypíše nástroj SysInternals Procdump vytvořit paměti. Tyto výpisy paměti jsou snímky procesu a často může pomoci při odstraňování složitějších problémů s vaší webové aplikace.

Další informace o funkcích, které jsou k dispozici v Kudu, najdete v části [online nástroje weby Azure, měli byste vědět o](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Zmírnění problém
#### <a name="scale-the-web-app"></a>Škálování webové aplikace
Ve službě Azure App Service pro vyšší výkon a propustnost, můžete upravit škálování, ve kterém je spuštěná vaše aplikace. Škálování webovou aplikaci zahrnuje dvě souvisejících akcích: Změna plánu služby App Service na používat vyšší cenová úroveň a konfigurace určitá nastavení po jste přepnuli do vyšší cenová úroveň.

Další informace o škálování najdete v tématu [škálování webové aplikace v Azure App Service](web-sites-scale.md).

Kromě toho můžete spustit aplikaci na více než jednu instanci. Pouze to poskytuje další možnost zpracování, ale také vám dává některé množství odolnost proti chybám. Pokud proces přestane fungovat na jednu instanci, ostatní instance pokračovat, obsluhovat požadavky.

Můžete nastavit škálování ručně nebo automaticky.

#### <a name="use-autoheal"></a>Pomocí funkce AutoHeal
Funkce AutoHeal recykluje pracovní proces pro vaši aplikaci na základě nastavení, které zvolíte (například změny konfigurace, požadavky, omezení na základě paměti nebo doba potřebná k provedení požadavku). Ve většině případů, recyklaci proces je nejrychlejší způsob, jak obnovit z problém. I když můžete vždy restartování webové aplikace z přímo na portálu Azure, funkce AutoHeal bude provádět automaticky za vás. Všechny, které musíte udělat, je přidat některé aktivační události v kořenovém souboru web.config pro vaši webovou aplikaci. Mějte na paměti, že by tato nastavení fungují v stejným způsobem, i v případě, že vaše aplikace není .net jeden.

Další informace najdete v tématu [automatické opravy weby Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Restartování webové aplikace
To je často nejjednodušší způsob, jak obnovit z jednorázových problémů. Na [portálu Azure](https://portal.azure.com/), v okně vaší webové aplikace, zobrazí se možnosti pro zastavení nebo restartování aplikace.

 ![Restartujte aplikace k řešení chyb HTTP 502 Chybná brána a 503 Služba nedostupná](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Můžete také spravovat webové aplikace pomocí Azure Powershell. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

