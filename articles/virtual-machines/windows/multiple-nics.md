---
title: Vytváření a správa virtuálních počítačů s Windows v Azure, které používají více síťových karet
description: Naučte se vytvářet a spravovat virtuální počítače s Windows, které mají k němu připojené více síťových adaptérů pomocí šablon Azure PowerShell nebo Správce prostředků.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 2664d175818a6e29dcd3f704c6938987bae050cd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555172"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Vytvoření a Správa virtuálního počítače s Windows s více síťovými kartami
K virtuálním počítačům v Azure můžou být připojené několik síťových adaptérů (nic). Běžným scénářem je použití různých podsítí pro front-endové a back-endové připojení. K virtuálnímu počítači můžete přidružit více síťových adaptérů k několika podsítím, ale tyto podsítě se musí nacházet ve stejné virtuální síti (vNet). Tento článek podrobně popisuje, jak vytvořit virtuální počítač s připojenými více síťovými rozhraními. Naučíte se také, jak přidat nebo odebrat síťové karty z existujícího virtuálního počítače. Různé [velikosti virtuálních počítačů](../sizes.md) podporují proměnlivý počet síťových adaptérů, proto si odpovídajícím způsobem nasaďte velikost svého virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů jsou *myResourceGroup*, *myVnet* a *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Vytvoření virtuálního počítače s několika síťovými kartami
Nejdřív vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *EastUs* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Vytvořit virtuální síť a podsítě
Běžným scénářem je, že virtuální síť má dvě nebo víc podsítí. Jedna podsíť může být pro přenos front-endu, druhá pro přenos back-endu. Pokud se chcete připojit k oběma podsítím, použijte na svém VIRTUÁLNÍm počítači více síťových karet.

1. Definujte dvě podsítě virtuální sítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad definuje podsítě pro *mySubnetFrontEnd* a *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Vytvořte virtuální síť a podsítě pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Vytvoření více síťových karet
Vytvořte dvě síťové karty pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Připojte jednu síťovou kartu k front-endové podsíti a jednu síťovou kartu k back-endové podsíti. Následující příklad vytvoří síťové karty s názvem *myNic1* a *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Obvykle vytvoříte také [skupinu zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) pro filtrování síťového provozu do virtuálního počítače a [Nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md) pro distribuci provozu napříč několika virtuálními počítači.

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače
Teď začněte sestavovat konfiguraci virtuálních počítačů. Velikost každého virtuálního počítače má omezení celkového počtu síťových adaptérů, které můžete přidat do virtuálního počítače. Další informace najdete v tématu [velikosti virtuálních počítačů s Windows](../sizes.md).

1. Přihlašovací údaje pro virtuální počítač nastavte na `$cred` proměnnou následujícím způsobem:

    ```powershell
    $cred = Get-Credential
    ```

