---
title: Omezení exfiltrace dat na Azure Storage-Azure PowerShell
description: V tomto článku se dozvíte, jak omezit a omezit exfiltrace dat virtuální sítě a jak Azure Storage prostředky pomocí zásad koncového bodu služby virtuální sítě pomocí Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: d8e300c9be8f3e59dc9443bf1f1806e4228992ad
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790355"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Správa exfiltrace dat pro Azure Storage účtů pomocí zásad koncového bodu služby virtuální sítě pomocí Azure PowerShell

Zásady koncového bodu služby virtuální sítě umožňují použít řízení přístupu u účtů Azure Storage v rámci virtuální sítě prostřednictvím koncových bodů služby. Toto je klíč pro zabezpečení úloh, správu, které účty úložiště jsou povolené a kde je povolený exfiltrace dat.
V tomto článku získáte informace o těchto tématech:

* Vytvořte virtuální síť.
* Přidání podsítě a povolení koncového bodu služby pro Azure Storage.
* Vytvořte dva účty Azure Storage a povolte přístup k síti z podsítě vytvořené výše.
* Vytvořte zásadu koncového bodu služby, která povolí přístup jenom k jednomu z účtů úložiště.
* Nasaďte virtuální počítač (VM) do podsítě.
* Potvrďte přístup k povolenému účtu úložiště z podsítě.
* Potvrďte, že je přístup k nedovolenému účtu úložiště z podsítě odepřený.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby

Vytvořte podsíť ve virtuální síti. V tomto příkladu se podsíť s názvem *Private* vytvoří s koncovým bodem služby pro *Microsoft. Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Omezení přístupu k síti pro podsíť

Vytvořte pravidla zabezpečení skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující pravidlo povoluje odchozí přístup k veřejným IP adresám přiřazeným službě Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Následující pravidlo odepře přístup ke všem veřejným IP adresám. Předchozí pravidlo přepisuje toto pravidlo z důvodu vyšší priority, což umožňuje přístup k veřejným IP adresám Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Následující pravidlo povoluje příchozí provoz protokol RDP (Remote Desktop Protocol) (RDP) do podsítě odkudkoli. Připojení ke vzdálené ploše jsou povolená pro podsíť, takže můžete v pozdějším kroku potvrdit síťový přístup k prostředkům.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Přidružte skupinu zabezpečení sítě k *privátní* podsíti pomocí [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) a pak zapište konfiguraci podsítě do virtuální sítě. Následující příklad přidruží skupinu zabezpečení sítě *myNsgPrivate* k *privátní* podsíti:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Omezení síťového přístupu k účtům Azure Storage

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky pro omezení síťového přístupu pro účet Azure Storage jako příklad.

### <a name="create-two-storage-accounts"></a>Vytvoření dvou účtů úložiště

Vytvořte účet Azure Storage pomocí [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po vytvoření účtu úložiště načtěte klíč pro účet úložiště do proměnné pomocí [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Klíč se používá k vytvoření sdílené složky v pozdějším kroku. Zadejte `$storageAcctKey` hodnotu a poznamenejte si ji, protože ji budete muset ručně zadat v pozdějším kroku, Pokud namapujete sdílenou složku na jednotku ve virtuálním počítači.

Nyní opakujte výše uvedené kroky a vytvořte druhý účet úložiště.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Z tohoto účtu taky načtěte klíč účtu úložiště pro pozdější použití k vytvoření sdílené složky.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Vytvoření sdílené složky v každém účtu úložiště

Vytvořte kontext pro účet úložiště a klíč pomocí [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontext zapouzdřuje název účtu úložiště a klíč účtu:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Vytvořte sdílenou složku pomocí [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Odepřít všem síťovým přístupům účty úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Odepřít* pomocí [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Povolit přístup k síti jenom z podsítě virtuální sítě

Načtěte vytvořenou virtuální síť pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) a pak načtěte objekt privátní podsítě do proměnné pomocí [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Povolte síťový přístup k účtu úložiště z *privátní* podsítě pomocí [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Použijte zásady, aby se povolil přístup k platnému účtu úložiště.

Abyste měli jistotu, že uživatelé ve virtuální síti budou mít přístup jenom k účtům Azure Storage, které jsou bezpečné a povolené, můžete vytvořit zásadu koncového bodu služby se seznamem povolených účtů úložiště v definici. Tato zásada se pak použije pro podsíť virtuální sítě, která je připojená k úložišti prostřednictvím koncových bodů služby.

### <a name="create-a-service-endpoint-policy"></a>Vytvoření zásad koncového bodu služby

V této části se vytvoří definice zásady se seznamem povolených prostředků pro přístup prostřednictvím koncového bodu služby.

Načte ID prostředku prvního (povoleného) účtu úložiště. 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Vytvořte definici zásady, která povolí výše uvedený prostředek.

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Vytvořte zásadu koncového bodu služby pomocí definice zásady, kterou jste vytvořili výše.

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Přidružte zásady koncového bodu služby k podsíti virtuální sítě.

Po vytvoření zásad koncového bodu služby ji přidružíte k cílové podsíti s konfigurací koncového bodu služby pro Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Ověří omezení přístupu k účtům Azure Storage.

### <a name="deploy-the-virtual-machine"></a>Nasazení virtuálního počítače

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do podsítě virtuální počítač.

Vytvořte virtuální počítač v *privátní* podsíti pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Při spuštění následujícího příkazu se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Vrátí výstup podobný následujícímu příkladu výstupu:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Potvrďte přístup k *povolenému* účtu úložiště.

K vrácení veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVmPrivate* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

V následujícím příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vrácenou předchozím příkazem a pak ho zadejte:

```powershell
mstsc /v:<publicIpAddress>
```

Vytvoří se soubor protokolu RDP (.rdp) a stáhne se na váš počítač. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. Vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.

Na virtuálním počítači *myVmPrivate* namapujte sdílenou složku Azure z účtu povoleného úložiště na jednotku Z pomocí prostředí PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell vrátí podobný výstup jako v následujícím příkladu:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Sdílená složka Azure se úspěšně namapovala na jednotku Z.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Potvrďte, že přístup je odepřený účtu úložiště, *který není povolený* .

Na stejném virtuálním počítači s *myVmPrivate* se pokuste namapovat sdílenou složku Azure na jednotku X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Přístup ke sdílené složce se zamítl a zobrazí se `New-PSDrive : Access is denied` Chyba. Přístup byl odepřen, protože účet úložiště *notallowedaccount* není v seznamu povolených prostředků v zásadách koncového bodu služby. 

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste u koncového bodu služby Azure Virtual Network použili zásadu koncového bodu služby na Azure Storage. Vytvořili jste účty Azure Storage a omezili jste přístup k síti pouze na určité účty úložiště (a tedy jiným uživatelům zamítli) z podsítě virtuální sítě. Další informace o zásadách koncového bodu služby najdete v tématu [Přehled zásad koncových bodů služby](virtual-network-service-endpoint-policies-overview.md).
