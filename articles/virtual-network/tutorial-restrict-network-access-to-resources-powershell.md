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
ms.openlocfilehash: 28c95e1333b4641e50284a869135a9608dd3242f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Omezit přístup k síti na PaaS prostředky s koncovými body služby virtuální sítě pomocí prostředí PowerShell

Koncové body služby virtuální sítě umožňují omezit přístup k síti k některým prostředkům služby Azure do podsítě virtuální sítě. Můžete také odebrat přístup k Internetu k prostředkům. Koncové body služby poskytují přímé připojení z virtuální sítě k podporované služby Azure, budete moci použít privátní adresní prostor vaší virtuální sítě pro přístup ke službám Azure. Přenosy určené prostředky Azure prostřednictvím koncových bodů služby vždy zůstává na páteřní síti Microsoft Azure. V tomto článku se dozvíte, jak:

* Vytvořit virtuální síť s jednou podsítí
* Přidat podsíť a povolení koncového bodu služby
* Vytvořit prostředek služby Azure a povolit přístup k síti z jenom podsítě
* Nasazení virtuálního počítače (VM) pro každou podsíť
* Potvrďte přístup k prostředku z podsítě
* Potvrďte, že byl odepřen přístup k prostředku z podsítě a z Internetu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí Azure PowerShell verze modulu 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup*: 

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

Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Následující příklad vytvoří konfiguraci podsítě pro podsíť s názvem *veřejné*:

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

## <a name="restrict-network-access-for-a-subnet"></a>Omezit přístup k síti pro podsíť

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

Vytvořit skupinu zabezpečení sítě s [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNsgPrivate*.

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

## <a name="restrict-network-access-to-a-resource"></a>Omezit přístup k síti na prostředek

Kroky potřebné k omezit přístup k síti na prostředky, které jsou vytvořené pomocí služby Azure, které jsou povolené pro koncové body služby se liší v rámci služby. Najdete v dokumentaci pro jednotlivé služby pro konkrétní kroky pro každou službu. Zbývající část tohoto článku obsahuje kroky k omezit přístup k síti pro účet úložiště Azure jako příklad.

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

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Odepřít všechny přístup k síti na účet úložiště

Ve výchozím nastavení účty úložiště přijmout síťová připojení z klientů ve žádné síti. Omezit přístup k vybrané sítě, změňte výchozí akci na *Odepřít* s [aktualizace AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Jakmile je odepřen přístup k síti, účet úložiště není přístupné z jakékoli sítě.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Povolit přístup k síti z podsítě

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

Otestovat přístup k síti na účet úložiště, nasaďte virtuální počítač do každé podsítě.

### <a name="create-the-first-virtual-machine"></a>Vytvořte první virtuální počítač

Vytvoření virtuálního počítače v *veřejné* podsíť s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Když spustíte příkaz, který následuje, zobrazí se výzva k zadání pověření. Hodnoty, které zadáte jsou nakonfigurovány jako uživatelské jméno a heslo pro virtuální počítač. `-AsJob` Možnost vytvoří virtuální počítač na pozadí, aby mohl pokračovat k dalšímu kroku.

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

### <a name="create-the-second-virtual-machine"></a>Vytvořit druhý virtuální počítač

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

## <a name="confirm-access-to-storage-account"></a>Potvrďte volbu přístup k účtu úložiště

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVmPrivate* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Nahraďte `<publicIpAddress>` v následující příkaz, s veřejnou IP adresu vrácená z předchozí příkaz a potom zadejte následující příkaz: 

```powershell
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen a stažena do počítače. Otevřete soubor stažený rdp. Po zobrazení výzvy vyberte **Connect**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Je nutné vybrat **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. Vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat**, chcete-li pokračovat s připojením.

Na *myVmPrivate* virtuálních počítačů, mapování jednotky pomocí prostředí PowerShell Z sdílenou složku Azure file. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` hodnotami z zadaný nebo načíst v [vytvořit účet úložiště](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
Prostředí PowerShell vrátí výstup podobné výstupu v následujícím příkladu:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Úspěšně namapován na jednotku Z Azure sdílené složky.

Potvrďte, že virtuální počítač bez odchozí připojení k jakékoli jiné veřejné IP adresy:

```powershell
ping bing.com
```

Obdržíte bez odpovědi, protože skupina zabezpečení sítě přidružené k *privátní* podsíť neumožňuje odchozí přístup k veřejné IP adresy než adresy přiřazené ke službě Azure Storage.

Zavřete relaci vzdálené plochy k *myVmPrivate* virtuálních počítačů.

## <a name="confirm-access-is-denied-to-storage-account"></a>Potvrďte, že byl odepřen přístup k účtu úložiště

Získat veřejnou IP adresu *myVmPublic* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Nahraďte `<publicIpAddress>` v následující příkaz, s veřejnou IP adresu vrácená z předchozí příkaz a potom zadejte následující příkaz: 

```powershell
mstsc /v:<publicIpAddress>
```

Na *myVmPublic* virtuálních počítačů, snažit mapovat jednotku Z Azure sdílené složky. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` hodnotami z zadaný nebo načíst v [vytvořit účet úložiště](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

Byl odepřen přístup ke sdílené složce, a zobrazí se `New-PSDrive : Access is denied` chyby. Přístup je odepřen, protože *myVmPublic* virtuální počítač nasazen v *veřejné* podsítě. *Veřejné* podsíť nemá povoleno pro Azure Storage koncového bodu služby a účet úložiště pouze umožňuje přístup k síti z *privátní* podsíť, není *veřejné*podsítě.

Zavřete relaci vzdálené plochy k *myVmPublic* virtuálních počítačů.

Z počítače se pokusí o zobrazení sdílených složek v účtu úložiště pomocí následujícího příkazu:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Přístup byl odepřen, a zobrazí se *Get-AzureStorageFile: vzdálený server vrátil chybu: (403) zakázán. Kód stavu HTTP: 403 - HTTP chybová zpráva: Tento požadavek není autorizovaný k provedení této operace* chyby, protože počítač není v *privátní* podsíť *MyVirtualNetwork* virtuální síť.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další postup

V tomto článku jste povolili koncového bodu služby pro podsíť virtuální sítě. Jste zjistili, že koncových bodů služby se dá nastavit pro prostředky nasazené s více službami Azure. Vytvořili jste účet úložiště Azure a omezený přístup k síti k účtu úložiště na pouze prostředky v podsíti virtuální sítě. Další informace o koncových bodů služby najdete v tématu [přehled koncové body služby](virtual-network-service-endpoints-overview.md) a [spravovat podsítě](virtual-network-manage-subnet.md).

Pokud máte více virtuálních sítí ve vašem účtu, můžete k propojení dvou virtuálních sítí tak prostředky v rámci jedné virtuální sítě může komunikovat navzájem. Další informace, jak zjistit, [připojení virtuální sítě](tutorial-connect-virtual-networks-powershell.md).