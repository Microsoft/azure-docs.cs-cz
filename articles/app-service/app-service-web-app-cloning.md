---
title: Klonování aplikací s využitím PowerShellu
description: Naučte se naklonovat App Service aplikaci do nové aplikace pomocí prostředí PowerShell. Zahrnuje celou řadu scénářů klonování, včetně integrace Traffic Manager.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e3ae342e7cbd8a9c2e126de7666d07f0664be407
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573638"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service klonování aplikace pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

S vydáním Microsoft Azure PowerShell verze 1.1.0 byla přidána nová možnost `New-AzWebApp` , která umožňuje klonovat existující aplikaci App Service na nově vytvořenou aplikaci v jiné oblasti nebo ve stejné oblasti. Tato možnost umožňuje zákazníkům rychle a snadno nasadit řadu aplikací v různých oblastech.

Klonování aplikací se podporuje pro plány služby App Service úrovně Standard, Premium a Premium v2 a Isolated. Nová funkce používá stejná omezení jako funkce App Service Backup, viz [zálohování aplikace v Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Klonování existující aplikace
Scénář: existující aplikace v Střed USA – jih oblasti a chcete klonovat obsah do nové aplikace v Střed USA – sever oblasti. Dá se dosáhnout pomocí Azure Resource Manager verze rutiny prostředí PowerShell pro vytvoření nové aplikace s `-SourceWebApp` možností.

Znalost názvu skupiny prostředků, která obsahuje zdrojovou aplikaci, můžete k získání informací o zdrojové aplikaci použít následující příkaz PowerShellu (v tomto případě s názvem `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Pokud chcete vytvořit nový plán App Service, můžete použít `New-AzAppServicePlan` příkaz jako v následujícím příkladu.

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Pomocí `New-AzWebApp` příkazu můžete vytvořit novou aplikaci v oblasti střed USA – sever a spojit ji s existujícím plánem App Service. Kromě toho můžete použít stejnou skupinu prostředků jako zdrojovou aplikaci nebo definovat novou skupinu prostředků, jak je znázorněno v následujícím příkazu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Pokud chcete klonovat existující aplikaci včetně všech přidružených slotů nasazení, musíte použít `IncludeSourceWebAppSlots` parametr.  Všimněte si, že `IncludeSourceWebAppSlots` parametr je podporován pouze pro klonování celé aplikace včetně všech slotů. Následující příkaz prostředí PowerShell ukazuje použití tohoto parametru s `New-AzWebApp` příkazem:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

K naklonování existující aplikace v rámci stejné oblasti je potřeba vytvořit novou skupinu prostředků a nový plán služby App Service ve stejné oblasti a pomocí následujícího příkazu PowerShellu naklonovat aplikaci:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonování existující aplikace do App Service Environment
Scénář: existující aplikace v Střed USA – jih oblasti a chcete klonovat obsah do nové aplikace do existující App Service Environment (pomocného mechanismu).

Znalost názvu skupiny prostředků, která obsahuje zdrojovou aplikaci, můžete k získání informací o zdrojové aplikaci použít následující příkaz PowerShellu (v tomto případě s názvem `source-webapp` ):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Znalost jména pomocného mechanismu a názvu skupiny prostředků, do které patří přihlášený, můžete vytvořit novou aplikaci v existujícím pomocném uživatelském rozhraní, jak je znázorněno v následujícím příkazu:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location`Parametr je vyžadován z důvodu starší verze, ale při vytváření aplikace v POmocném mechanismu řízení se ignoruje. 

## <a name="cloning-an-existing-app-slot"></a>Klonování existujícího slotu aplikace
Scénář: chcete naklonovat existující slot nasazení aplikace do nové aplikace nebo nové přihrádky. Nová aplikace může být ve stejné oblasti jako původní pozice aplikace nebo v jiné oblasti.

Znalost názvu skupiny prostředků, která obsahuje zdrojovou aplikaci, můžete k získání informací o pozici zdrojové aplikace (v tomto případě s názvem) použít následující příkaz prostředí PowerShell `source-appslot` `source-app` :

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Následující příkaz ukazuje vytvoření klonu zdrojové aplikace do nové aplikace:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurace Traffic Manager při klonování aplikace
Vytváření aplikací pro více oblastí a konfigurace služby Azure Traffic Manager pro směrování provozu do všech těchto aplikací je důležitým scénářem, který zajistí vysokou dostupnost aplikací pro zákazníky. Při klonování existující aplikace máte možnost připojit obě aplikace buď k novému profilu Traffic Manageru, nebo k existujícímu. Je podporována pouze Azure Resource Manager verze Traffic Manager.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Vytvoření nového profilu Traffic Manager během klonování aplikace
Scénář: chcete naklonovat aplikaci do jiné oblasti během konfigurace Azure Resource Manager profilu Traffic Manageru, který zahrnuje obě aplikace. Následující příkaz ukazuje vytvoření klonu zdrojové aplikace do nové aplikace při konfiguraci nového profilu Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Přidání nové klonované aplikace do existujícího profilu Traffic Manager
Scénář: už máte profil Azure Resource Manager Traffic Manageru a chcete obě aplikace přidat jako koncové body. K tomu je třeba nejprve sestavit ID profilu Traffic Manageru. Budete potřebovat ID předplatného, název skupiny prostředků a stávající název profilu Traffic Manageru.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po použití ID Traffic Manageru následující příkaz ukazuje, jak vytvořit klon zdrojové aplikace do nové aplikace a přidat je do existujícího profilu Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Pokud se zobrazí chybová zpráva oznamující, že se nedaří ověřit SSL u názvu hostitele Traffic Manageru, doporučujeme vám při provádění operace klonování použít atribut-IgnoreCustomHostNames v rutině PowerShellu nebo jinak použít portál.

## <a name="current-restrictions"></a>Aktuální omezení
Tady jsou známá omezení klonování aplikací:

* Nastavení automatického škálování není naklonované.
* Nastavení plánu zálohování nejsou naklonovaná.
* Nastavení virtuální sítě nejsou naklonovaná.
* App Insights se v cílové aplikaci automaticky nenastavuje.
* Nastavení jednoduchého ověřování nejsou naklonovaná.
* Rozšíření Kudu nejsou naklonovaná.
* Pravidla tipů nejsou naklonovaná.
* Obsah databáze není klonován.
* Změny odchozích IP adres při klonování do jiné jednotky škálování
* Není k dispozici pro aplikace pro Linux
* Spravované identity nejsou naklonovány.

### <a name="references"></a>Reference
* [App Service klonování](app-service-web-app-cloning.md)
* [Zálohování aplikace v Azure App Service](manage-backup.md)
* [Podpora Azure Resource Manager pro Azure Traffic Manager Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Úvod do prostředí App Service](environment/intro.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md)

