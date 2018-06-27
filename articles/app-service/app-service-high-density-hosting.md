---
title: S vysokou hustotou hostování na Azure App Service pomocí škálování pro aplikaci | Microsoft Docs
description: S vysokou hustotou hostování na Azure App Service
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.openlocfilehash: 97e1efe34417c3bf2f23801b2112b718f55d3416
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36961919"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>S vysokou hustotou hostování na Azure App Service pomocí škálování pro aplikaci
Ve výchozím nastavení, škálovat aplikace služby App Service pomocí příjmu [plán služby App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) běží na. Když se více aplikací spouštějí ve stejné plán služby App Service, každá instance upraveným spustí všechny aplikace v plánu.

Můžete povolit *škálování pro aplikaci* v App Service plánování úroveň. Se škáluje aplikace nezávisle plán služby App Service, který je hostitelem ho. Tímto způsobem plán služby App Service můžete škálovat na 10 instancí, ale aplikace může být nastaven na použití pouze pěti.

> [!NOTE]
> Změna velikosti pro aplikaci je k dispozici pouze pro **standardní**, **Premium**, **Premium V2** a **izolovaná** cenové úrovně.
>

## <a name="per-app-scaling-using-powershell"></a>Jednotlivé aplikace příjmu pomocí Powershellu

Vytvoření plánu s škálování předáním v pro aplikaci ```-perSiteScaling $true``` atribut ```New-AzureRmAppServicePlan``` PowerShell.

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktualizace existujícího plánu služby App Service pomocí škálování pro aplikaci: 

- získat plán cíl ```Get-AzureRmAppServicePlan```
- Úprava vlastností místně ```$newASP.PerSiteScaling = $true```
- uložení změn zpět do azure ```Set-AzureRmAppServicePlan``` 

```powershell
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Na úrovni aplikace nakonfigurujte počet instancí, které aplikace můžete používat v plánu služby App Service.

V následujícím příkladu je omezený na dvě instance bez ohledu na to, kolik instancí základní plán služby app service horizontálně navýší kapacitu pro aplikaci.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` se liší od `$newapp.MaxNumberOfWorkers`. Pro aplikaci škálování používá `$newapp.SiteConfig.NumberOfWorkers` k určení charakteristik škálování aplikace.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Škálování pro aplikaci pomocí Azure Resource Manager

Vytvoří následující šablony Azure Resource Manager:

- Plán služby App Service, která je škálovat na 10 instancí
- aplikace, který je nakonfigurovaný škálování na maximální pět instancí.

Plán služby App Service se nastaví **PerSiteScaling** vlastnost na hodnotu true `"perSiteScaling": true`. Aplikace se nastaví **počet pracovních procesů** sloužící k 5 `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Doporučenou konfiguraci pro hostování s vysokou hustotou
Za škálování aplikace je funkce, která je povolena v obou globální oblastech Azure a [prostředí App Service](environment/app-service-app-service-environment-intro.md). Doporučená strategie je však používat prostředí App Service k využívat jejich pokročilých funkcí a větší fondy kapacity.  

Postupujte podle těchto kroků nakonfigurujete vysokou hustotou hostování pro vaše aplikace:

1. Konfigurace služby App Service Environment a vyberte fond pracovních procesů, který je vyhrazen pro hostování scénáři s vysokou hustotou.
1. Vytvoření jednoho plánu služby App Service a škálujte ji používat všechny dostupné kapacity ve fondu pracovních procesů.
1. Nastavte `PerSiteScaling` příznak na hodnotu true na plán služby App Service.
1. Nové aplikace jsou vytvořeny a přiřazené plán služby App Service pomocí **numberOfWorkers** vlastnost nastavena na hodnotu **1**. Pomocí této konfigurace poskytuje nejvyšší hustotou možné u tohoto fondu pracovního procesu.
1. Počet pracovních procesů může být nakonfigurováno nezávisle na aplikaci podle potřeby udělovat další prostředky. Příklad:
    - Můžete nastavit aplikaci intenzivně využívaných **numberOfWorkers** k **3** tak, aby měl větší kapacitu zpracování pro tuto aplikaci. 
    - Nízká použití aplikace se nastavuje **numberOfWorkers** k **1**.

## <a name="next-steps"></a>Další kroky

- [Podrobný přehled plánů služby Azure App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Úvod do prostředí App Service](environment/app-service-app-service-environment-intro.md)
