---
title: Profilování živých Azure App Service aplikací pomocí Application Insights | Microsoft Docs
description: Profilujte živé aplikace na Azure App Service s využitím Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275773"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilování aplikací v reálném čase Azure App Service s využitím Application Insights

Profiler můžete spustit v ASP.NET a aplikacích ASP.NET Core, které běží na Azure App Service pomocí úrovně Basic nebo vyšší. Povolení profileru v systému Linux je aktuálně možné pouze prostřednictvím [této metody](profiler-aspnetcore-linux.md).

## <a id="installation"></a>Povolení profileru pro vaši aplikaci
Pokud chcete povolit profiler pro aplikaci, postupujte podle následujících pokynů. Pokud používáte jiný typ služby Azure, najdete tady pokyny k povolení profileru na jiných podporovaných platformách:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric aplikací](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler je předem nainstalován jako součást modulu runtime App Services. Následující postup vám ukáže, jak ho pro App Service povolit. Postupujte podle těchto kroků i v případě, že jste do aplikace zahrnuli sadu App Insights SDK v době sestavení.

1. U služby App Service povolte nastavení "Always On". Nastavení můžete aktualizovat na stránce konfigurace App Service v části Obecné nastavení.
1. V Azure Portal otevřete podokno **App Services** .
1. Přejděte do **nastavení > Application Insights** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/profiler/AppInsights-AppServices.png)

1. Podle pokynů v podokně vytvořte nový prostředek nebo vyberte existující prostředek App Insights, abyste mohli svoji aplikaci monitorovat. Také se ujistěte, že je profiler **zapnutý**. Pokud se prostředek Application Insights v jiném předplatném, než App Service, nemůžete tuto stránku použít ke konfiguraci Application Insights. Můžete to provést ručně, i když vytvoříte potřebná nastavení aplikace ručně. [V další části najdete pokyny pro ruční povolení profileru.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Přidat rozšíření webu App Insights][Enablement UI]

1. Profiler se teď aktivuje pomocí nastavení aplikace služby App.

    ![Nastavení aplikace, které pro Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ruční povolení profileru nebo Azure Resource Manager
Application Insights Profiler můžete povolit vytvořením nastavení aplikace pro Azure App Service. Stránka s výše uvedenými možnostmi vytvoří tato nastavení aplikace. Vytváření těchto nastavení ale můžete automatizovat pomocí šablony nebo jiným způsobem. Tato nastavení budou fungovat i v případě, že se váš Application Insights prostředek nachází v jiném předAzure App Service platném.
Tady jsou nastavení potřebná k povolení profileru:

|Nastavení aplikace    | Hodnota    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro prostředek Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Tyto hodnoty můžete nastavit pomocí [šablon Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure PowerShellu](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)a rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Ruční povolení profileru pro ostatní cloudy

Pokud chcete Profiler povolit pro jiné cloudy, můžete použít následující nastavení aplikace.

|Nastavení aplikace    | Hodnoty pro státní správu USA| Čína – Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Zakázat Profiler

Pokud chcete zastavit nebo restartovat profiler pro instanci jednotlivé aplikace, v části **webové úlohy**přejdete do prostředku aplikace. Profiler odstraníte tak, že přejdete na **rozšíření**.

![Zakázat Profiler pro webové úlohy][disable-profiler-webjob]

Doporučujeme, abyste pro všechny vaše aplikace povolili Profiler, abyste zjistili případné problémy s výkonem co nejdříve.

Soubory profileru lze odstranit při použití nástroje WebDeploy k nasazení změn do vaší webové aplikace. Odstranění můžete zabránit tak, že odstraníte složku App_Data, která se má odstranit během nasazení. 


## <a name="next-steps"></a>Další kroky

* [Práce s Application Insights v aplikaci Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
