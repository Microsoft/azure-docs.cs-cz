---
title: Klonování aplikací s využitím prostředí PowerShell – Azure App Service
description: Zjistěte, jak klonovat vaše webové aplikace do nové webové aplikace pomocí Powershellu.
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
ms.openlocfilehash: 87bae4db64c0a22790b7f52f919601f82aa548df
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261863"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplikace Azure App Service klonování pomocí Powershellu
Verze prostředí Azure PowerShell verze 1.1.0 se přidala nová možnost pro `New-AzureRMWebApp` , který umožňuje naklonovat existující webové aplikace do nově vytvořené aplikace v jiné oblasti nebo ve stejné oblasti. Tato možnost umožňuje zákazníkům rychle a snadno nasadit počet aplikací, které v různých oblastech.

Klonování aplikace je momentálně podporována pouze pro plány služby app service úrovně premium. Nová funkce používá stejná omezení jako funkce zálohy Web Apps, najdete v článku [zálohování webové aplikace ve službě Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Klonování existující aplikace
Scénář: Existující webové aplikace v oblasti střed USA – jih a chcete naklonovat obsah do nové webové aplikace v oblasti střed USA – sever. To lze provést pomocí Azure Resource Manageru verze rutiny prostředí PowerShell k vytvoření nové webové aplikace s `-SourceWebApp` možnost.

Znalost, název skupiny prostředků, který obsahuje zdrojovou webovou aplikaci, slouží následující příkaz Powershellu získat informace o zdrojové webové aplikace (v tomto případě s názvem `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Pokud chcete vytvořit nový plán služby App Service, můžete použít `New-AzureRmAppServicePlan` příkaz jako v následujícím příkladu

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Použití `New-AzureRmWebApp` příkazu, můžete vytvořit novou webovou aplikaci v oblasti střed USA – sever a spojit je k vrstvě premium existující plán služby App Service. Kromě toho můžete použít stejnou skupinu prostředků jako zdrojovou webovou aplikaci nebo definovat novou skupinu prostředků, jak je znázorněno v následujícím příkazu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Klonování existující webovou aplikaci, včetně všech přidružených nasazovacích slotů, budete muset použít `IncludeSourceWebAppSlots` parametru. Následující příkaz prostředí PowerShell ukazuje použití tohoto parametru se `New-AzureRmWebApp` příkaz:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Klonování existující webové aplikace v rámci stejné oblasti, je potřeba vytvořit novou skupinu prostředků a novou službu app service plánování ve stejné oblasti a pak pomocí následujícího příkazu Powershellu naklonujte webové aplikace

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klonování existující aplikace do služby App Service Environment
Scénář: Existující webové aplikace v oblasti střed USA – jih a chcete naklonovat obsah do nové webové aplikace do existující App Service Environment (ASE).

Znalost, název skupiny prostředků, který obsahuje zdrojovou webovou aplikaci, slouží následující příkaz Powershellu získat informace o zdrojové webové aplikace (v tomto případě s názvem `source-webapp`):

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Vědět, že služba ASE název a název skupiny prostředků, které patří služby ASE, můžete vytvořit novou webovou aplikaci v existující služby ASE, jak je znázorněno v následujícím příkazu:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` Parametr je povinný. z důvodu starší verze, ale je ignorována při vytváření aplikace ve službě ASE. 

## <a name="cloning-an-existing-app-slot"></a>Klonování existujícího slotu aplikace
Scénář: Chcete naklonovat existující Web App Slot buď na novou webovou aplikaci nebo nový slot webové aplikace. Nová webová aplikace může být ve stejné oblasti jako původní slot webové aplikace nebo v jiné oblasti.

Znalost, název skupiny prostředků, který obsahuje zdrojovou webovou aplikaci, slouží následující příkaz Powershellu získat informace o slot serveru zdrojové webové aplikace (v tomto případě s názvem `source-webappslot`) vázané na webovou aplikaci `source-webapp`:

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

Následující příkaz ukazuje vytvoření klonu zdrojovou webovou aplikaci do nové webové aplikace:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Konfigurace Traffic Manageru při klonování aplikace
Vytváření aplikací pro web ve více oblastech a konfigurace Azure Traffic Manageru směrovat provoz na těchto webových aplikací, je scénář odstranění důležitý k zajištění vysoce dostupných aplikací zákazníků. Při klonování existující webové aplikace, máte možnost se připojit i webové aplikace na nový profil služby traffic manager nebo některý z existujících. Pouze Azure Resource Manageru verze Traffic Manageru se nepodporuje.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Vytvoření nového profilu Traffic Manageru při klonování aplikace
Scénář: Chcete naklonovat webové aplikace do jiné oblasti, při konfiguraci Azure Resource Manageru profil služby traffic manager, která zahrnuje jak webové aplikace. Následující příkaz ukazuje vytvoření klonu zdrojovou webovou aplikaci do nové webové aplikace při konfiguraci nového profilu Traffic Manageru:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Přidání nových klonovat webové aplikace na existující profil Traffic Manageru
Scénář: Již máte profil traffic Manageru Azure Resource Manageru a chcete přidat i webových aplikací jako koncových bodů. K tomu, je nejprve nutné sestavit existující provozu ID profilu správce. Budete potřebovat ID předplatného, název skupiny prostředků a stávající název profilu traffic Manageru.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Po s ID traffic Manager, následující příkaz ukazuje vytvoření klonu zdrojovou webovou aplikaci do nové webové aplikace při jejich přidání do existujícího profilu Traffic Manageru:

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Aktuální omezení
Tato funkce je aktuálně ve verzi preview a jsou přidány nové funkce v čase. Tady je známé omezení v aktuální verzi klonování aplikací:

* Nastavení automatického škálování se neklonuje
* Nastavení plánu zálohování se neklonuje
* Nastavení virtuální sítě se neklonuje
* App Insights nejsou nastaveny automaticky na cílové webové aplikace
* Snadné nastavení vícefaktorového ověřování se neklonuje
* Se neklonuje rozšíření kudu
* Pravidla tiP se neklonuje
* Databáze obsahu není klonovat.
* Odchozí IP adresy změní, pokud se klonování na jednotce škálování různých

### <a name="references"></a>Odkazy
* [Klonování webových aplikací](app-service-web-app-cloning.md)
* [Zálohování webové aplikace ve službě Azure App Service](web-sites-backup.md)
* [Podpora Azure Resource Manageru pro Azure Traffic Manager ve verzi Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Úvod do prostředí App Service](environment/intro.md)
* [Použití Azure PowerShellu s Azure Resource Managerem](../azure-resource-manager/powershell-azure-resource-manager.md)

