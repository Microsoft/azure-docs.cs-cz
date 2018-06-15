---
title: Omezit síťový přístup k prostředkům PaaS - prostředí Azure PowerShell | Microsoft Docs
description: V tomto článku zjistěte, jak chcete omezit a omezit přístup k síti na prostředky Azure, například Azure Storage a Azure SQL Database, s koncovými body služby virtuální sítě pomocí prostředí Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b3977e045751165947243c67291e81b998b5fcb5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603755"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Omezit přístup k síti na PaaS prostředky s koncovými body služby virtuální sítě pomocí prostředí PowerShell

Koncové body služby virtuální sítě umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto článku získáte informace o těchto tématech:

* Vytvoření virtuální sítě s jednou podsítí
* Přidání podsítě a povolení koncového bodu služby
* Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
* Nasazení virtuálního počítače do každé podsítě
* Ověření přístupu k prostředku z podsítě
* Ověření odepření přístupu k prostředku z podsítě a internetu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup*: 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork* s předponou adresy *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Vytvořte konfiguraci podsítě pomocí rutiny [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítě pro podsíť s názvem *veřejné*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Vytvořte podsíť ve virtuální síti pomocí zápisu podsítě konfigurace do virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby 

Můžete povolit koncové body služby pouze pro služby, které podporují koncové body služby. Zobrazit služby koncový bod s podporou služeb dostupných v Azure umístění s [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). Následující příklad vrátí seznam hodnot povolena služba-koncový bod služby k dispozici v *eastus* oblast. Seznam služeb, vrátí se zvýší časem více Azure services jsou povolené koncový bod služby.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Vytvořte další podsítě ve virtuální síti. V tomto příkladu podsíť s názvem *privátní* je vytvořena s koncového bodu služby pro *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Vytvoření pravidel skupiny zabezpečení s zabezpečení sítě [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Následující pravidlo umožňuje odchozí přístup na veřejné IP adresy přiřazené ke službě Azure Storage: 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
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

Následující pravidlo odepře přístup k všechny veřejné IP adresy. Předchozí pravidlo přepíše toto pravidlo, a to z důvodu vyšší prioritu, který umožňuje přístup k veřejné IP adresy služby Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
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

Následující pravidlo umožňuje provoz protokolu RDP (Remote Desktop) k podsíti příchozí odkudkoli. Připojení ke vzdálené ploše je povoleno podsítě, abyste mohli ověřit síťový přístup k prostředku v pozdější fázi.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
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

Vytvořte skupinu zabezpečení sítě pomocí rutiny [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Přidružení skupiny zabezpečení sítě k *privátní* podsíť s [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) a zapište si konfiguraci podsítě virtuální sítě. V následujícím příkladu *myNsgPrivate* skupinu zabezpečení sítě *privátní* podsítě:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Omezení síťového přístupu k prostředku

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure povolených v koncových bodech se u jednotlivých služeb liší. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto článku obsahuje kroky k omezit přístup k síti pro účet úložiště Azure jako příklad.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvoření účtu úložiště Azure s [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/new-azurermstorageaccount). Nahraďte `<replace-with-your-unique-storage-account-name>` s názvem, který je jedinečné ve všech umístěních Azure, 3 až 24 znaků, pomocí jenom čísla a malá písmena.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Po vytvoření účtu úložiště načíst klíč pro účet úložiště do proměnné s [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

Používá se k vytvoření sdílené složky v pozdější fázi. Zadejte `$storageAcctKey` a poznamenejte si hodnotu, protože budete také muset ručně zadejte později při mapování sdílené složky na jednotku ve virtuálním počítači.

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

Vytvoření kontextu pro účet úložiště a klíč s [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). Kontext obsahuje klíč účet a název účtu úložiště:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

Vytvoření sdílené složky s [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my sdílené složky - kontextu $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Odepřít všechny přístup k síti na účet úložiště

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti. Omezit přístup k vybrané sítě, změňte výchozí akci na *Odepřít* s [aktualizace AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Jakmile je odepřen přístup k síti, účet úložiště není přístupné z jakékoli sítě.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolení síťového přístupu z podsítě

Načtení vytvořené virtuální síť s [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) a následně načíst objekt privátní podsítě do proměnné s [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Povolit přístup k síti na účet úložiště z *privátní* podsíť s [přidat AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do každé podsítě virtuální počítač.

### <a name="create-the-first-virtual-machine"></a>Vytvoření prvního virtuálního počítače

Vytvoření virtuálního počítače v *veřejné* podsíť s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Při spuštění následujícího příkazu se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Vrátí se výstup podobný výstupu v následujícím příkladu.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>Vytvoření druhého virtuálního počítače

Vytvoření virtuálního počítače v *privátní* podsítě:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Jak dlouho trvá několik minut, než Azure a vytvoření virtuálního počítače. Nebudete pokračovat k dalšímu kroku až Azure dokončí vytváření virtuálního počítače a vrátí výstup do prostředí PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

Pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) získejte veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVmPrivate* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

V následujícím příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vrácenou předchozím příkazem a pak ho zadejte: 

```powershell
mstsc /v:<publicIpAddress>
```

Vytvoří se soubor protokolu RDP (.rdp) a stáhne se na váš počítač. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. Vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.

Na virtuálním počítači *myVmPrivate* pomocí PowerShellu namapujte sdílenou složku Azure na jednotku Z. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` hodnotami z zadaný nebo načíst v [vytvořit účet úložiště](#create-a-storage-account).

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

Potvrďte, že virtuální počítač bez odchozí připojení k jakékoli jiné veřejné IP adresy:

```powershell
ping bing.com
```

Neobdržíte žádné odpovědi, protože skupina zabezpečení sítě přidružená k podsíti *Private* nepovoluje odchozí přístup k jiným veřejným IP adresám, než jsou adresy přiřazené službě Azure Storage.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

Získat veřejnou IP adresu *myVmPublic* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

V následujícím příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vrácenou předchozím příkazem a pak ho zadejte: 

```powershell
mstsc /v:<publicIpAddress>
```

Na *myVmPublic* virtuálních počítačů, snažit mapovat jednotku Z Azure sdílené složky. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` hodnotami z zadaný nebo načíst v [vytvořit účet úložiště](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Byl odepřen přístup ke sdílené složce, a zobrazí se `New-PSDrive : Access is denied` chyby. Přístup byl odepřen, protože virtuální počítač *myVmPublic* je nasazený v podsíti *Public*. Podsíť *Public* nemá povolený koncový bod služby pro Azure Storage a účet úložiště umožňuje síťový přístup pouze z podsítě *Private*, a ne z podsítě *Public*.

Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*.

Z počítače se pokusí o zobrazení sdílených složek v účtu úložiště pomocí následujícího příkazu:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Přístup byl odepřen, a zobrazí se *Get-AzureStorageFile: vzdálený server vrátil chybu: (403) zakázán. Kód stavu HTTP: 403 - HTTP chybová zpráva: Tento požadavek není autorizovaný k provedení této operace* chyby, protože počítač není v *privátní* podsíť *MyVirtualNetwork* virtuální síť.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste povolili koncového bodu služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb je možné povolit pro prostředky nasazené pomocí několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Další informace, jak zjistit, [připojení virtuální sítě](tutorial-connect-virtual-networks-powershell.md).
