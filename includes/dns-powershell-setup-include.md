---
title: soubor k zahrnutí pro PowerShell pro Azure DNS
description: soubor k zahrnutí pro PowerShell pro Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 388bea528b138d78b9ec23ceea295108306c61e9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613598"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Nastavení Azure PowerShellu pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Je potřeba nainstalovat nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

Pokud chcete používat privátní zóny (Public Preview), musíte se navíc ujistit, že máte následující moduly PowerShellu ve správné verzi. 
* AzureRM.Dns – [verze 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) nebo vyšší
* AzureRM.Network – [verze 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) nebo vyšší

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
Ve výstupu výše uvedených příkazů se musí zobrazit verze AzureRM.Dns 4.1.0 nebo vyšší a verze AzureRM.Network 5.4.0 nebo vyšší.  

V případě, že váš systém obsahuje starší verze, můžete nainstalovat nejnovější verzi Azure PowerShellu nebo stáhnout a nainstalovat výše uvedené moduly z Galerie prostředí PowerShell přes odkazy uvedené výše vedle verzí modulů. Pak je můžete nainstalovat pomocí níže uvedených příkazů. Vyžadují se oba moduly a oba jsou plně zpětně kompatibilní. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Další informace najdete v tématu [Použití PowerShellu s Resource Managerem](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Výběr předplatného
 
Zkontrolujte předplatná pro příslušný účet.

```powershell
Get-AzureRmSubscription
```

Zvolte předplatné Azure, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. Toto umístění slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Všechny prostředky DNS jsou ale globální, a ne místní, takže volba umístění skupiny prostředků nemá na Azure DNS žádný vliv.

Pokud používáte některou ze stávajících skupin prostředků, můžete tento krok přeskočit.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrace poskytovatele prostředků

Službu Azure DNS spravuje poskytovatel prostředků Microsoft.Network. Abyste mohli používat Azure DNS, je nutné zaregistrovat předplatné Azure k používání tohoto poskytovatele prostředků. Jedná se o jednorázovou operaci u každého odběru.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
