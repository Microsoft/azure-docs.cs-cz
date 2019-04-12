---
title: Profilování živých aplikací Azure App Service pomocí nástroje Application Insights | Dokumentace Microsoftu
description: Profilování živých aplikací ve službě Azure App Service s Application Insights Profiler.
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
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493735"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilování živých aplikací Azure App Service pomocí nástroje Application Insights

Můžete spustit Profiler technologie ASP.NET a ASP.NET Core aplikací, které jsou spuštěny ve službě Azure App Service při použití úrovně služby Basic nebo vyšší. Povolení Profiler v Linuxu je aktuálně možné jenom přes [tato metoda](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Povolit Profiler pro vaši aplikaci
Pokud chcete povolit Profiler pro aplikaci, postupujte podle pokynů níže. Pokud používáte jiný typ služby Azure, tady jsou pokyny pro povolení Profiler na jiné podporované platformy:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler je již nainstalován jako součást modulu runtime služeb App Services. Jak se dá povolit pro službu App Service se zobrazí následující postup. I v případě, že jste zahrnuli App Insights SDK do vaší aplikace v době sestavení, postupujte podle těchto kroků.

1. Povolte nastavení "Always On" pro službu app service. Můžete aktualizovat nastavení na stránce konfigurace služby App Service v obecném nastavení.
1. Přejděte **App Services** podokně webu Azure Portal.
1. Přejděte do **Nastavení > Application Insights** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/profiler/AppInsights-AppServices.png)

1. Buď podle pokynů a vytvořte nový prostředek nebo vyberte existující prostředek App Insights pro sledování aplikace podokna. Také se ujistěte, Profiler je **na**. Pokud váš prostředek Application Insights v jiném předplatném než službu App Service, nelze na této stránce Konfigurovat Application Insights. Stále to zvládnete ručně i když tak, že vytvoříte nastavení potřebné aplikace ručně. [Další část obsahuje pokyny pro ruční povolení Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Přidat rozšíření webu App Insights][Enablement UI]

1. Profiler se teď aktivuje pomocí nastavení aplikace služby App.

    ![Nastavení aplikace, které pro Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Povolit Profiler ručně nebo pomocí Azure Resource Manageru
Application Insights Profiler je možné povolit tak, že vytvoříte nastavení aplikace pro službu Azure App Service. Na stránce s možnostmi výše uvedené vytvoří nastavení aplikace za vás. Ale můžete automatizovat vytváření těchto nastavení pomocí šablony nebo jiným způsobem. Tato nastavení budou fungovat i pokud je váš prostředek Application Insights v jiném předplatném než Azure App Service.
Tady je nastavení potřebná k povolení profileru:

|Nastavení aplikace    | Hodnota    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Instrumentační klíč pro váš prostředek Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Můžete nastavit tyto hodnoty pomocí [šablon Azure Resource Manageru](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [prostředí Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).


## <a name="disable-profiler"></a>Zakázat Profiler

Pro zastavení nebo restartování Profiler pro jednotlivé aplikace instance, v části **webové úlohy**, přejděte na prostředek aplikace. Chcete-li odstranit Profiler, přejděte na **rozšíření**.

![Zakázat Profiler pro webové úlohy][disable-profiler-webjob]

Doporučujeme, abyste měli Profiler povolená na všech svých aplikací se co nejdříve zjistit problémy s výkonem.

Profiler pro soubory můžete odstranit, při nasazení změny do vaší webové aplikace pomocí nástroje WebDeploy. Odstranění je možné zabránit vyloučením složku App_Data odstranit během nasazení. 


## <a name="next-steps"></a>Další postup

* [Práce s Application Insights v sadě Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
