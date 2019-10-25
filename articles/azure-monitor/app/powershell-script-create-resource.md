---
title: Skript PowerShellu pro vytvoření prostředku Application Insights | Microsoft Docs
description: Automatizuje vytváření prostředků Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820684"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Rutina PowerShell pro vytvoření prostředku Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Když chcete monitorovat novou aplikaci nebo novou verzi aplikace – pomocí [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), nastavíte v Microsoft Azure nový prostředek. Tento prostředek je místo, kde se analyzují a zobrazují data telemetrie z vaší aplikace. 

Vytváření nového prostředku můžete automatizovat pomocí prostředí PowerShell.

Pokud například vyvíjíte aplikaci pro mobilní zařízení, je možné, že budete mít v některých případech několik publikovaných verzí vaší aplikace, které vaši zákazníci používají. Nechcete získat výsledky telemetrie z různých verzí v kombinaci. Proto získáte proces sestavení pro vytvoření nového prostředku pro každé sestavení.

> [!NOTE]
> Pokud chcete vytvořit sadu prostředků ve stejnou dobu, zvažte [vytvoření prostředků pomocí šablony Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript pro vytvoření prostředku Application Insights
Podívejte se na příslušné specifikace rutiny:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShellový skript*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Co dělat s iKey
Jednotlivé prostředky identifikuje klíč instrumentace (iKey). IKey je výstupem skriptu pro vytvoření prostředku. Váš skript sestavení by měl poskytovat iKey Application Insights SDK vložené do vaší aplikace.

Existují dva způsoby, jak iKey zpřístupnit pro sadu SDK:

* V [souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Nebo v [inicializačním kódu](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření prostředků Application Insights a webového testu ze šablon](powershell.md)
* [Nastavení monitorování diagnostiky Azure pomocí PowerShellu](powershell-azure-diagnostics.md) 
* [Nastavení upozornění pomocí prostředí PowerShell](powershell-alerts.md)

