---
title: Povolení služby Azure DS Domain Services pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat a povolit služby Azure Active Directory Domain Services pomocí Azure AD PowerShella a Azure PowerShellu.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613237"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Povolení služby Azure Active Directory Domain Services pomocí prostředí PowerShell

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, ověřování LDAP, Kerberos/NTLM, které je plně kompatibilní se službou Active Directory systému Windows Server. Tyto služby domény spotřebováváte bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Azure AD DS integruje s vaším stávajícím tenantem Azure AD. Tato integrace umožňuje uživatelům přihlásit pomocí jejich podnikových přihlašovacích údajů a můžete použít existující skupiny a uživatelské účty k zabezpečení přístupu k prostředkům.

Tento článek ukazuje, jak povolit Azure AD DS pomocí PowerShellu.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li tento článek dokončit, potřebujete následující zdroje:

* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se přihlásíte k předplatnému Azure pomocí rutiny [Connect-AzAccount.][Connect-AzAccount]
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se k Azure AD](/powershell/azure/active-directory/install-adv2).
    * Ujistěte se, že se přihlásíte ke svému tenantovi Azure AD pomocí rutiny [Connect-AzureAD.][Connect-AzureAD]
* K povolení služby Azure AD DS potřebujete oprávnění *globálního správce* ve vašem tenantovi Azure AD.
* K vytvoření požadovaných prostředků Azure AD DS potřebujete oprávnění *přispěvatele* ve vašem předplatném Azure.

## <a name="create-required-azure-ad-resources"></a>Vytvoření požadovaných prostředků Azure AD

Azure AD DS vyžaduje instanční objekt a skupinu Azure AD. Tyto prostředky umožňují spravované doméně Azure AD DS synchronizovat data a definovat, kteří uživatelé mají oprávnění správce ve spravované doméně.

Nejprve vytvořte instanční objekt služby Azure AD pro Azure AD DS pro komunikaci a ověření sám. Konkrétní ID aplikace se používá s názvem *Služby řadiče domény* s ID *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Toto ID aplikace neměňte.

