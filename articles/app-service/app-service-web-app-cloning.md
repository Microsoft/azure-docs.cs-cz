---
title: Klonování aplikací s využitím prostředí PowerShell – Azure App Service
description: Zjistěte, jak klonovat aplikaci služby App Service do nové aplikace pomocí Powershellu.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 17ea8545855cd926a393e9e40d3eccaabd6dba53
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886522"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplikace Azure App Service klonování pomocí Powershellu
Verze prostředí Azure PowerShell verze 1.1.0 se přidala nová možnost pro `New-AzureRMWebApp` , který umožňuje klonování existující aplikace služby App Service do nově vytvořené aplikace v jiné oblasti nebo ve stejné oblasti. Tato možnost umožňuje zákazníkům rychle a snadno nasadit počet aplikací, které v různých oblastech.

Klonování aplikace je momentálně podporována pouze pro plány služby app service úrovně premium. Nová funkce používá stejná omezení jako funkce App Service – zálohování, naleznete v tématu [zálohování aplikace ve službě Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonování existující aplikace
Scénář: Existující aplikace v oblasti střed USA – jih a chcete naklonovat obsah do nové aplikace v oblasti střed USA – sever. To lze provést pomocí Azure Resource Manageru verze rutiny Powershellu pro vytvoření nové aplikace s `-SourceWebApp` možnost.

Znalost, název skupiny prostředků, který obsahuje zdrojovou aplikaci, slouží následující příkaz Powershellu získat informace o aplikaci zdroje (v tomto případě s názvem `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Pokud chcete vytvořit nový plán služby App Service, můžete použít `New-AzureRmAppServicePlan` příkaz jako v následujícím příkladu

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Použití `New-AzureRmWebApp` příkazu, můžete vytvořit novou aplikaci v oblasti střed USA – sever a spojit je k vrstvě premium existující plán služby App Service. Kromě toho můžete použít stejnou skupinu prostředků jako zdrojová aplikace nebo definovat novou skupinu prostředků, jak je znázorněno v následujícím příkazu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Klonování existující aplikace včetně všech přidružených nasazovacích slotů, budete muset použít `IncludeSourceWebAppSlots` parametru. Následující příkaz prostředí PowerShell ukazuje použití tohoto parametru se `New-AzureRmWebApp` příkaz:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Klonování existující aplikace v rámci stejné oblasti, je potřeba vytvořit novou skupinu prostředků a novou službu app service plánování ve stejné oblasti a pak pomocí následujícího příkazu Powershellu naklonujte aplikaci:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonování existující aplikace do služby App Service Environment
Scénář: Existující aplikace v oblasti střed USA – jih a chcete naklonovat obsah do nové aplikace do existující App Service Environment (ASE).

Znalost, název skupiny prostředků, který obsahuje zdrojovou aplikaci, slouží následující příkaz Powershellu získat informace o aplikaci zdroje (v tomto případě s názvem `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Vědět, že služba ASE název a název skupiny prostředků, které patří služby ASE, můžete vytvořit novou aplikaci do existující služby ASE, jak je znázorněno v následujícím příkazu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametr je povinný. z důvodu starší verze, ale je ignorována při vytváření aplikace ve službě ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klonování existujícího slotu aplikace
Scénář: Chcete naklonovat existující slot nasazení aplikace do buď nové aplikace nebo nový slot. Nová aplikace může být ve stejné oblasti jako původní slotu aplikace nebo v jiné oblasti.

Znalost, název skupiny prostředků, která obsahuje zdrojová aplikace, slouží následující příkaz Powershellu k získání informací o zdrojový slot app (v tomto případě s názvem `source-appslot`) spojený s `source-app`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Následující příkaz ukazuje vytvoření klonu byla zdrojová aplikace do nové aplikace:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurace Traffic Manageru při klonování aplikace
Vytvoření aplikace ve více oblastech a konfigurace Azure Traffic Manageru směrovat provoz na těchto aplikací je scénář odstranění důležitý k zajištění vysoce dostupných aplikací zákazníků. Při klonování existující aplikace, máte možnost se připojit k existující nebo nový profil služby traffic manager obě aplikace. Pouze Azure Resource Manageru verze Traffic Manageru se nepodporuje.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Vytvoření nového profilu Traffic Manageru při klonování aplikace
Scénář: Chcete klonování aplikace do jiné oblasti, při konfiguraci Azure Resource Manageru profil služby traffic manager, která obsahuje obě aplikace. Následující příkaz ukazuje vytvoření klonu byla zdrojová aplikace do nové aplikace při konfiguraci nového profilu Traffic Manageru:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Přidání nových klonovat aplikaci tak, aby existující profil Traffic Manageru
Scénář: Již máte profil traffic Manageru Azure Resource Manageru a chcete přidat obě aplikace jako koncové body. K tomu, je nejprve nutné sestavit existující provozu ID profilu správce. Budete potřebovat ID předplatného, název skupiny prostředků a stávající název profilu traffic Manageru.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po s ID traffic Manager, následující příkaz ukazuje vytvoření klonu byla zdrojová aplikace do nové aplikace při jejich přidání do existujícího profilu Traffic Manageru:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuální omezení
Tady je známé omezení klonování aplikací:

* Nastavení automatického škálování se neklonuje
* Nastavení plánu zálohování se neklonuje
* Nastavení virtuální sítě se neklonuje
* App Insights nejsou nastaveny automaticky na cílovou aplikaci
* Snadné nastavení vícefaktorového ověřování se neklonuje
* Se neklonuje rozšíření kudu
* Pravidla tiP se neklonuje
* Databáze obsahu není klonovat.
* Odchozí IP adresy změní, pokud se klonování na jednotce škálování různých

### <a name="references"></a>Odkazy
* [Klonování služby App Service](app-service-web-app-cloning.md)
* [Zálohování aplikace ve službě Azure App Service](manage-backup.md)
* [Podpora Azure Resource Manageru pro Azure Traffic Manager ve verzi Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Úvod do prostředí App Service](environment/intro.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)

