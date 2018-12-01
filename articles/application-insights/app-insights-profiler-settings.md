---
title: Okno nastavení Azure Application Insights Profiler | Dokumentace Microsoftu
description: Zobrazit stav profileru a spuštění relace profilování
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d96b4a99b2ea66cdeff43f06c1789dd133c2c31a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723002"
---
# <a name="configure-application-insights-profiler"></a>Konfigurovat Application Insights Profiler

## <a name="profiler-settings-page"></a>Stránka nastavení Profiler

Na stránce nastavení profileru můžete otevřít ze stránky s Application Insights výkon stisknutím kombinace kláves **Profiler** tlačítko.

![Nakonfigurujte záznamy v podokně profileru][configure-profiler-entry]

Na stránce konfigurace Application Insights Profiler obsahuje čtyři funkce: 
1. **Profil nyní** – kliknutím na toto tlačítko způsobí, že relace profilování spuštění pro všechny aplikace, které jsou propojeny k této instanci Application Insights
1. **Propojené aplikace** – seznam aplikací, odesílání profiler k tomuto prostředku Application Insights
1. **Relace v průběhu** – při stisknutí klávesy **profilu nyní**, stav relace se tady zobrazí.)
1. **Nedávné relace profilace** – zobrazí informace o poslední relace profilování.

![Profiler na vyžádání][profiler-on-demand]

## <a name="app-service-environments-ase"></a>Služby App Service Environment (ASE)
V závislosti na konfiguraci služby ASE může být zablokován volání na kontrolu stavu agenta. Tato stránka se dozvíte, když se ve skutečnosti není spuštěn agent. Webové úlohy můžete zkontrolovat Opravdu chcete ve své aplikaci. Ale pokud jsou všechna nastavení aplikací správně nastavené a rozšíření webu App Insights je nainstalován ve své aplikaci, poběží profileru a nedávné relace profilování v seznamu byste měli vidět, pokud neexistuje odpovídající provozu do vaší aplikace.

## <a id="profileondemand"></a> Ručně aktivovat Profiler

Profiler lze spustit ručně kliknutím na jedno tlačítko. Předpokládejme, že jste spustili v testu výkonnosti webu. Budete potřebovat trasování, které vám pomohou pochopit, jaký je výkon vaší webové aplikace v zatížení. Máte kontrolu nad při zaznamenat trasování je zásadní, protože nevíte, kdy bude spuštěn zátěžový test, ale náhodný vzorkovací přijít.
Následující postup ukazuje, jak v tomto scénáři funguje:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Volitelné) Krok 1: Vytvořte provozu do vaší webové aplikace tím, že spuštění testu výkonnosti webu

Pokud vaše webová aplikace už obsahuje příchozí provoz nebo pokud chcete jen vygenerovat ručně provoz, tuto část přeskočit a pokračovat v kroku 2.

Přejděte na portál Application Insights **konfigurovat > testování výkonu**. Klikněte na tlačítko Spustit nový test výkonnosti.

![Vytvořit nový test výkonnosti][create-performance-test]

V **nový test výkonnosti** podokně nakonfigurovat cílová adresa URL testu. Přijměte všechna výchozí nastavení a spuštění zátěžového testu.

![Konfigurace zátěžového testu][configure-performance-test]

Zobrazí se že nový test se do fronty zařadí nejprve, za nímž následuje ve stavu v průběhu.

![zátěžový test je odeslána a zařazeny do fronty][load-test-queued]

![v průběhu je spuštěn zátěžový test][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Krok 2: Spuštění profilování na vyžádání

Jakmile je spuštěn zátěžový test, můžeme začít profiler k zaznamenání trasování ve webové aplikaci, zatímco přijímá zatížení.
Přejděte do podokna Profiler konfigurace:


### <a name="step-3-view-traces"></a>Krok 3: Zobrazení trasování

Po doběhnutí profileru, postupujte podle pokynů v oznámení pro přechod na stránku a zobrazení trasování výkonu.

## <a name="troubleshooting-on-demand-profiler"></a>Řešení potíží s profilování na vyžádání

V některých případech může se zobrazit chybová zpráva vypršení časového limitu Profiler po relaci na vyžádání:

![Chyba časového limitu Profiler][profiler-timeout]

Může existovat dva důvody, proč se zobrazí tato chyba:

1. Relace profilování na vyžádání bylo úspěšné, ale trvalo déle zpracovat shromážděná data, Application Insights. Pokud data se nedokončilo zpracování za 15 minut, na portálu se zobrazí zpráva vypršení časového limitu. Když po chvíli se zobrazí trasování Profiler. Pokud k tomu dojde, ignorujte prozatím chybová zpráva. Aktivně pracujeme na opravě.

1. Webové aplikace má starší verzi agenta Profiler, který nemá žádné funkce na vyžádání. Pokud jste dříve povolili profil Application Insights, je pravděpodobné, že je potřeba aktualizovat váš Profiler agenta chcete začít používat funkce na vyžádání.
  
Postupujte podle těchto kroků zkontrolujte a nainstalujte nejnovější Profiler:

1. Přejděte do nastavení aplikace služby App a zaškrtněte, pokud následující nastavení mají hodnotu:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: nahraďte správné Instrumentační klíč Application Insights.
    * **APPINSIGHTS_PORTALINFO**: TECHNOLOGIE ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 Pokud některé z těchto nastavení nejsou nastavená, přejděte do podokna povolení Application Insights se nainstalovat nejnovější rozšíření lokality.

1. Přejděte do podokna Application Insights na portálu služby App Services.

    ![Povolit Application Insights na portálu služby App Services][enable-app-insights]

1. Pokud se zobrazí na tlačítko 'Aktualizovat' v následující stránku, klikněte na něj aktualizovat rozšíření webu Application Insights, který bude instalovat nejnovější verzi agenta Profiler.

    ![Aktualizovat rozšíření webu][update-site-extension]

1. Pak klikněte na tlačítko **změnit** chcete zajistit, Profiler zapnutý a vyberte **OK** a uložte změny.

    ![Změnit a uložit app insights][change-and-save-appinsights]

1. Přejděte zpět na **nastavení aplikace** jsou nastaveny kartu pro App Service a zkontrolujte následující položky nastavení aplikace:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: nahraďte správné Instrumentační klíč pro službu application insights.
    * **APPINSIGHTS_PORTALINFO**: TECHNOLOGIE ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![nastavení aplikace pro profiler][app-settings-for-profiler]

1. Volitelně můžete zaškrtnutím verze rozšíření a ujistěte se, že není k dispozici žádná aktualizace.

    ![Vyhledat aktualizace rozšíření][check-for-extension-update]

## <a name="next-steps"></a>Další kroky
[Povolit Profiler a zobrazení trasování](app-insights-profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png