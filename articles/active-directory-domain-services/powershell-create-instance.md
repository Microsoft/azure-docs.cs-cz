---
title: Povolení služby Azure DS Domain Services pomocí PowerShellu | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat a povolit Azure Active Directory Domain Services s využitím Azure AD PowerShellu a Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e385fd6af61fe58912c3e8053fcd16422c24c0d0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441722"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Povolení Azure Active Directory Domain Services pomocí prostředí PowerShell

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory. Tyto doménové služby spotřebujete bez nutnosti nasazovat, spravovat a opravovat řadiče domény sami. Služba Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD. Tato integrace umožňuje uživatelům přihlásit se pomocí svých podnikových přihlašovacích údajů a pomocí existujících skupin a uživatelských účtů můžete zabezpečit přístup k prostředkům.

V tomto článku se dozvíte, jak povolit Azure služba AD DS pomocí prostředí PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete tyto prostředky:

* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se ke svému předplatnému Azure přihlašujete pomocí rutiny [Connect-AzAccount][Connect-AzAccount] .
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se ke službě Azure AD](/powershell/azure/active-directory/install-adv2).
    * Pomocí rutiny [Connect-AzureAD][Connect-AzureAD] se ujistěte, že se přihlašujete k TENANTOVI Azure AD.
* Abyste mohli Azure služba AD DS povolit, potřebujete ve svém tenantovi Azure AD oprávnění *globálního správce* .
* Abyste mohli vytvořit požadované prostředky Azure služba AD DS, potřebujete oprávnění *přispěvatele* v předplatném Azure.

## <a name="create-required-azure-ad-resources"></a>Vytvoření požadovaných prostředků Azure AD

Služba Azure služba AD DS vyžaduje instanční objekt a skupinu Azure AD. Tyto prostředky umožňují, aby Azure služba AD DS spravovaná doména synchronizovaná data a definovali, kteří uživatelé mají oprávnění správce ve spravované doméně.

Nejdřív vytvořte instanční objekt služby Azure AD pro Azure služba AD DS, který bude komunikovat a ověřovat sám sebe. Konkrétní ID aplikace se používá s názvem *služby řadiče domény* s ID *2565bd9d-DA50-47d4-8B85-4c97f669dc36*. Neměňte toto ID aplikace.

Pomocí rutiny [New-AzureADServicePrincipal][New-AzureADServicePrincipal] vytvořte instanční objekt služby Azure AD:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Nyní vytvořte skupinu Azure AD s názvem *AAD DC Administrators*. Uživatelům přidaným do této skupiny se pak udělí oprávnění k provádění úloh správy ve spravované doméně.

Pomocí rutiny [New-AzureADGroup][New-AzureADGroup] vytvořte skupinu *správců řadiče domény AAD* :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Když je vytvořená skupina *AAD DC Administrators* , přidejte uživatele do skupiny pomocí rutiny [Add-AzureADGroupMember][Add-AzureADGroupMember] . Nejprve pomocí rutiny [Get-][Get-AzureADUser] [AzureADGroup][Get-AzureADGroup] Získejte ID objektu skupiny *AAD DC Administrators* a pak ID objektu požadovaného uživatele.

