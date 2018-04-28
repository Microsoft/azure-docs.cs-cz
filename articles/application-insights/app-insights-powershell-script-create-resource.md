---
title: Skript prostředí PowerShell pro vytvoření prostředek Application Insights | Microsoft Docs
description: Automatizovat vytváření prostředků Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: d06b44246b694c0d2a83503ecd1ae0cedfadd9ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Rutina PowerShell pro vytvoření prostředku Application Insights


Pokud chcete monitorování nové aplikace - nebo nová verze aplikace - s [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), můžete nastavit nový prostředek v Microsoft Azure. Tento prostředek je, kde analyzovat a zobrazí data telemetrie z vaší aplikace. 

Vytvoření nového prostředku můžete automatizovat pomocí prostředí PowerShell.

Například pokud vyvíjíte aplikace mobilních zařízení, je pravděpodobné, že, kdykoli bude několik publikované verze aplikace používá vašich zákazníků. Nechcete získat výsledky telemetrická data z různých verzí ve smíšeném. Získáte tak vaše sestavení postup vytvoření nového prostředku pro každé sestavení.

> [!NOTE]
> Pokud chcete vytvořit sadu prostředků všechny najednou, zvažte [vytváření prostředků pomocí šablony Azure](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Skript pro vytvoření prostředek Application Insights
Zobrazit specifikace příslušné rutiny:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Skript prostředí PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

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


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Co dělat s iKey
Každý prostředek, je identifikován svůj klíč instrumentace (iKey). IKey je výstup skriptu pro vytváření prostředků. Skript buildu by měl poskytovat iKey do Application Insights SDK vloženému ve vaší aplikaci.

Existují dva způsoby, jak zpřístupnit iKey k sadě SDK:

* V [souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Nebo v [inicializace kód](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření služby Application Insights a web test prostředky ze šablon](app-insights-powershell.md)
* [Nastavení monitorování diagnostiky Azure pomocí prostředí PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Nastavit upozornění pomocí prostředí PowerShell](app-insights-powershell-alerts.md)

