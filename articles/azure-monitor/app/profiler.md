---
title: Profilování živých Azure App Service aplikací pomocí Application Insights | Microsoft Docs
description: Profilujte živé aplikace na Azure App Service s využitím Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b743b5be195f44c03adbee75c3108f4908d8d4e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717753"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilování aplikací v reálném čase Azure App Service s využitím Application Insights

Profiler můžete spustit v ASP.NET a aplikacích ASP.NET Core, které běží na Azure App Service pomocí úrovně Basic nebo vyšší. Povolení profileru v systému Linux je aktuálně možné pouze prostřednictvím [této metody](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Povolení profileru pro vaši aplikaci
Pokud chcete povolit profiler pro aplikaci, postupujte podle následujících pokynů. Pokud používáte jiný typ služby Azure, najdete tady pokyny k povolení profileru na jiných podporovaných platformách:
* [Cloudové služby](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric aplikací](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler je předem nainstalován jako součást modulu runtime App Services. Následující postup vám ukáže, jak ho pro App Service povolit. Postupujte podle těchto kroků i v případě, že jste do aplikace zahrnuli sadu App Insights SDK v době sestavení.

> [!NOTE]
> Nekódovatelné instalace Application Insights Profiler se řídí zásadami podpory .NET Core.
> Další informace o podporovaných modulech runtime najdete v tématu [zásady podpory .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

1. Pro App Service přejděte na ovládací panel Azure.
1. U služby App Service povolte nastavení "Always On". Toto nastavení najdete v části **Nastavení**, **konfigurační** stránka (viz snímek obrazovky v dalším kroku) a vyberte kartu **Obecné nastavení** .
1. Přejděte na **nastavení > Application Insights** stránce.

   ![Povolení App Insights na portálu App Services](./media/profiler/AppInsights-AppServices.png)

1. Podle pokynů v podokně vytvořte nový prostředek nebo vyberte existující prostředek App Insights, abyste mohli svoji aplikaci monitorovat. Také se ujistěte, že je profiler **zapnutý**. Pokud se prostředek Application Insights v jiném předplatném, než App Service, nemůžete tuto stránku použít ke konfiguraci Application Insights. Můžete to provést ručně, i když vytvoříte potřebná nastavení aplikace ručně. [V další části najdete pokyny pro ruční povolení profileru.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Přidat rozšíření webu App Insights][Enablement UI]

1. Profiler je teď povolený pomocí nastavení aplikace App Services.

    ![Nastavení aplikace pro Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Ruční povolení profileru nebo Azure Resource Manager
Application Insights Profiler můžete povolit vytvořením nastavení aplikace pro Azure App Service. Stránka s výše uvedenými možnostmi vytvoří tato nastavení aplikace. Vytváření těchto nastavení ale můžete automatizovat pomocí šablony nebo jiným způsobem. Tato nastavení budou fungovat i v případě, že se váš Application Insights prostředek nachází v jiném předAzure App Service platném.
Tady jsou nastavení potřebná k povolení profileru:

|Nastavení aplikace    | Hodnota    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro prostředek Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


Tyto hodnoty můžete nastavit pomocí [šablon Azure Resource Manager](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure POWERSHELL](/powershell/module/az.websites/set-azwebapp)a  [Azure CLI](/cli/azure/webapp/config/appsettings).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Ruční povolení profileru pro ostatní cloudy

Pokud chcete Profiler povolit pro jiné cloudy, můžete použít následující nastavení aplikace.

|Nastavení aplikace    | Hodnoty pro státní správu USA| Čína – Cloud |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Zakázat Profiler

Pokud chcete zastavit nebo restartovat profiler pro instanci jednotlivé aplikace, na levém bočním panelu vyberte **WebJobs** a zastavte webovou úlohu s názvem `ApplicationInsightsProfiler3` .

  ![Zakázat profiler pro webovou úlohu][disable-profiler-webjob]

Doporučujeme, abyste pro všechny vaše aplikace povolili Profiler, abyste zjistili případné problémy s výkonem co nejdříve.

Soubory profileru lze odstranit při použití nástroje WebDeploy k nasazení změn do vaší webové aplikace. Odstranění můžete zabránit tak, že odstraníte složku App_Data, která se má odstranit během nasazení. 


## <a name="next-steps"></a>Další kroky

* [Práce s Application Insights v sadě Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