Vytvořte instanční objekt služby Azure AD pomocí rutiny [New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Teď vytvořte skupinu Azure AD s názvem *Správci řadiče domény AAD*. Uživatelům přidaným do této skupiny jsou pak udělena oprávnění k provádění úloh správy ve spravované doméně Azure AD DS.

Vytvořte skupinu *správců řadiče domény AAD* pomocí rutiny [New-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Při vytvoření *skupiny AAD DC Administrators* přidejte uživatele do skupiny pomocí rutiny [Add-AzureADGroupMember.][Add-AzureADGroupMember] Nejprve získáte ID *skupiny skupiny AAD DC Administrators* pomocí rutiny [Get-AzureADGroup,][Get-AzureADGroup] potom id objektu požadovaného uživatele pomocí rutiny [Get-AzureADUser.][Get-AzureADUser]

V následujícím příkladu id objektu uživatele pro účet `admin@aaddscontoso.onmicrosoft.com`s hlavní název uživatele . Nahraďte tento uživatelský účet hlavní skupinou uživatele, kterou chcete přidat do *skupiny Správci řadiče domény AAD:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Vytvoření podpůrných prostředků Azure

Nejprve zaregistrujte zprostředkovatele prostředků služby Azure AD Domain Services pomocí rutiny [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Dále vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup.][New-AzResourceGroup] V následujícím příkladu je skupina prostředků s názvem *myResourceGroup* a je vytvořena v oblasti *westus.* Použijte své vlastní jméno a požadovanou oblast:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Vytvořte virtuální síť a podsítě pro služby Azure AD Domain Services. Jsou vytvořeny dvě podsítě – jedna pro *DomainServices*a jedna pro *úlohy*. Azure AD DS se nasazuje do vyhrazené podsítě *DomainServices.* Do této podsítě nenasazujte jiné aplikace nebo úlohy. Pro zbytek virtuálních počítačů použijte samostatné *úlohy* nebo jiné podsítě.

Vytvořte podsítě pomocí rutiny [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] a potom vytvořte virtuální síť pomocí rutiny [Nová azvirtualnetwork.][New-AzVirtualNetwork]

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-an-azure-ad-ds-managed-domain"></a>Vytvoření spravované domény Azure AD DS

Teď pojďme vytvořit spravovanou doménu Azure AD DS. Nastavte ID předplatného Azure a zadejte název spravované domény, například *aaddscontoso.com*. ID předplatného můžete získat pomocí rutiny [Get-AzSubscription.][Get-AzSubscription]

Pokud zvolíte oblast, která podporuje zóny dostupnosti, prostředky Azure AD DS jsou distribuovány napříč zónami pro další redundanci.

Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Aby byla zajištěna odolnost proti chybám, jsou ve všech povolených oblastech minimálně tři samostatné zóny.

Není nic pro vás nakonfigurovat pro Azure AD DS distribuovat napříč zónami. Platforma Azure automaticky zpracovává zónovou distribuci prostředků. Další informace a informace o dostupnosti oblasti najdete v tématu [Co jsou zóny dostupnosti v Azure?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Vytvoření prostředku a vrácení ovládacího prvku do výzvy prostředí PowerShell trvá několik minut. Spravovaná doména Azure AD DS se nadále zřaží na pozadí a dokončení nasazení může trvat až hodinu. Na webu Azure Portal stránka **Přehled** pro spravovanou doménu Azure AD DS zobrazuje aktuální stav v této fázi nasazení.

Když portál Azure ukazuje, že spravovaná doména Azure AD DS dokončila zřizování, je třeba dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Chcete-li nakonfigurovat službu DNS, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatické konfiguraci těchto nastavení DNS.
* Pokud jste vytvořili spravovanou doménu Azure AD DS v oblasti, která podporuje zóny dostupnosti, vytvořte skupinu zabezpečení sítě, která omezí provoz ve virtuální síti pro spravovanou doménu Azure AD DS. Azure standardní vyrovnávání zatížení je vytvořen, který vyžaduje, aby tato pravidla být místo. Tato skupina zabezpečení sítě zabezpečuje Azure AD DS a je vyžadována pro spravovanou doménu pracovat správně.
    * Pokud chcete vytvořit skupinu zabezpečení sítě a požadovaná pravidla, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatickému vytvoření a konfiguraci skupiny zabezpečení sítě.
* [Povolte synchronizaci hesel do služby Azure AD Domain Services,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="complete-powershell-script"></a>Kompletní skript Prostředí PowerShell

Následující kompletní skript prostředí PowerShell kombinuje všechny úkoly uvedené v tomto článku. Zkopírujte skript a uložte jej `.ps1` do souboru s příponou. Spusťte skript v místní konzoli PowerShellu nebo [v prostředí Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Chcete-li povolit Azure AD DS, musíte být globálním správcem pro klienta Azure AD. V předplatném Azure také potřebujete alespoň oprávnění *přispěvatele.*

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

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
  -Force -Verbose
```

Vytvoření prostředku a vrácení ovládacího prvku do výzvy prostředí PowerShell trvá několik minut. Spravovaná doména Azure AD DS se nadále zřaží na pozadí a dokončení nasazení může trvat až hodinu. Na webu Azure Portal stránka **Přehled** pro spravovanou doménu Azure AD DS zobrazuje aktuální stav v této fázi nasazení.

Když portál Azure ukazuje, že spravovaná doména Azure AD DS dokončila zřizování, je třeba dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Chcete-li nakonfigurovat službu DNS, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatické konfiguraci těchto nastavení DNS.
* Pokud jste vytvořili spravovanou doménu Azure AD DS v oblasti, která podporuje zóny dostupnosti, vytvořte skupinu zabezpečení sítě, která omezí provoz ve virtuální síti pro spravovanou doménu Azure AD DS. Azure standardní vyrovnávání zatížení je vytvořen, který vyžaduje, aby tato pravidla být místo. Tato skupina zabezpečení sítě zabezpečuje Azure AD DS a je vyžadována pro spravovanou doménu pracovat správně.
    * Pokud chcete vytvořit skupinu zabezpečení sítě a požadovaná pravidla, vyberte na portálu spravovanou doménu Azure AD DS. V okně **Přehled** budete vyzváni k automatickému vytvoření a konfiguraci skupiny zabezpečení sítě.
* [Povolte synchronizaci hesel do služby Azure AD Domain Services,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit spravovanou doménu Azure AD DS v akci, můžete [se připojit k virtuálnímu počítači windows][windows-join], [nakonfigurovat zabezpečené LDAP][tutorial-ldaps]a [nakonfigurovat synchronizaci hash hesel][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
