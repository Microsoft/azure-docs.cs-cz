---
title: Klonování aplikací s využitím PowerShellu
description: Přečtěte si, jak naklonovat aplikaci App Service do nové aplikace pomocí PowerShellu. Jsou popsány různé scénáře klonování, včetně integrace Traffic Manageru.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255189"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Klonování aplikací služby Azure pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

S vydáním Microsoft Azure PowerShell verze 1.1.0, nová `New-AzWebApp` možnost byla přidána, která umožňuje klonovat existující aplikaci služby App Service do nově vytvořené aplikace v jiné oblasti nebo ve stejné oblasti. Tato možnost umožňuje zákazníkům rychle a snadno nasadit několik aplikací v různých oblastech.

Klonování aplikací je podporováno pro plány standardu, premium, premium v2 a izolované služby aplikace. Nová funkce používá stejná omezení jako funkce Zálohování služby App Service, viz [Zálohování aplikace ve službě Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonování existující aplikace
Scénář: Existující aplikace v oblasti USA – střed a chcete naklonovat obsah do nové aplikace v oblasti USA – střed v severním centru USA. To lze provést pomocí verze Azure Resource Manager rutiny PowerShell vytvořit novou `-SourceWebApp` aplikaci s možností.

Znát název skupiny prostředků, který obsahuje zdrojovou aplikaci, můžete použít následující příkaz Prostředí PowerShell získat informace zdrojové aplikace (v tomto případě s názvem `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Chcete-li vytvořit nový plán služby App Service, můžete použít `New-AzAppServicePlan` příkaz jako v následujícím příkladu

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Pomocí `New-AzWebApp` příkazu můžete vytvořit novou aplikaci v oblasti USA – sever – střed a spojit ji s existujícím plánem služby App Service. Kromě toho můžete použít stejnou skupinu prostředků jako zdrojovou aplikaci nebo definovat novou skupinu prostředků, jak je znázorněno v následujícím příkazu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Chcete-li klonovat existující aplikaci včetně všech přidružených `IncludeSourceWebAppSlots` slotů nasazení, musíte použít parametr.  Všimněte `IncludeSourceWebAppSlots` si, že parametr je podporován pouze pro klonování celé aplikace včetně všech jeho slotů. Následující příkaz prostředí PowerShell ukazuje použití tohoto `New-AzWebApp` parametru pomocí příkazu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Chcete-li klonovat existující aplikaci ve stejné oblasti, musíte vytvořit novou skupinu prostředků a nový plán služby app service ve stejné oblasti a pak pomocí následujícího příkazu PowerShell uklonovat aplikaci:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonování existující aplikace do prostředí služby App Service
Scénář: Existující aplikace v oblasti USA – –střed a chcete naklonovat obsah do nové aplikace do existujícího prostředí služby App Service (ASE).

Znát název skupiny prostředků, který obsahuje zdrojovou aplikaci, můžete použít následující příkaz Prostředí PowerShell získat informace zdrojové aplikace (v tomto případě s názvem `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znát název ase a název skupiny prostředků, které patří ase, můžete vytvořit novou aplikaci v existující ase, jak je znázorněno v následujícím příkazu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

Parametr `Location` je vyžadován z důvodu starší verze, ale je ignorována při vytváření aplikace ve správě ase. 

## <a name="cloning-an-existing-app-slot"></a>Klonování existujícího slotu aplikace
Scénář: Chcete naklonovat existující slot nasazení aplikace buď do nové aplikace nebo nového slotu. Nová aplikace může být ve stejné oblasti jako původní patice aplikace nebo v jiné oblasti.

Znát název skupiny prostředků, který obsahuje zdrojovou aplikaci, můžete použít následující příkaz Prostředí PowerShell `source-appslot`získat `source-app`informace o zdrojové patice aplikace (v tomto případě s názvem) vázané na :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Následující příkaz ukazuje vytvoření klonu zdrojové aplikace do nové aplikace:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurace Traffic Manageru při klonování aplikace
Vytváření aplikací pro více oblastí a konfigurace Azure Traffic Manageru pro směrování provozu do všech těchto aplikací je důležitý scénář, který zajistí, že aplikace zákazníků budou vysoce dostupné. Při klonování existující aplikace máte možnost připojit obě aplikace k novému profilu traffic manageru nebo k existujícímu profilu. Je podporována jenom verze Správce prostředků Azure.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Vytvoření nového profilu Traffic Manageru při klonování aplikace
Scénář: Chcete klonovat aplikaci do jiné oblasti při konfiguraci profilu správce provozu Azure Resource Manager, který zahrnuje obě aplikace. Následující příkaz ukazuje vytvoření klonu zdrojové aplikace do nové aplikace při konfiguraci nového profilu Traffic Manageru:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Přidání nové klonované aplikace do existujícího profilu Traffic Manageru
Scénář: Už máte profil správce provozu Azure Resource Manager a chcete přidat obě aplikace jako koncové body. Chcete-li tak učinit, musíte nejprve sestavit existující ID profilu správce provozu. Potřebujete ID předplatného, název skupiny prostředků a název existujícího profilu správce provozu.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po vytvoření ID řízení provozu ukazuje následující příkaz vytvoření klonu zdrojové aplikace do nové aplikace při jejich přidání do existujícího profilu Traffic Manageru:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuální omezení
Zde jsou známá omezení klonování aplikací:

* Automatické nastavení měřítka není klonováno
* Nastavení plánu zálohování nejsou klonována
* Nastavení virtuální sítě nejsou klonována
* Přehledy aplikací se v cílové aplikaci nenastavují automaticky
* Nastavení snadného uštovu není klonováno
* Kudu Extension nejsou klonovány
* Pravidla TiP nejsou klonována.
* Obsah databáze není klonován.
* Odchozí IP adresy se změní při klonování do jiné jednotky škálování
* Není k dispozici pro linuxové aplikace

### <a name="references"></a>Odkazy
* [Klonování služby App Service](app-service-web-app-cloning.md)
* [Zálohování aplikace ve službě Azure App Service](manage-backup.md)
* [Podpora Azure Resource Managerpro Azure Traffic Manager Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Úvod do prostředí App Service](environment/intro.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md)

