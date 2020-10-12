---
title: Omezení síťového přístupu k prostředkům PaaS – Azure PowerShell
description: V tomto článku se dozvíte, jak omezit a omezit síťový přístup k prostředkům Azure, jako jsou Azure Storage a Azure SQL Database, pomocí koncových bodů služby virtuální sítě pomocí Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 92a5ce539cdd61feb57722756ec8722916f346de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072379"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Omezení síťového přístupu k prostředkům PaaS pomocí koncových bodů služby virtuální sítě pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Koncové body služby pro virtuální síť umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě s jednou podsítí
* Přidání podsítě a povolení koncového bodu služby
* Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
* Nasazení virtuálního počítače do každé podsítě
* Ověření přístupu k prostředku z podsítě
* Ověření odepření přístupu k prostředku z podsítě a internetu

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte konfiguraci podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítě pro podsíť s názvem *Public*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Vytvořte podsíť ve virtuální síti tak, že napíšete konfiguraci podsítě do virtuální sítě pomocí [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby

Koncové body služby můžete povolit pouze pro služby, které podporují koncové body služby. Zobrazte služby s povoleným koncovým bodem služby dostupné v umístění Azure pomocí [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice). Následující příklad vrátí seznam služeb s povoleným koncovým bodem služby dostupných v oblasti *eastus* . Seznam vrácených služeb bude v průběhu času růst, protože se další služby Azure stávají koncovým bodem služby.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Vytvořte další podsíť ve virtuální síti. V tomto příkladu se podsíť s názvem *Private* vytvoří s koncovým bodem služby pro *Microsoft. Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Vytvořte pravidla zabezpečení skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Následující pravidlo povoluje odchozí přístup k veřejným IP adresám přiřazeným službě Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
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
  -Priority 110 `
  -Protocol * `
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
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Omezení síťového přístupu k prostředku

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky pro omezení síťového přístupu pro účet Azure Storage jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte účet Azure Storage pomocí [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Nahraďte `<replace-with-your-unique-storage-account-name>` názvem, který je jedinečný v rámci všech umístění Azure, mezi 3-24 znaků a číslicí a malými písmeny.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po vytvoření účtu úložiště načtěte klíč pro účet úložiště do proměnné pomocí [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Klíč se používá k vytvoření sdílené složky v pozdějším kroku. Zadejte `$storageAcctKey` hodnotu a poznamenejte si ji, protože ji budete muset ručně zadat v pozdějším kroku, Pokud namapujete sdílenou složku na jednotku ve virtuálním počítači.

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvořte kontext pro účet úložiště a klíč pomocí [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontext zapouzdřuje název účtu úložiště a klíč účtu:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Vytvořte sdílenou složku pomocí [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

$share = New-AzStorageShare my-File-Share-Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Odepřít všem síťovým přístupům účet úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Chcete-li omezit přístup k vybraným sítím, změňte výchozí akci na *Odepřít* pomocí [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). Jakmile je přístup k síti zakázán, účet úložiště není přístupný ze žádné sítě.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolení síťového přístupu z podsítě

Načtěte vytvořenou virtuální síť pomocí [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) a pak načtěte objekt privátní podsítě do proměnné pomocí [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Povolte síťový přístup k účtu úložiště z *privátní* podsítě pomocí [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do každé podsítě virtuální počítač.

### <a name="create-the-first-virtual-machine"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač ve *veřejné* podsíti pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Při spuštění následujícího příkazu se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Vrátí výstup podobný následujícímu příkladu výstupu:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>Vytvoření druhého virtuálního počítače

Vytvořte virtuální počítač v *privátní* podsíti:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Vytvoření virtuálního počítače v Azure trvá několik minut. Nepokračujte k dalšímu kroku, dokud Azure nedokončí vytváření virtuálního počítače a nevrátí výstup do PowerShellu.

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

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

Na virtuálním počítači *myVmPrivate* pomocí PowerShellu namapujte sdílenou složku Azure na jednotku Z. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` hodnotami, které jste zadali nebo získali v části [Vytvoření účtu úložiště](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell vrátí podobný výstup jako v následujícím příkladu:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Sdílená složka Azure se úspěšně namapovala na jednotku Z.

Potvrďte, že virtuální počítač nemá žádné odchozí připojení k žádným jiným veřejným IP adresám:

```powershell
ping bing.com
```

Neobdržíte žádné odpovědi, protože skupina zabezpečení sítě přidružená k podsíti *Private* nepovoluje odchozí přístup k jiným veřejným IP adresám, než jsou adresy přiřazené službě Azure Storage.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Získejte veřejnou IP adresu virtuálního počítače *myVmPublic* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

V následujícím příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vrácenou předchozím příkazem a pak ho zadejte: 

```powershell
mstsc /v:<publicIpAddress>
```

Na virtuálním počítači *myVmPublic* se pokuste namapovat sdílenou složku Azure na jednotku Z. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` hodnotami, které jste zadali nebo získali v části [Vytvoření účtu úložiště](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Přístup ke sdílené složce se zamítl a zobrazí se `New-PSDrive : Access is denied` Chyba. Přístup byl odepřen, protože virtuální počítač *myVmPublic* je nasazený v podsíti *Public*. Podsíť *Public* nemá povolený koncový bod služby pro Azure Storage a účet úložiště umožňuje síťový přístup pouze z podsítě *Private*, a ne z podsítě *Public*.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*.

Z počítače se pokuste zobrazit sdílené složky v účtu úložiště pomocí následujícího příkazu:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Přístup je odepřený a obdržíte *Get-AzStorageFile: vzdálený server vrátil chybu: (403) zakázáno. Stavový kód HTTP: 403-chybová zpráva protokolu HTTP: Tento požadavek nemá oprávnění k provedení této operace* , protože váš počítač není v *privátní* podsíti virtuální sítě *MyVirtualNetwork* .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste povolili koncový bod služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb je možné povolit pro prostředky nasazené pomocí několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Informace o postupu najdete v tématu [připojení virtuálních sítí](tutorial-connect-virtual-networks-powershell.md).