2. Definujte svůj virtuální počítač pomocí [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Následující příklad definuje virtuální počítač s názvem *myVM* a používá velikost virtuálního počítače, která podporuje více než dvě síťové karty (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Vytvořte zbytek konfigurace virtuálního počítače pomocí [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows serverem 2016:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Připojte dvě síťové karty, které jste dříve vytvořili pomocí [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Pomocí postupu v části [Konfigurace operačního systému pro více síťových adaptérů](#configure-guest-os-for-multiple-nics)přidejte trasy pro sekundární síťové karty do operačního systému.

## <a name="add-a-nic-to-an-existing-vm"></a>Přidání síťového rozhraní do existujícího virtuálního počítače
Pokud chcete přidat virtuální síťovou kartu k existujícímu virtuálnímu počítači, nasaďte virtuální počítač, přidejte virtuální síťovou kartu a potom spusťte virtuální počítač. Různé [velikosti virtuálních počítačů](../sizes.md) podporují proměnlivý počet síťových adaptérů, proto si odpovídajícím způsobem nasaďte velikost svého virtuálního počítače. V případě potřeby můžete [změnit velikost virtuálního počítače](resize-vm.md).

1. Zrušte přidělení virtuálního počítače pomocí [stop-AzVM](/powershell/module/az.compute/stop-azvm). Následující příklad zruší přidělení virtuálního počítače s názvem *myVM* v *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Získejte existující konfiguraci virtuálního počítače pomocí [Get-AzVm](/powershell/module/az.compute/get-azvm). Následující příklad načte informace pro virtuální počítač s názvem *myVM* v *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Následující příklad vytvoří virtuální síťovou kartu pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) s názvem *myNic3* , která je připojena k *mySubnetBackEnd*. Virtuální síťová karta se pak připojí k virtuálnímu počítači s názvem *myVM* v *myResourceGroup* pomocí [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primární virtuální síťové karty
    Jedna z síťových adaptérů na virtuálním počítači s více SÍŤOVÝmi kartami musí být primární. Pokud už jedna z existujících virtuálních síťových karet na virtuálním počítači je nastavená jako primární, můžete tento krok přeskočit. V následujícím příkladu se předpokládá, že na virtuálním počítači teď existují dvě virtuální síťové karty a chcete přidat první síťovou kartu ( `[0]` ) jako primární:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Spusťte virtuální počítač pomocí [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Pomocí postupu v části [Konfigurace operačního systému pro více síťových adaptérů](#configure-guest-os-for-multiple-nics)přidejte trasy pro sekundární síťové karty do operačního systému.

## <a name="remove-a-nic-from-an-existing-vm"></a>Odebrání síťového rozhraní z existujícího virtuálního počítače
Pokud chcete odebrat virtuální síťovou kartu z existujícího virtuálního počítače, oddělíte virtuální počítač, odeberte virtuální síťovou kartu a potom spusťte virtuální počítač.

1. Zrušte přidělení virtuálního počítače pomocí [stop-AzVM](/powershell/module/az.compute/stop-azvm). Následující příklad zruší přidělení virtuálního počítače s názvem *myVM* v *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Získejte existující konfiguraci virtuálního počítače pomocí [Get-AzVm](/powershell/module/az.compute/get-azvm). Následující příklad načte informace pro virtuální počítač s názvem *myVM* v *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Přečtěte si informace o odebrání síťových adaptérů pomocí [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface). Následující příklad získá informace o *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Odeberte síťovou kartu pomocí [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) a pak aktualizujte virtuální počítač pomocí [Update-AzVm](/powershell/module/az.compute/update-azvm). Následující příklad odebere *myNic3* , jak získali `$nicId` v předchozím kroku:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Spusťte virtuální počítač pomocí [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Vytvoření více síťových karet pomocí šablon
Šablony Azure Resource Manager poskytují způsob vytvoření více instancí prostředku během nasazování, například vytvoření více síťových karet. Správce prostředků šablony používají k definování vašeho prostředí deklarativní soubory JSON. Další informace najdete v tématu [přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md). K určení počtu instancí, které se mají vytvořit, můžete použít možnost *Kopírovat* :

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Další informace najdete v tématu [vytvoření více instancí pomocí *kopírování*](../../azure-resource-manager/templates/copy-resources.md). 

Můžete také použít `copyIndex()` pro připojení čísla k názvu prostředku. Pak můžete vytvořit *myNic1*, *MyNic2* a tak dále. Následující kód ukazuje příklad připojení hodnoty indexu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Můžete si přečíst kompletní příklad [vytváření více síťových karet pomocí šablon Správce prostředků](../../virtual-network/template-samples.md).

Pomocí postupu v části [Konfigurace operačního systému pro více síťových adaptérů](#configure-guest-os-for-multiple-nics)přidejte trasy pro sekundární síťové karty do operačního systému.

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaného operačního systému pro více síťových karet

Azure přiřadí výchozí bránu k prvnímu (primárnímu) síťovému rozhraní připojenému k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní však mohou komunikovat s prostředky mimo jejich podsíť, i když se postup pro povolení komunikace liší v různých operačních systémech.

1. Z příkazového řádku systému Windows spusťte `route print` příkaz, který vrátí výstup podobný následujícímu výstupu pro virtuální počítač se dvěma připojenými síťovými rozhraními:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    V tomto příkladu je **Microsoft Hyper-V síťový adaptér #4** (rozhraní 7) sekundární síťové rozhraní, ke kterému není přiřazená výchozí brána.

2. Z příkazového řádku spusťte příkaz, `ipconfig` abyste viděli, která IP adresa je přiřazená sekundárnímu síťovému rozhraní. V tomto příkladu je 192.168.2.4 přiřazen rozhraní 7. Pro sekundární síťové rozhraní se nevrátí žádná adresa výchozí brány.

3. Pokud chcete směrovat veškerý provoz určený pro adresy mimo podsíť sekundárního síťového rozhraní do brány pro podsíť, spusťte následující příkaz:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Adresa brány pro podsíť je první IP adresa (končící na 1) v rozsahu adres definovaném pro podsíť. Pokud nechcete směrovat veškerý provoz mimo podsíť, můžete místo toho přidat jednotlivé trasy k určitým cílům. Pokud například chcete směrovat provoz ze sekundárního síťového rozhraní do sítě 192.168.3.0, zadejte příkaz:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Pokud chcete potvrdit úspěšnou komunikaci s prostředkem v síti 192.168.3.0, zadejte do příkazového řádku 192.168.3.4 (192.168.2.4) následující příkaz:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Je možné, že budete muset otevřít protokol ICMP přes bránu Windows Firewall zařízení, které otestujete příkazem, pomocí následujícího příkazu:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Pokud chcete potvrdit, že se přidaná trasa nachází v tabulce směrování, zadejte `route print` příkaz, který vrátí výstup podobný následujícímu textu:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Trasa uvedená v části **Gateway** *je ve výchozím* nastavení trasa pro primární síťové rozhraní. Trasa s *192.168.2.1* pod **branou** je trasa, kterou jste přidali.

## <a name="next-steps"></a>Další kroky
Zkontrolujte [velikosti virtuálních počítačů s Windows](../sizes.md) , když se pokoušíte vytvořit virtuální počítač s více síťovými kartami. Věnujte pozornost maximálnímu počtu síťových adaptérů, které podporují jednotlivé velikosti virtuálních počítačů.