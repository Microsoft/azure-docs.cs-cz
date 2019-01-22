---
title: Povolit Azure Active Directory Domain Services pomocí Powershellu | Dokumentace Microsoftu
description: Povolit Azure Active Directory Domain Services pomocí Powershellu
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2017
ms.author: ergreenl
ms.openlocfilehash: ce6d3815e562e5ada2a70ebaff04f9abc20b714c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426535"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Povolit Azure Active Directory Domain Services pomocí Powershellu
Tento článek popisuje, jak povolit Azure Active Directory (AD) Domain Services pomocí Powershellu.

## <a name="task-1-install-the-required-powershell-modules"></a>Úloha 1: Nainstalujte požadované moduly prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure AD PowerShell a připojte se k Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure PowerShell a připojte se ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Úloha 2: Vytvoření instančního objektu požadovaná služba v adresáři služby Azure AD
Zadejte následující příkaz prostředí PowerShell k vytvoření instančního objektu služby, vyžaduje se pro Azure AD Domain Services v adresáři služby Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Úloha 3: Vytvořte a nakonfigurujte skupinu "Správci AAD DC.
Dalším krokem je vytvoření skupiny správců, který se použije k delegování úkolů správy ve vaší spravované doméně.
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

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Úloha 4: Registrace poskytovatele prostředků služby Azure AD Domain Services
Zadejte následující příkaz Powershellu zaregistrujte poskytovatele prostředků služby Azure AD Domain Services:
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

V této skupině prostředků můžete vytvořit virtuální síť a spravované doméně služby Azure AD Domain Services.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Krok 6: Vytvoření a konfigurace virtuální sítě
Teď vytvořte virtuální síť, ve kterém povolíte Azure AD Domain Services. Ujistěte se, že vytvoříte vyhrazenou podsíť pro službu Azure AD Domain Services. Nenasazujte do této vyhrazenou podsíť virtuálních počítačů pracovního vytížení.

Zadejte následující příkazy Powershellu k vytvoření virtuální sítě s vyhrazenou podsíť pro službu Azure AD Domain Services.

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


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Krok 7: Zřízení spravované doméně služby Azure AD Domain Services
Zadejte následující příkaz Powershellu k povolení služby Azure AD Domain Services pro svůj adresář:

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
> **Nezapomeňte na další konfigurační kroky po zřízení spravované domény.**
> Po zřízení spravované domény, je stále potřeba provést následující úlohy:
> * **[Aktualizace nastavení DNS](active-directory-ds-getting-started-dns.md)**  pro virtuální síť, virtuální počítače můžete najít spravované domény pro připojení k doméně nebo ověřování.
* **[Povolení synchronizace hesel do služby Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)**, aby koncoví uživatelé můžou přihlásit ke spravované doméně pomocí podnikových přihlašovacích.
>


## <a name="powershell-script"></a>Skript PowerShellu
Skript prostředí PowerShell použít k plnění všech úloh, které jsou uvedené v tomto článku je níže. Zkopírujte skript a uložte ho do souboru s příponou ".ps1". Spusťte skript prostředí PowerShell nebo s použitím prostředí PowerShell integrovaném skriptovacím prostředí (ISE).

> [!NOTE]
> **Oprávnění potřebná ke spuštění tohoto skriptu** k povolení služby Azure AD Domain Services, musíte být globálním správcem adresáře Azure AD. Kromě toho budete potřebovat alespoň oprávnění "Přispěvatel" ve službě virtual network, ve kterém povolení služby Azure AD Domain Services.
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
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

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
> **Nezapomeňte na další konfigurační kroky po zřízení spravované domény.**
> Po zřízení spravované domény, je stále potřeba provést následující úlohy:
> * Aktualizace nastavení DNS pro virtuální síť, virtuální počítače můžete najít spravované domény pro připojení k doméně nebo ověřování.
* Povolení synchronizace hesel do služby Azure AD Domain Services tak, že koncoví uživatelé můžou přihlásit ke spravované doméně pomocí podnikových přihlašovacích.
>

## <a name="next-steps"></a>Další postup
Po vytvoření vaší spravované domény proveďte následující úlohy konfigurace, abyste mohli používat spravovanou doménu:

* [Aktualizace nastavení serveru DNS pro virtuální síť tak, aby odkazoval do spravované domény](active-directory-ds-getting-started-dns.md)
* [Povolení synchronizace hesel do spravované domény](active-directory-ds-getting-started-password-sync.md)
