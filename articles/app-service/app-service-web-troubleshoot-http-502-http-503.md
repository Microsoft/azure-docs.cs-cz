---
title: Oprava 502 – Chybná brána, 503 Služba není k dispozici chyby - službě Azure App Service | Dokumentace Microsoftu
description: Řešení potíží s 502 – Chybná brána a služba není k dispozici 503 ve webové aplikaci hostované v Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 – Chybná brána, 503 Služba není k dispozici, chybě 503, Chyba 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 71842f9dbc8d0454da1847c956dea3b063208836
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53259286"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Řešení potíží s chybami HTTP typu "502 – Chybná brána" a "503 Služba není dostupná" ve službě Azure web apps
"502 – Chybná brána" a "503 Služba není k dispozici" jsou běžné chyby ve webové aplikaci hostované v [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Tento článek pomůže při řešení těchto chyb.

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Alternativně můžete také soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a klikněte na **získat podporu**.

## <a name="symptom"></a>Příznak
Když přejdete do webové aplikace, vrátí HTTP "502 – Chybná brána" Chyba nebo HTTP "503 – nedostupná služba" Chyba.

## <a name="cause"></a>Příčina
Tento problém je často způsobeno úrovně problémů v aplikacích, jako například:

* požadavky trvá příliš dlouho
* aplikace s použitím vysoké paměti a procesoru
* aplikace k chybám z důvodu výjimky.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Řešení potíží s kroky pro řešení "502 – Chybná brána" a "503 Služba není k dispozici" chyby
Řešení potíží je možné rozdělit do tří odlišných úkolech v postupném pořadí:

1. [Sledovat a monitorovat chování aplikace](#observe)
2. [Shromažďování dat](#collect)
3. [Tyto problémy zmírnit](#mitigate)

[App Service Web Apps](app-service-web-overview.md) nabízí různé možnosti v každém kroku.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Sledovat a monitorovat chování aplikace
#### <a name="track-service-health"></a>Sledování stavu služby
Microsoft Azure publicizes pokaždé, když je služba došlo k přerušení nebo výkonu snížení. Můžete sledovat stav služby na [webu Azure Portal](https://portal.azure.com/). Další informace najdete v tématu [sledování stavu služby](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorování webové aplikace
Tato možnost umožňuje zjistit, pokud vaše aplikace má nějaké problémy. V okně webové aplikace, klikněte na tlačítko **požadavky a chyby** dlaždici. **Metrika** okno se zobrazí všechny metriky můžete přidat.

Některé metriky, které můžete chtít monitorovat webové aplikace

* Průměrná pracovní sada paměti
* Průměrná doba odezvy
* Čas procesoru
* Pracovní sada paměti
* Požadavky

![monitorování webové aplikace k řešení chyb HTTP 502 – Chybná brána a 503 – nedostupná služba](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Další informace naleznete v tématu:

* [Monitorovat můžete webové aplikace ve službě Azure App Service](web-sites-monitor.md)
* [Zobrazování oznámení o výstrahách](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Shromažďování dat
#### <a name="use-the-diagnostics-tool"></a>Pomocí diagnostického nástroje
Služba App Service poskytuje inteligentní a interaktivní prostředí při odstraňování webové aplikace bez nezbytné konfigurace. Pokud narazíte na problémy s vaší webovou aplikací, diagnostický nástroj, který bude odkazovat, co je špatně a provede vás na ty správné informace k snadno a rychle odstraňovat potíže a řešit potíže.

Pro přístup k diagnostice App Service, přejděte do App Service Environment ve vaší aplikaci služby App Service [webu Azure portal](https://portal.azure.com). V levém navigačním panelu klikněte na **diagnostikovat a řešit problémy**.

#### <a name="use-the-kudu-debug-console"></a>Použijte konzolu pro ladění Kudu
Funkce Web Apps se dodává s konzolou pro ladění, který můžete použít pro ladění, zkoumat a nahrávání souborů, stejně jako koncové body JSON pro získání informací o vašem prostředí. Tento postup se nazývá *konzola Kudu* nebo *řídicího panelu SCM* pro vaši webovou aplikaci.

Tento řídicí panel můžete přistupovat tak, že přejdete na odkaz **https://&lt;název vaší aplikace >.scm.azurewebsites.net/**.

Zde jsou některé z akcí, které poskytuje Kudu:

* nastavení prostředí pro vaši aplikaci
* stream protokolů
* diagnostické výpisu stavu systému
* ladění konzoly, ve kterém můžete spouštět rutiny prostředí Powershell a základní příkazy DOS.

Další užitečnou funkci Kudu je, že v případě, že vaše aplikace vyvolává výjimkách first-chance, můžete použít Kudu a vypíše nástroj SysInternals Procdump vytvořit paměti. Tyto výpisy paměti jsou snímky procesu a často vám můžou pomoct vyřešit problémy složitější s vaší webovou aplikací.

Další informace o funkcích dostupných v Kudu najdete v tématu [Azure Websites online nástrojů, které byste měli znát](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Tyto problémy zmírnit
#### <a name="scale-the-web-app"></a>Škálování webové aplikace
Ve službě Azure App Service pro vyšší výkon a propustnost, můžete upravit škálování, ve kterém je spuštěna vaše aplikace. Škálování webové aplikace vyžaduje dvě související akce: Změna vašeho plánu služby App Service na vyšší cenovou úroveň a určitá nastavení konfigurace, poté, co jste přešli na vyšší cenovou úroveň.

Další informace o škálování najdete v tématu [škálování webové aplikace ve službě Azure App Service](web-sites-scale.md).

Kromě toho můžete spustit aplikaci ve více než jednu instanci. Nejen to vám poskytne další možnosti zpracování, ale také poskytuje určitou míru odolnosti proti chybám. Pokud proces přestane fungovat na jednu instanci, druhá instance pokračovat, obsluze žádostí.

Můžete nastavit jako ruční nebo automatické škálování.

#### <a name="use-autoheal"></a>Používají AutoHeal
AutoHeal recykluje pracovní proces pro vaši aplikaci na základě nastavení, které zvolíte (například změny konfigurace, požadavky, omezení na základě paměti nebo doba potřebná k provedení požadavku). Ve většině případů, recyklace procesu je nejrychlejší způsob, jak zotavení po chybě. I když můžete vždy restartovat webovou aplikaci z přímo z portálu Azure Portal, AutoHeal bude to udělala automaticky za vás. Všechno, co musíte udělat, je přidání některých aktivačních událostí v kořenovém souboru web.config pro vaši webovou aplikaci. Všimněte si, že tato nastavení bude fungovat stejným způsobem, i v případě, že vaše aplikace není .net jeden.

Další informace najdete v tématu [Samoopravení weby Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Restartujte webovou aplikaci
Často je to nejjednodušší způsob, jak zotavení při jednorázových potížích. Na [webu Azure Portal](https://portal.azure.com/), v okně webové aplikace, máte možnost zastavit nebo restartovat aplikaci.

 ![Restartujte aplikaci k řešení chyb HTTP 502 – Chybná brána a 503 – nedostupná služba](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Můžete také spravovat webové aplikace pomocí Azure Powershellu. Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

