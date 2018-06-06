---
title: S vysokou hustotou hostování na Azure App Service | Microsoft Docs
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
ms.openlocfilehash: 8bdf62ce70929c583b59b91e5f1e7c520376120b
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735808"
---
# <a name="high-density-hosting-on-azure-app-service"></a>S vysokou hustotou hostování na Azure App Service
Při používání služby App Service, odpojené od kapacitu přidělených dvěma konceptů aplikace:

* **Aplikace:** představuje aplikace a jeho konfigurace modulu runtime. Například obsahuje verze rozhraní .NET, která by se měly načíst modul runtime, nastavení aplikace.
* **Plán služby App Service:** definuje vlastnosti kapacitu, sada k dispozici funkcí a polohu aplikace. Charakteristiky může být například velký počítač (4 jádra), čtyři instancí, prémiové funkce v oblasti Východ USA.

Aplikace je vždy spojen plán služby App Service, ale plán služby App Service může poskytnout dostatečnou kapacitu pro jednu nebo více aplikací.

V důsledku toho platformou poskytuje možnost izolovat aplikace na jeden nebo více aplikacemi sdílet prostředky sdílením plán služby App Service.

Ale při více aplikací sdílet plán služby App Service, instance této aplikace běží na všechny instance tohoto plánu služby App Service.

## <a name="per-app-scaling"></a>Pokud na škálování aplikace
*Pokud na škálování aplikace* je funkce, která může být povolena na úrovni plán služby App Service a pak se použije na aplikaci.

Jednotlivé aplikace škáluje škálování aplikace nezávisle plán služby App Service, který je hostitelem ho. Tímto způsobem plán služby App Service můžete škálovat na 10 instancí, ale aplikace může být nastaven na použití pouze pěti.

   >[!NOTE]
   >Na aplikace škálování, je k dispozici pouze pro **standardní**, **Premium**, **Premium V2** a **izolovaná** plánů služby App Service SKU
   >

### <a name="per-app-scaling-using-powershell"></a>Jednotlivé aplikace příjmu pomocí Powershellu

Vytvoření plánu nakonfigurovaný jako *za škálování aplikace* plán předáním v ```-perSiteScaling $true``` atribut ```New-AzureRmAppServicePlan``` PowerShell.

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Pokud chcete aktualizovat existující plán služby App Service k použití této funkce: 

- získat plán cíl ```Get-AzureRmAppServicePlan```
- Úprava vlastností místně ```$newASP.PerSiteScaling = $true```
- uložení změn zpět do azure ```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Na úrovni aplikace nakonfigurujte počet instancí, které aplikace můžete používat v plánu služby app service.

V následujícím příkladu je omezený na dvě instance bez ohledu na to, kolik instancí základní plán služby app service horizontálně navýší kapacitu pro aplikaci.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp. SiteConfig.NumberOfWorkers se liší od $newapp. MaxNumberOfWorkers. Jednotlivé aplikace používá škálování $newapp. SiteConfig.NumberOfWorkers k určení charakteristik škálování aplikace.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Pokud na škálování aplikace pomocí Azure Resource Manager

Následující *šablony Azure Resource Manageru* vytvoří:

- Plán služby App Service, která je škálovat na 10 instancí
- aplikace, který je nakonfigurovaný škálování na maximální pět instancí.

Plán služby App Service se nastaví **PerSiteScaling** vlastnost na hodnotu true ```"perSiteScaling": true```. Aplikace se nastaví **počet pracovních procesů** sloužící k 5 ```"properties": { "numberOfWorkers": "5" }```.

```
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
Za škálování aplikace je funkce, která je povolena v globální oblastí Azure a prostředí App Service. Doporučená strategie je však používat prostředí App Service k využívat jejich pokročilých funkcí a větší fondy kapacity.  

Postupujte podle těchto kroků nakonfigurujete vysokou hustotou hostování pro vaše aplikace:

1. Konfigurace služby App Service Environment a vyberte fond pracovních procesů, který je vyhrazen pro hostování scénáři s vysokou hustotou.
1. Vytvoření jednoho plánu služby App Service a škálujte ji používat všechny dostupné kapacity ve fondu pracovních procesů.
1. Nastavte příznak PerSiteScaling plán aplikační služby na hodnotu true.
1. Nové aplikace jsou vytvořeny a přiřazené plán služby App Service pomocí **numberOfWorkers** vlastnost nastavena na hodnotu **1**. Pomocí této konfigurace poskytuje nejvyšší hustotou možné u tohoto fondu pracovního procesu.
1. Počet pracovních procesů může být nakonfigurováno nezávisle na aplikaci podle potřeby udělovat další prostředky. Příklad:
    - Můžete nastavit aplikaci intenzivně využívaných **numberOfWorkers** k **3** tak, aby měl větší kapacitu zpracování pro tuto aplikaci. 
    - Nízká použití aplikace se nastavuje **numberOfWorkers** k **1**.

## <a name="next-steps"></a>Další kroky

- [Podrobný přehled plánů služby Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Úvod do prostředí App Service](app-service-app-service-environment-intro.md)
