---
title: Omezení exfiltrace dat na Azure Storage – Azure PowerShell
description: V tomto článku se dozvíte, jak omezit a omezit exfiltraci dat virtuální sítě na prostředky Azure Storage pomocí zásad koncového bodu virtuální síťové služby pomocí Azure PowerShellu.
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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253023"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Správa exfiltrace dat na účty Azure Storage pomocí zásad koncového bodu služby Virtuální sítě pomocí Azure PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zásady koncového bodu virtuální síťové služby umožňují použít řízení přístupu na účty Azure Storage z virtuální sítě přes koncové body služby. To to je klíč k zabezpečení úloh, správě účtů úložiště jsou povoleny a kde je povolena exfiltrace dat.
V tomto článku získáte informace o těchto tématech:

* Vytvořte virtuální síť.
* Přidejte podsíť a povolte koncový bod služby pro Azure Storage.
* Vytvořte dva účty Azure Storage a povolte přístup k síti z výše vytvořené podsítě.
* Vytvořte zásadu koncového bodu služby, která umožní přístup pouze k jednomu z účtů úložiště.
* Nasazení virtuálního počítače (VM) do podsítě.
* Potvrďte přístup k povolenému účtu úložiště z podsítě.
* Potvrďte, že přístup k nepovolenému účtu úložiště je odepřen z podsítě.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro virtuální síť a všechny ostatní prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Vytvořte virtuální síť s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby

Vytvořte podsíť ve virtuální síti. V tomto příkladu je vytvořena podsíť s názvem *Private* s koncovým bodem služby pro *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Omezení přístupu k síti pro podsíť

Vytvořte pravidla zabezpečení skupiny zabezpečení sítě pomocí funkce [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující pravidlo umožňuje odchozí přístup k veřejným IP adresám přiřazeným ke službě Azure Storage: 

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

Následující pravidlo odepře přístup ke všem veřejným IP adresám. Předchozí pravidlo přepíše toto pravidlo, vzhledem k jeho vyšší prioritu, která umožňuje přístup k veřejné IP adresy Azure Storage.

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

Následující pravidlo umožňuje přenosy protokolu RDP (RdP) vzdálené plochy a z libovolného místa. Připojení ke vzdálené ploše jsou k podsíti povolena, takže můžete v pozdějším kroku potvrdit přístup k prostředku v síti.

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

Vytvořte skupinu zabezpečení sítě pomocí skupiny [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Přidružte skupinu zabezpečení sítě k *privátní* podsíti k [nástroji Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) a pak zapište konfiguraci podsítě do virtuální sítě. Následující příklad přidruží skupinu zabezpečení sítě *myNsgPrivate* k *soukromé* podsíti:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Omezení přístupu k síti na účty Azure Storage

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky k omezení přístupu k síti pro účet Azure Storage, jako příklad.

### <a name="create-two-storage-accounts"></a>Vytvoření dvou účtů úložiště

Vytvořte účet úložiště Azure s [new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po vytvoření účtu úložiště načtěte klíč pro účet úložiště do proměnné s [klíčem Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Klíč se používá k vytvoření sdílené složky v pozdějším kroku. Zadejte `$storageAcctKey` a poznamenejte si hodnotu, protože ji budete muset zadat ručně v pozdějším kroku, když mapujete sdílenou složku na jednotku ve virtuálním ms.

Nyní opakujte výše uvedené kroky k vytvoření druhého účtu úložiště.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Také načíst klíč účtu úložiště z tohoto účtu pro pozdější použití k vytvoření sdílené složky.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Vytvoření sdílené složky v každém účtu úložiště

Vytvořte kontext pro váš účet úložiště a klíč s [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontext zapouzdřuje název účtu úložiště a klíč účtu:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Vytvořte sdílenou složku s [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Odepřít veškerý síťový přístup k účtům úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Příkaz Odepřít* pomocí [sady Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

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

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Povolení přístupu k síti jenom z podsítě virtuální sítě

Načtěte vytvořenou virtuální síť pomocí [programu Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) a potom načtěte objekt privátní podsítě do proměnné pomocí [funkce Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Povolit síťový přístup k účtu úložiště z *privátní* podsítě pomocí [funkce Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

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

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Použití zásad pro povolení přístupu k platnému účtu úložiště

Chcete-li se ujistit, že uživatelé ve virtuální síti mají přístup jenom k účtům Azure Storage, které jsou bezpečné a povolené, můžete vytvořit zásadu koncového bodu služby se seznamem povolených účtů úložiště v definici. Tato zásada se pak použije pro podsíť virtuální sítě, která je připojena k úložišti prostřednictvím koncových bodů služby.

### <a name="create-a-service-endpoint-policy"></a>Vytvoření zásadkoncového bodu služby

Tato část vytvoří definici zásad se seznamem povolených prostředků pro přístup přes koncový bod služby.

Načtení ID prostředku pro první (povolený) účet úložiště 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Vytvoření definice zásad, která umožní výše uvedený zdroj

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Vytvoření zásady koncového bodu služby pomocí definice zásady vytvořené výše

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Přidružení zásady koncového bodu služby k podsíti virtuální sítě

Po vytvoření zásady koncového bodu služby ji přidružíte k cílové podsíti s konfigurací koncového bodu služby pro Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Ověření omezení přístupu k účtům Azure Storage

### <a name="deploy-the-virtual-machine"></a>Nasazení virtuálního počítače

Chcete-li otestovat přístup k síti k účtu úložiště, nasaďte virtuální ho v podsíti.

Vytvořte virtuální počítač v *privátní* podsíti s [New-AzVM](/powershell/module/az.compute/new-azvm). Při spuštění následujícího příkazu se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Výstup podobný následujícímu příkladu výstupu je vrácena:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Potvrzení přístupu k *povolenému* účtu úložiště

Pomocí [get-azPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) vrátit veřejnou IP adresu virtuálního počítačů. Následující příklad vrátí veřejnou IP adresu virtuálního mm *myVmPrivate:*

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

Na virtuálním počítači *myVmPrivate* namapujte sdílenou složku Azure z povoleného účtu úložiště na jednotku Z pomocí PowerShellu. 

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

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Potvrzení odepření *přístupu k nepovolenému* účtu úložiště

Na stejném virtuálním počítači *myVmPrivate* se pokuste mapovat sdílenou složku Azure na jednotku X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Přístup ke sdílené položce je `New-PSDrive : Access is denied` odepřen a zobrazí se chyba. Přístup je odepřen, protože účet úložiště *není povolen účet* není v seznamu povolených prostředků v zásadách koncového bodu služby. 

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete [odebrat-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste použili zásady koncového bodu služby přes koncový bod virtuální sítě Azure do azure storage. Vytvořili jste účty Azure Storage a omezený přístup k síti pouze na určité účty úložiště (a tím odepřeli ostatní) z podsítě virtuální sítě. Další informace o zásadách koncového bodu služby najdete v [tématu Přehled zásad koncových bodů služby](virtual-network-service-endpoint-policies-overview.md).
