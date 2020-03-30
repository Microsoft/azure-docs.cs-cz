---
title: Škálování pro aplikaci pro hostování s vysokou hustotou
description: Škálujte aplikace nezávisle na plánech služby App Service a optimalizujte škálované instance ve vašem plánu.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672353"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hostování s vysokou hustotou ve službě Azure App Service pomocí škálování pro aplikace

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při používání služby App Service můžete škálovat aplikace škálováním [plánu služby App Service,](overview-hosting-plans.md) na který běží. Když se ve stejném plánu služby App Service spustí více aplikací, každá instanci s horizontálním navýšením kapacity spustí všechny aplikace v plánu.

*Škálování na aplikaci* lze povolit na úrovni plánu služby App Service, aby bylo možné škálovat aplikaci nezávisle na plánu služby App Service, který ji hostuje. Tímto způsobem lze plán služby App Service škálovat na 10 instancí, ale aplikace může být nastavena tak, aby používala pouze pět.

> [!NOTE]
> Škálování pro jednotlivé aplikace je k dispozici pouze pro **standardní**, **prémiové**, **prémiové v2** a **izolované** cenové úrovně.
>

Aplikace se přidělují k dostupnému plánu služby App Service pomocí přístupu s maximálním úsilím pro rovnoměrnou distribuci mezi instancemi. Zatímco rovnoměrná distribuce není zaručena, platforma zajistí, že dvě instance stejné aplikace nebudou hostovány ve stejné instanci plánu služby App Service.

Platforma nespoléhá na metriky rozhodnout o přidělení pracovníka. Aplikace jsou znovu vyváženy pouze v případě, že instance jsou přidány nebo odebrány z plánu služby App Service.

## <a name="per-app-scaling-using-powershell"></a>Změna velikosti podle aplikací pomocí PowerShellu

Vytvořte plán s měřítkem pro aplikaci předáním parametru ```-PerSiteScaling $true``` do rutiny. ```New-AzAppServicePlan```

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Povolte škálování pro aplikaci pomocí existujícího `-PerSiteScaling $true` plánu služby App Service předáním parametru do rutiny. ```Set-AzAppServicePlan```

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na úrovni aplikace nakonfigurujte počet instancí, které aplikace může použít v plánu služby App Service.

V níže uvedeném příkladu je aplikace omezena na dvě instance bez ohledu na to, kolik instancí se základní plán služby App Service škáluje.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`se liší `$newapp.MaxNumberOfWorkers`od . Škálování na aplikaci používá `$newapp.SiteConfig.NumberOfWorkers` k určení charakteristiky škálování aplikace.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Škálování pro každý chod aplikací pomocí Azure Resource Manageru

Vytvoří se následující šablona Azure Resource Manageru:

- Plán služby App Service, který je škálován na 10 instancí
- aplikace, která je nakonfigurovaná pro škálování na maximálně pět instancí.

Plán služby App Service nastavuje vlastnost `"perSiteScaling": true` **PerSiteScaling** na hodnotu true . Aplikace nastavuje počet pracovníků, kteří `"properties": { "numberOfWorkers": "5" }`mají být **používáni,** na 5 .

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Doporučená konfigurace pro hosting s vysokou hustotou

Škálování podle aplikací je funkce, která je povolená v globálních oblastech Azure i v [prostředích služby App Service](environment/app-service-app-service-environment-intro.md). Doporučenou strategií je však použití prostředí služby App Service k využití jejich pokročilých funkcí a větší kapacity plánu služby App Service.  

Chcete-li nakonfigurovat hostování s vysokou hustotou pro vaše aplikace, postupujte takto:

1. Určete plán služby App Service jako plán s vysokou hustotou a škálujte jej na požadovanou kapacitu.
1. Nastavte `PerSiteScaling` příznak na true v plánu služby App Service.
1. Nové aplikace jsou vytvořeny a přiřazeny k tomuto plánu služby App Service s **vlastností numberOfWorkers** nastavenou na **1**.
   - Použití této konfigurace poskytuje nejvyšší možnou hustotu.
1. Počet pracovníků lze nakonfigurovat nezávisle na aplikaci udělit další prostředky podle potřeby. Například:
   - Aplikace s vysokým použitím můžete nastavit **numberOfWorkers** na **3** mít větší kapacitu zpracování pro tuto aplikaci.
   - Aplikace s nízkým použitím by **nastavily numberOfWorkers** na **1**.

## <a name="next-steps"></a>Další kroky

- [Podrobný přehled plánů služby Azure App Service](overview-hosting-plans.md)
- [Úvod do prostředí App Service](environment/app-service-app-service-environment-intro.md)
