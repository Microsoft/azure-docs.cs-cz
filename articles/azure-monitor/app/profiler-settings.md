---
title: Použití pokokna nastavení Azure Application Insights Profiler | Dokumentace Microsoftu
description: Zobrazit stav Profiler a spuštění relace profilování
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 7f864c90b8279f315fc0dbe96f41bfd03be7cebc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883362"
---
# <a name="configure-application-insights-profiler"></a>Konfigurovat Application Insights Profiler

## <a name="profiler-settings-pane"></a>Podokno nastavení Profiler

Otevření podokna nastavení Azure Application Insights Profiler, přejděte do podokna výkonu Application Insights a vyberte **Profiler** tlačítko.

![Konfigurace panelu Profiler][configure-profiler-entry]

**Nakonfigurovat Application Insights Profiler** podokno obsahuje čtyři funkce: 
* **Profil nyní**: Spuštění profilování relací pro všechny aplikace, které jsou propojeny k této instanci Application Insights.
* **Propojené aplikace**: Seznam aplikací, které odesílají profilování dat k tomuto prostředku Application Insights.
* **Relace v průběhu**: Zobrazuje stav relace, když vyberete **profilu nyní**. 
* **Nedávné relace profilace**: Zobrazí informace o poslední relace profilování.

![Profiler na vyžádání][profiler-on-demand]

## <a name="app-service-environment"></a>App Service Environment
V závislosti na konfiguraci služby Azure App Service Environment může být blokovaný volání na kontrolu stavu agenta. V podokně se může zobrazit zpráva, že agent není spuštěn i v případě, že je spuštěná. Pokud chcete mít jistotu, že se jedná, zkontrolujte úlohy webjob ve své aplikaci. Pokud jsou všechny hodnoty nastavení aplikace správné a rozšíření webu Application Insights je nainstalován ve své aplikaci, je spuštěn Profiler. Pokud vaše aplikace přijímá dostatek provozu, poslední relace profilování má být zobrazena v seznamu.

## <a id="profileondemand"></a> Ručně aktivovat Profiler

Profiler můžete ručně aktivovat jediným kliknutím. Předpokládejme, že máte spuštěnou testu výkonnosti webu. Budete potřebovat trasování, které vám pomohou pochopit, jaký je výkon vaší webové aplikace v zatížení. Řídit, kdy jsou zachyceny trasování je zásadní, protože víte, kdy bude spuštěn zátěžový test. Ale interval vzorkování náhodné přijít.

Další části ukazuje, jak v tomto scénáři funguje:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Krok 1: (Volitelné) Vytvořte provozu do vaší webové aplikace tím, že spuštění testu výkonnosti webu

Pokud vaše webová aplikace už obsahuje příchozí provoz nebo pokud chcete jen vygenerovat ručně provoz, tuto část přeskočit a pokračovat v kroku 2.

1. Na portálu Application Insights, vyberte **konfigurovat** > **testování výkonu**. 

1. Chcete-li spustit nový test výkonnosti, vyberte **nový** tlačítko.

   ![Vytvořit nový test výkonnosti][create-performance-test]

1. V **nový test výkonnosti** podokně nakonfigurovat cílová adresa URL testu. Přijměte všechna výchozí nastavení a pak vyberte **spustit test** spouštění zátěžového testu.

    ![Konfigurace zátěžového testu][configure-performance-test]

    Nový test se do fronty zařadí nejprve, za nímž následuje stav *probíhá*.

    ![zátěžový test je odeslána a zařazeny do fronty][load-test-queued]

    ![v průběhu je spuštěn zátěžový test][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Krok 2: Spusťte Profiler relaci na vyžádání

1. Když je spuštěn zátěžový test, spusťte Profiler k zaznamenání trasování ve webové aplikaci, zatímco přijímá zatížení.

1. Přejděte **nakonfigurujte Profiler** podokně.


### <a name="step-3-view-traces"></a>Krok 3: Zobrazení trasování

Po doběhnutí Profiler, postupujte podle pokynů v oznámení pro přechod na podokno a zobrazení trasování výkonu.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Řešení potíží s Profiler relaci na vyžádání

Po relaci na vyžádání vám může Profiler vypršení časového limitu chybová zpráva:

![Chyba časového limitu Profiler][profiler-timeout]

Tato chyba se může zobrazit pro některý z následujících důvodů:

* Profiler relaci na vyžádání bylo úspěšné, ale trvalo déle, než se očekávalo se shromážděná data zpracovat, Application Insights.  

  Pokud není během 15 minut zpracování dat na portálu zobrazí zprávu vypršení časového limitu. Po chvíli ale Profiler trasování se zobrazí. Pokud se zobrazí chybová zpráva, teď ho ignorujte. Aktivně pracujeme na opravě.

* Webové aplikace má starší verzi agenta Profiler, který nemá funkce na vyžádání.  

  Pokud jste povolili Application Insights Profiler dříve, může být potřeba aktualizovat váš Profiler agenta chcete začít používat funkce na vyžádání.
  
Přejděte na App Services **nastavení aplikace** podokno a vyhledejte následující nastavení:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Nahraďte správné Instrumentační klíč pro službu Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Pokud nejsou nastavené žádné předchozí hodnoty, nainstalujte nejnovější rozšíření webu následujícím způsobem:

1. Přejděte **Application Insights** podokně portálu App Services.

    ![Povolit Application Insights z portálu služby App Services][enable-app-insights]

1. Pokud **Application Insights** podokně se zobrazí **aktualizovat** tlačítko, vyberte ji a aktualizovat rozšíření webu Application Insights, který nainstaluje nejnovější verzi agenta Profiler.

    ![Aktualizovat rozšíření webu][update-site-extension]

1. Ujistěte se, že je zapnutá Profiler, vyberte **změnu**a pak vyberte **OK** a uložte změny.

    ![Změnit a uložit app insights][change-and-save-appinsights]

1. Přejděte zpět na **nastavení aplikace** podokno pro službu App Service k zajištění, že jsou nastaveny následující hodnoty:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Nahraďte správné Instrumentační klíč pro službu application insights.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET 
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![Nastavení aplikace pro Profiler][app-settings-for-profiler]

1. Volitelně vyberte **rozšíření**a zkontrolujte verzi rozšíření a určit, zda je k dispozici aktualizace.

    ![Vyhledat aktualizace rozšíření][check-for-extension-update]

## <a name="next-steps"></a>Další postup
[Povolit Profiler a zobrazit trasování](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