V následujícím příkladu je ID objektu uživatele pro účet s hlavním názvem uživatele (UPN) `admin@contoso.onmicrosoft.com` . Nahraďte tento uživatelský účet uživatelským jménem uživatele, kterého chcete přidat do skupiny *správců řadiče domény AAD* :

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

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Nejdřív Zaregistrujte poskytovatele prostředků Azure AD Domain Services pomocí rutiny [Register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Dále vytvořte skupinu prostředků pomocí rutiny [New-AzResourceGroup][New-AzResourceGroup] . V následujícím příkladu má skupina prostředků název *myResourceGroup* a je vytvořená v oblasti *westus* . Použijte svůj vlastní název a požadovanou oblast:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Vytvořte virtuální síť a podsítě pro Azure AD Domain Services. Vytvoří se dvě podsítě – jeden pro *DomainServices*a jeden pro *úlohy*. Azure služba AD DS je nasazený do vyhrazené podsítě *DomainServices* . Do této podsítě nesaďte žádné další aplikace ani úlohy. Pro zbytek virtuálních počítačů použijte samostatné *úlohy* nebo jiné podsítě.

Vytvořte podsítě pomocí rutiny [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] a pak vytvořte virtuální síť pomocí rutiny [New-AzVirtualNetwork][New-AzVirtualNetwork] .

```powershell
$VnetName = "myVnet"
$SubnetName = "DomainServices"

# Create the dedicated subnet for Azure AD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
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

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Azure služba AD DS potřebuje skupinu zabezpečení sítě k zabezpečení portů potřebných pro spravovanou doménu a blokování všech ostatních příchozích přenosů. [Skupina zabezpečení sítě (NSG)][nsg-overview] obsahuje seznam pravidel, která povolují nebo zakazují síťový provoz do provozu ve službě Azure Virtual Network. V Azure služba AD DS skupina zabezpečení sítě funguje jako další vrstva ochrany, která umožňuje uzamknout přístup ke spravované doméně. Chcete-li zobrazit požadované porty, přečtěte si téma [skupiny zabezpečení sítě a požadované porty][network-ports].

Následující rutiny PowerShellu používají rutinu [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] k vytvoření pravidel a pak rutinu [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] k vytvoření skupiny zabezpečení sítě. Skupina zabezpečení sítě a pravidla se pak přidruží k podsíti virtuální sítě pomocí rutiny [set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig] .

```powershell
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 443 traffic for synchronization with Azure AD
$nsg101 = New-AzNetworkSecurityRuleConfig `
    -Name AllowSyncWithAzureAD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 101 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg101,$nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>Vytvoření spravované domény

Nyní vytvoříme spravovanou doménu. Nastavte ID vašeho předplatného Azure a potom zadejte název spravované domény, třeba *aaddscontoso.com*. ID vašeho předplatného můžete získat pomocí rutiny [Get-AzSubscription][Get-AzSubscription] .

Pokud zvolíte oblast, která podporuje Zóny dostupnosti, prostředky Azure služba AD DS se rozdělují mezi zóny, aby se mohla zvýšit redundance.

Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny.

Není tu nic, co byste mohli nakonfigurovat pro Azure služba AD DS k distribuci mezi zónami. Platforma Azure automaticky zpracovává distribuci prostředků v zóně. Další informace a informace o dostupnosti oblastí najdete v tématu [co jsou zóny dostupnosti v Azure?][availability-zones].

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

Vytvoření prostředku a vrácení řízení do příkazového řádku PowerShellu trvá několik minut. Spravovaná doména je nadále zřízena na pozadí a může trvat až hodinu, než se nasazení dokončí. V Azure Portal zobrazuje stránka **Přehled** pro spravovanou doménu aktuální stav v rámci této fáze nasazení.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény, musí se dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte spravovanou doménu na portálu. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel do Azure služba AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="complete-powershell-script"></a>Dokončení skriptu PowerShellu

Následující dokončený skript PowerShellu kombinuje všechny úlohy, které jsou uvedené v tomto článku. Zkopírujte skript a uložte ho do souboru s `.ps1` příponou. Spusťte skript v konzole prostředí PowerShell nebo v [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Pokud chcete povolit Azure služba AD DS, musíte být globálním správcem tenanta Azure AD. V předplatném Azure budete také potřebovat aspoň oprávnění *Přispěvatel* .

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
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
  
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 443 traffic for synchronization with Azure AD
$nsg101 = New-AzNetworkSecurityRuleConfig `
    -Name AllowSyncWithAzureAD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 101 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg101,$nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Vytvoření prostředku a vrácení řízení do příkazového řádku PowerShellu trvá několik minut. Spravovaná doména je nadále zřízena na pozadí a může trvat až hodinu, než se nasazení dokončí. V Azure Portal zobrazuje stránka **Přehled** pro spravovanou doménu aktuální stav v rámci této fáze nasazení.

Když Azure Portal ukáže, že se dokončilo zřizování spravované domény, musí se dokončit následující úkoly:

* Aktualizujte nastavení DNS pro virtuální síť, aby virtuální počítače mohly najít spravovanou doménu pro připojení k doméně nebo ověřování.
    * Pokud chcete nakonfigurovat DNS, vyberte spravovanou doménu na portálu. V okně **Přehled** se zobrazí výzva k automatické konfiguraci těchto nastavení DNS.
* [Povolte synchronizaci hesel do Azure služba AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) tak, aby se koncoví uživatelé mohli přihlásit ke spravované doméně pomocí svých podnikových přihlašovacích údajů.

## <a name="next-steps"></a>Další kroky

Pokud chcete spravovanou doménu zobrazit v akci, můžete se [připojit k virtuálnímu počítači s Windows][windows-join], [nakonfigurovat zabezpečený protokol LDAP][tutorial-ldaps]a [nakonfigurovat synchronizaci hodnot hash hesel][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

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
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig
