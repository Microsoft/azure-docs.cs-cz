---
title: Profilujte živé aplikace služby Azure App Service pomocí přehledů aplikací | Dokumenty společnosti Microsoft
description: Profilujte živé aplikace ve službě Azure App Service pomocí profileru Application Insights.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275773"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilujte živé aplikace služby Azure App Service pomocí přehledů aplikací

Profiler můžete spustit na ASP.NET a ASP.NET základních aplikacích, které běží ve službě Azure App Service pomocí úrovně basic nebo vyšší. Povolení Profiler na Linuxu je v současné době možné pouze prostřednictvím [této metody](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a>Povolení profileru pro vaši aplikaci
Pokud chcete pro aplikaci povolit Profiler, postupujte podle následujících pokynů. Pokud používáte jiný typ služby Azure, tady jsou pokyny pro povolení Profiler na jiných podporovaných platformách:
* [Cloudové služby](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Profiler Application Insights je předinstalovaný jako součást runtime služby App Services. Následující postup vám ukáže, jak ji povolit pro vaši službu App Service. Postupujte podle těchto kroků, i když jste v době sestavení zahrnuli sdk App Insights do aplikace.

1. Povolte nastavení "Vždy zapnuto" pro vaši službu aplikace. Nastavení můžete aktualizovat na stránce Konfigurace služby App Service v části Obecné nastavení.
1. Přejděte do podokna **Služby pro aplikace** na webu Azure Portal.
1. Přejděte do podokna **Nastavení > přehledy aplikací.**

   ![Povolení přehledů aplikací na portálu služby App Services](./media/profiler/AppInsights-AppServices.png)

1. Podle pokynů v podokně vytvořte nový prostředek nebo vyberte existující prostředek App Insights, který bude sledovat vaši aplikaci. Také se ujistěte, profileru je **zapnuto**. Pokud váš prostředek Application Insights je v jiném předplatném než vaše služba App Service, nemůžete tuto stránku použít ke konfiguraci Application Insights. Stále to můžete udělat ručně, i když tím, že vytvoříte potřebné nastavení aplikace ručně. [Další část obsahuje pokyny pro ruční povolení Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Přidání rozšíření webu Přehledy aplikací][Enablement UI]

1. Profiler je teď povolený pomocí nastavení aplikací služby App Services.

    ![Nastavení aplikace pro profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Povolení profileru ručně nebo pomocí Správce prostředků Azure
Profiler přehledů aplikací lze povolit vytvořením nastavení aplikace pro službu Azure App Service. Stránka s výše uvedenými možnostmi vytvoří tato nastavení aplikace. Ale můžete automatizovat vytváření těchto nastavení pomocí šablony nebo jinými prostředky. Tato nastavení budou fungovat také v případě, že váš prostředek Application Insights je v jiném předplatném než vaše služba Azure App Service.
Zde jsou nastavení potřebná k povolení profileru:

|Nastavení aplikace    | Hodnota    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro váš prostředek Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Tyto hodnoty můžete nastavit pomocí [šablon Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), Azure [Powershellu](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp), [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Ruční povolení profileru pro jiné cloudy

Pokud chcete povolit profiler pro jiné cloudy, můžete použít níže uvedené nastavení aplikace.

|Nastavení aplikace    | Hodnoty vlády USA| Čína Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Zakázat profiler

Pokud chcete zastavit nebo restartovat Profiler pro instanci jednotlivých aplikací, přejděte v části **Webové úlohy**na prostředek aplikace. Chcete-li odstranit profiler, přejděte na **rozšíření**.

![Zakázat profiler pro webovou úlohu][disable-profiler-webjob]

Doporučujeme, abyste ve všech vašich aplikacích povolili profiler, abyste co nejdříve zjistili případné problémy s výkonem.

Soubory profileru lze odstranit při použití aplikace WebDeploy k nasazení změn do webové aplikace. Odstranění můžete zabránit vyloučením odstranění složky App_Data během nasazení. 


## <a name="next-steps"></a>Další kroky

* [Práce s Application Insights v sadě Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
