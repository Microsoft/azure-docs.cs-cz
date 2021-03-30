---
title: Škálování na aplikaci pro hostování s vysokou hustotou
description: Škálujte aplikace nezávisle na App Service plánech a optimalizujte instance s horizontálním škálováním v plánu.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74672353"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Vysoká hustota hostování na Azure App Service pomocí škálování podle aplikace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při použití App Service můžete škálovat aplikace škálováním [App Service plánu](overview-hosting-plans.md) , na kterém běží. Když se ve stejném plánu App Service spouští víc aplikací, každá instance s horizontálním škálováním na víc instancí spustí všechny aplikace v plánu.

*Škálování na aplikaci* lze povolit na úrovni plánu App Service, aby bylo možné aplikaci škálovat nezávisle na App Service plánu, který je hostuje. V takovém případě je možné plán App Service škálovat na 10 instancí, ale aplikaci můžete nastavit tak, aby se používala jenom pět.

> [!NOTE]
> Škálování podle aplikace je dostupné jenom pro cenové úrovně **Standard**, **Premium**, **Premium v2** a **Isolated** .
>

Aplikace se přidělují k dispozici App Service plánu s využitím optimálního přístupu k distribuci napříč instancemi. I když není zaručená distribuce, platforma zajistí, že dvě instance stejné aplikace nebudou hostovány ve stejné App Service plánování instance.

Platforma nespoléhá na metriky při rozhodování o přidělení pracovního procesu. Aplikace se znovu vyrovnávají jenom v případě, že se instance přidávají nebo odebírají z App Serviceho plánu.

## <a name="per-app-scaling-using-powershell"></a>Škálování podle aplikace pomocí PowerShellu

Vytvořte plán s škálováním na aplikaci předáním ```-PerSiteScaling $true``` parametru do ```New-AzAppServicePlan``` rutiny.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Povolte škálování podle aplikace s existujícím plánem App Service předáním `-PerSiteScaling $true` parametru do ```Set-AzAppServicePlan``` rutiny.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na úrovni aplikace nakonfigurujte počet instancí, které aplikace může používat v plánu App Service.

V následujícím příkladu je aplikace omezená na dvě instance bez ohledu na to, na kolik instancí se základní plán služby App Service škáluje.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` se liší od `$newapp.MaxNumberOfWorkers` . Škálování podle aplikace používá `$newapp.SiteConfig.NumberOfWorkers` k určení charakteristik měřítka aplikace.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Škálování podle aplikace pomocí Azure Resource Manager

Následující šablona Azure Resource Manager vytvoří:

- Plán App Service, který se škáluje na 10 instancí
- aplikace, která je nakonfigurovaná tak, aby se mohla škálovat na maximálně pět instancí.

App Service plán nastavuje vlastnost **PerSiteScaling** na hodnotu true `"perSiteScaling": true` . Aplikace nastavuje **počet pracovních procesů** , které se mají použít na 5 `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="recommended-configuration-for-high-density-hosting"></a>Doporučená konfigurace pro hostování s vysokou hustotou

Škálování na aplikaci je funkce, která je povolená v globálních oblastech Azure i v [App Servicech prostředích](environment/app-service-app-service-environment-intro.md). Doporučuje se ale použít prostředí App Service k využití jejich pokročilých funkcí a větší App Service kapacity plánu.  

Pomocí těchto kroků můžete nakonfigurovat hostování s vysokou hustotou pro vaše aplikace:

1. Určete plán App Service jako plán s vysokou hustotou a proveďte jeho horizontální navýšení kapacity na požadovanou kapacitu.
1. Nastavte `PerSiteScaling` příznak na hodnotu true v plánu App Service.
1. Vytvoří se nové aplikace a přiřadí se k tomuto App Service plánu s vlastností **numberOfWorkers** nastavenou na hodnotu **1**.
   - Použití této konfigurace má za důsledek nejvyšší možnou hustotu.
1. Počet pracovních procesů se dá nakonfigurovat nezávisle na aplikaci, aby bylo možné v případě potřeby udělit další prostředky. Například:
   - Aplikace s vysokým využitím může nastavit **numberOfWorkers** na **3** , aby pro tuto aplikaci měla větší kapacitu zpracování.
   - Aplikace s nízkým použitím by nastavily **numberOfWorkers** na **1**.

## <a name="next-steps"></a>Další kroky

- [Podrobný přehled plánů Azure App Service](overview-hosting-plans.md)
- [Úvod do prostředí App Service](environment/app-service-app-service-environment-intro.md)
