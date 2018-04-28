---
title: Povolit Azure Active Directory Domain Services pomocí prostředí PowerShell | Microsoft Docs
description: Povolit Azure Active Directory Domain Services pomocí prostředí PowerShell
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: 22be243b9da5afd93744ca9410b63a8ec936088a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Povolit Azure Active Directory Domain Services pomocí prostředí PowerShell
Tento článek ukazuje, jak povolit doménové služby Azure Active Directory (AD) pomocí prostředí PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Úloha 1: Instalace požadované moduly prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD PowerShell
Postupujte podle pokynů v článku do [instalace modulu Azure AD PowerShell a připojte se k Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Postupujte podle pokynů v článku do [instalace modulu Azure PowerShell a připojte se k předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Úloha 2: Vytvoření objektu požadované služby v adresáři služby Azure AD
Zadejte následující příkaz prostředí PowerShell k vytvoření objektu služby požadované pro Azure AD Domain Services v adresáři služby Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Úloha 3: Vytvoření a konfigurace skupiny 'Administrators AAD řadič domény.
Dalším krokem je vytvoření skupiny správců, který se použije delegovat úlohy správy na vaší spravované domény.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Teď, když jste vytvořili skupinu, do skupiny přidat několik uživatelů.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Úloha 4: Registrace poskytovatele prostředků Azure AD Domain Services
Zadejte následující příkaz prostředí PowerShell k registraci poskytovatele prostředků pro Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Úloha 5: Vytvoření skupiny prostředků
Zadejte následující příkaz prostředí PowerShell k vytvoření skupiny prostředků:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

V této skupině prostředků, můžete vytvořit virtuální síť a spravované doméně služby Azure AD Domain Services.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Úloha 6: Vytvoření a konfigurace virtuální sítě
Teď vytvořte virtuální síť, ve kterém povolíte Azure AD Domain Services. Ujistěte se, vytvořit vyhrazený podsíť pro Azure AD Domain Services. Nenasazujte zatížení virtuálních počítačů do této vyhrazené podsítě.

Zadejte následující příkazy prostředí PowerShell vytvořit virtuální síť s podsítí vyhrazené pro Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Úloha 7: Zřizování spravované doméně služby Azure AD Domain Services
Zadejte následující příkaz prostředí PowerShell pro povolení služby Azure AD Domain Services pro svůj adresář:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Nezapomeňte na další konfigurační kroky po zřízení vaší spravované domény.**
> Po zřízení vaší spravované domény, musíte stále dokončit následující úlohy:
> * **[Aktualizace nastavení DNS](active-directory-ds-getting-started-dns.md)**  virtuální sítě, virtuální počítače můžete najít spravované domény pro připojení k doméně nebo ověřování.
* **[Povolení synchronizace hesel do služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)**, takže koncovým uživatelům se můžete přihlásit k spravované doméně pomocí své podnikové přihlašovací údaje.
>


## <a name="powershell-script"></a>Skript PowerShellu
Skript prostředí PowerShell použít k plnění všech úloh, které jsou uvedené v tomto článku je níže. Zkopírujte skript a uložte jej do souboru s příponou, .ps1'. Spusťte skript v prostředí PowerShell nebo pomocí PowerShell integrovaném skriptovacím prostředí (ISE).

> [!NOTE]
> **Oprávnění potřebná ke spuštění tohoto skriptu** , aby Azure AD Domain Services, musíte být globálním správcem adresáře Azure AD. Kromě toho je nutné alespoň oprávnění "Přispěvatel" ve virtuální síti, ve kterém povolení služby Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Nezapomeňte na další konfigurační kroky po zřízení vaší spravované domény.**
> Po zřízení vaší spravované domény, musíte stále dokončit následující úlohy:
> * Aktualizace nastavení DNS pro virtuální síť, virtuální počítače můžete najít spravované domény pro připojení k doméně nebo ověřování.
* Povolení synchronizace hesel do služby Azure AD Domain Services, aby koncoví uživatelé můžou přihlásit k spravované doméně pomocí podnikových přihlašovacích.
>

## <a name="next-steps"></a>Další postup
Po vytvoření vaší spravované domény proveďte následující úlohy konfigurace, abyste mohli používat spravované doméně:

* [Aktualizovat nastavení serveru DNS virtuální sítě tak, aby odkazoval na vaší spravované domény](active-directory-ds-getting-started-dns.md)
* [Povolení synchronizace hesel do vaší spravované domény](active-directory-ds-getting-started-password-sync.md)
