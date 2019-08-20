---
title: Povolení Azure Active Directory Domain Services pomocí prostředí PowerShell | Microsoft Docs
description: Povolení Azure Active Directory Domain Services pomocí prostředí PowerShell
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: iainfou
ms.openlocfilehash: c6572ab8bc2a10039f327233f983c2e822fba3b0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617220"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Povolení Azure Active Directory Domain Services pomocí prostředí PowerShell
V tomto článku se dozvíte, jak povolit službu Azure Active Directory (Active Directory Domain Services) pomocí prostředí PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Úloha 1: Instalace požadovaných modulů prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD PowerShellu
Podle pokynů v článku [nainstalujte modul Azure AD PowerShell a připojte se ke službě Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Podle pokynů v článku [nainstalujte modul Azure PowerShell a připojte se k předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Úloha 2: Vytvoření požadovaného instančního objektu ve vašem adresáři služby Azure AD
Zadejte následující příkaz prostředí PowerShell pro vytvoření instančního objektu, který je požadován pro Azure AD Domain Services v adresáři služby Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Úloha 3: Vytvoření a konfigurace skupiny AAD DC Administrators
Dalším úkolem je vytvořit skupinu správců, která bude sloužit k delegování úloh správy ve spravované doméně.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Teď, když jste vytvořili skupinu, přidejte do skupiny několik uživatelů.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Úloha 4: Registrace poskytovatele prostředků Azure AD Domain Services
Zadáním následujícího příkazu PowerShellu Zaregistrujte poskytovatele prostředků pro Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Úloha 5: Vytvoření skupiny prostředků
Zadáním následujícího příkazu PowerShellu vytvořte skupinu prostředků:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

V této skupině prostředků můžete vytvořit virtuální síť a Azure AD Domain Services spravovanou doménu.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Úloha 6: Vytvoření a konfigurace virtuální sítě
Nyní vytvořte virtuální síť, ve které povolíte Azure AD Domain Services. Ujistěte se, že jste pro Azure AD Domain Services vytvořili vyhrazenou podsíť. Nesaďte do této vyhrazené podsítě virtuální počítače úloh.

Zadáním následujících příkazů PowerShellu vytvořte virtuální síť s vyhrazenou podsítí pro Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Úloha 7: Zřízení spravované domény Azure AD Domain Services
Zadáním následujícího příkazu PowerShellu povolte Azure AD Domain Services pro svůj adresář:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Po zřízení spravované domény nezapomeňte další konfigurační kroky zapomenout.**
> Po zřízení spravované domény je stále potřeba provést následující úlohy:
> * Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování. Pokud chcete nakonfigurovat DNS, vyberte na portálu spravovanou doménu Azure služba AD DS. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
> * Vytvořte požadovaná pravidla skupiny zabezpečení sítě pro omezení příchozího provozu pro spravovanou doménu. Pokud chcete vytvořit pravidla skupiny zabezpečení sítě, vyberte na portálu spravovanou doménu Azure služba AD DS. V okně **Přehled** se zobrazí výzva k automatickému vytvoření odpovídajících pravidel skupiny zabezpečení sítě.
> * **[Povolí synchronizaci hesel Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , takže koncoví uživatelé se můžou ke spravované doméně přihlašovat pomocí svých podnikových přihlašovacích údajů.

## <a name="powershell-script"></a>Skript PowerShellu
Skript PowerShellu, který se používá k provedení všech úkolů uvedených v tomto článku, je uvedený níže. Zkopírujte skript a uložte ho do souboru s příponou. ps1. Spusťte skript v prostředí PowerShell nebo pomocí integrovaného skriptovacího prostředí (ISE) prostředí PowerShell.

> [!NOTE]
> **Oprávnění potřebná ke spuštění tohoto skriptu** Pokud chcete povolit Azure AD Domain Services, musíte být globálním správcem adresáře Azure AD. Kromě toho potřebujete alespoň oprávnění Přispěvatel ve virtuální síti, ve které povolíte Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

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
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Po zřízení spravované domény nezapomeňte další konfigurační kroky zapomenout.**
> Po zřízení spravované domény je stále potřeba provést následující úlohy:
> * Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování. Pokud chcete nakonfigurovat DNS, vyberte na portálu spravovanou doménu Azure služba AD DS. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
> * Vytvořte požadovaná pravidla skupiny zabezpečení sítě pro omezení příchozího provozu pro spravovanou doménu. Pokud chcete vytvořit pravidla skupiny zabezpečení sítě, vyberte na portálu spravovanou doménu Azure služba AD DS. V okně **Přehled** se zobrazí výzva k automatickému vytvoření odpovídajících pravidel skupiny zabezpečení sítě.
> * **[Povolí synchronizaci hesel Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , takže koncoví uživatelé se můžou ke spravované doméně přihlašovat pomocí svých podnikových přihlašovacích údajů.

## <a name="next-steps"></a>Další postup
Po vytvoření spravované domény proveďte následující úlohy konfigurace, abyste mohli používat spravovanou doménu:

* [Aktualizujte nastavení serveru DNS pro virtuální síť tak, aby odkazovala na spravovanou doménu.](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
* [Povolení synchronizace hesel do spravované domény](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
