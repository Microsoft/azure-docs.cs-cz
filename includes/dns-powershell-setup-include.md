---
title: zahrnuje soubor pro prostředí PowerShell pro Azure DNS
description: zahrnuje soubor pro prostředí PowerShell pro Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: e96b312f03069256396261bd6efe2f2586cdadea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Nastavení prostředí Azure PowerShell pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Musíte nainstalovat nejnovější verzi rutin Powershellu pro Azure Resource Manager. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).

Kromě toho k používání zón privátní (Public Preview), musíte zajistit, že máte následující moduly Powershellu a verze. 
* AzureRM.Dns - [verze 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) nebo novější
* AzureRM.Network - [verze 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) nebo novější

Výše uvedené moduly můžete stáhnout z Galerie prostředí PowerShell pomocí výše uvedené odkazy vedle verze modulu. Pak je můžete instalovat pomocí následující příkazy. Oba moduly jsou vyžadována a jsou plně zpětně kompatibilní. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Další informace najdete v tématu [pomocí prostředí PowerShell s Resource Managerem](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
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