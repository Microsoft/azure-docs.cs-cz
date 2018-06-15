---
title: Webové aplikace klonování pomocí prostředí PowerShell
description: Zjistěte, jak vytvořit kopii vaší webové aplikace do nové webové aplikace pomocí prostředí PowerShell.
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
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2018
ms.locfileid: "27867466"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplikační služby Azure klonování pomocí prostředí PowerShell
Verze služby Microsoft Azure PowerShell verze 1.1.0, byla přidána nová možnost pro `New-AzureRMWebApp` , která umožňuje klonovat stávající webovou aplikaci do nově vytvořené aplikace v jiné oblasti nebo ve stejné oblasti. Tato možnost umožňuje zákazníkům snadno a rychle nasadit počet aplikací v různých oblastech.

Klonování aplikace je momentálně podporovaná jenom pro plány služby app úrovně premium. Nová funkce používá stejná omezení jako funkci zálohování webové aplikace, najdete v části [zálohování webové aplikace ve službě Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Klonování existující aplikace
Scénář: Stávající webovou aplikaci v oblasti jihu USA a chcete klonovat obsah do nové webové aplikace v oblasti severní jihu USA. Dosáhnete pomocí Azure Resource Manager verzi rutiny prostředí PowerShell k vytvoření nové webové aplikace pomocí `-SourceWebApp` možnost.

Zároveň budete vědět, název skupiny prostředků, který obsahuje zdrojové webové aplikace, můžete použít příkaz prostředí PowerShell následující získat informace o zdrojové webové aplikace (v takovém případě s názvem `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Pokud chcete vytvořit, nový plán služby App Service, můžete použít `New-AzureRmAppServicePlan` jako v následujícím příkladu příkaz

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Pomocí `New-AzureRmWebApp` příkazů, můžete vytvořit novou webovou aplikaci v oblasti severní jihu USA a tie existující úrovně premium plán služby App Service. Kromě toho můžete použít stejné skupině prostředků jako zdroj webové aplikace, nebo definovat novou skupinu prostředků, jak je uvedeno v následujícím příkazu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Klonovat existující webovou aplikaci, včetně všech přidružených nasazovací sloty, budete muset použít `IncludeSourceWebAppSlots` parametr. Následující příkaz prostředí PowerShell demonstruje použití tohoto parametru se `New-AzureRmWebApp` příkaz:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Klonovat stávající webovou aplikaci v rámci stejné oblasti, budete muset vytvořit novou skupinu prostředků a nové služby app service plánování ve stejné oblasti a potom použijte následující příkaz prostředí PowerShell klonovat webové aplikace

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonování stávající aplikace pro služby App Service Environment
Scénář: Stávající webovou aplikaci v oblasti jihu USA a chcete klonovat obsah do nové webové aplikace do existující aplikace služby prostředí (App Service Environment).

Zároveň budete vědět, název skupiny prostředků, který obsahuje zdrojové webové aplikace, můžete použít příkaz prostředí PowerShell následující získat informace o zdrojové webové aplikace (v takovém případě s názvem `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znalost App Service Environment název a název skupiny prostředků, které patří App Service Environment, můžete vytvořit novou webovou aplikaci v existující App Service Environment, jak je znázorněno v následujícím příkazu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametr je vyžadován z důvodu starší verze, ale při vytváření aplikace v App Service Environment je ignorována. 

## <a name="cloning-an-existing-app-slot"></a>Klonování existujícího slotu aplikace
Scénář: Chcete clone existující Slot webové aplikace do buď nové webové aplikace nebo nový slot webové aplikace. Nové webové aplikace může být ve stejné oblasti jako původní slot webové aplikace nebo v jiné oblasti.

Zároveň budete vědět, název skupiny prostředků, který obsahuje zdrojové webové aplikace, můžete použít příkaz prostředí PowerShell následující získat informace o slot serveru zdrojové webové aplikace (v takovém případě s názvem `source-webappslot`) svázané do webové aplikace `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Následující příkaz ukazuje vytvoření kopie zdrojové webové aplikace do nové webové aplikace:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurace Traffic Manager během klonování aplikace
Vytvoření více oblast webové aplikace a konfigurace Azure Traffic Manager pro směrování provozu pro tyto webové aplikace, je důležité scénáři a pak zajistěte, aby aplikace zákazníků vysoce dostupný. Při klonování stávající webovou aplikaci, máte možnost pro připojení k nové profil správce provozu nebo stávající obě webové aplikace. Pouze správce Azure Resource Manager verze služby Traffic Manager je podporována.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Vytvoření nového profilu Traffic Manageru během klonování aplikace
Scénář: Chcete klonovat webové aplikace do jiné oblasti, při konfiguraci profil správce provozu Azure Resource Manager, která zahrnuje jak webové aplikace. Následující příkaz ukazuje vytvoření kopie zdrojové webové aplikace do nové webové aplikace při konfiguraci nového profilu Traffic Manageru:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Přidání nových klonovat webovou aplikaci do existujícího profilu Správce provozu
Scénář: Už máte profil správce provozu správce prostředků Azure a chcete přidat i webové aplikace jako koncové body. V takovém případě musíte nejprve ke kompilaci stávajících přenosů dat ID profilu správce. Potřebujete ID předplatného, název skupiny prostředků a stávající název profilu Správce provozu.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po s ID Manager provoz, následující příkaz ukazuje vytvoření kopie zdrojové webové aplikace do nové webové aplikace při jejich přidáním do existující profil Traffic Manageru:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuální omezení
Tato funkce je aktuálně ve verzi preview a nové funkce jsou přidány v čase. Toto jsou známé omezení v aktuální verzi klonování aplikace:

* Nastavení automatického škálování nejsou klonovat.
* Nastavení plánu zálohování nejsou klonovat.
* Nastavení sítě VNET nejsou klonovat.
* Přehled aplikace nejsou automaticky na nastavit cílové webové aplikace
* Nejsou klonovat snadno nastavení ověřování
* Kudu rozšíření nejsou klonovat.
* TiP pravidla nejsou klonovat.
* Databáze obsahu není klonovat.
* Odchozí IP adresy mění Pokud klonování, aby jednotka různých škálování

### <a name="references"></a>Odkazy
* [Klonování webové aplikace](app-service-web-app-cloning.md)
* [Zálohování webové aplikace ve službě Azure App Service](web-sites-backup.md)
* [Azure Resource Manager podpora pro Azure Traffic Manager Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Úvod do prostředí App Service](environment/intro.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)

