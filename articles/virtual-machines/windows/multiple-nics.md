---
title: Vytvářet a spravovat virtuální počítače Windows v Azure, použít několik síťových adaptérů | Microsoft Docs
description: Naučte se vytvářet a spravovat virtuální počítač Windows, který má několik síťových adaptérů připojený pomocí šablon Azure PowerShell nebo správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 776ae83990a7799102c69347196a72a68561ee6b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Vytvoření a Správa virtuálního počítače s Windows, který má několik síťových adaptérů
Virtuální počítače (VM) v Azure může mít několik virtuálních síťových karet (NIC) připojené k nim. Obvyklým scénářem je mít různé podsítě pro připojení front-end a back-end nebo síť vyhrazený pro řešení monitorování nebo zálohování. Tento článek popisuje, jak vytvořit virtuální počítač, který má několik síťových adaptérů, které jsou k němu připojen. Můžete také zjistěte, jak přidat nebo odebrat síťové adaptéry ze stávajícího virtuálního počítače. Různé [velikosti virtuálních počítačů](sizes.md) podporu různých počet síťových adaptérů, takže odpovídajícím způsobem upravit velikost virtuálního počítače.

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že máte [nejnovější verzi prostředí Azure PowerShell nainstalovaný a nakonfigurovaný](/powershell/azure/overview).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *Můjvp*.


## <a name="create-a-vm-with-multiple-nics"></a>Vytvoření virtuálního počítače s několika síťovými kartami
Nejprve vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *EastUs* umístění:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Vytvoření virtuální sítě a podsítě
Obvyklým scénářem je pro virtuální síť má dva nebo více podsítí. Jedna podsíť může být pro front-endu provoz, druhý pro přenosy back-end. Pro připojení k obě podsítě, potom použijte několik síťových adaptérů na vašem virtuálním počítači.

1. Definovat dvě podsítě virtuální sítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). V následujícím příkladu definuje podsítě pro *mySubnetFrontEnd* a *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Vytvoření virtuální sítě a podsítě s [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Vytvořit několik síťových adaptérů
Vytvořte dva síťové adaptéry se [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Jeden síťový adaptér a front-end podsítě a jeden síťový adaptér připojte k podsíti back-end. Následující příklad vytvoří síťové adaptéry s názvem *myNic1* a *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Obvykle můžete také vytvořit [skupinu zabezpečení sítě](../../virtual-network/security-overview.md) filtrování provozu sítě k virtuálnímu počítači a [nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md) provoz distribuovat mezi několika virtuálními počítači.

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače
Nyní spusťte Sestavit vaše konfigurace virtuálního počítače. Limit velikost každého virtuálního počítače je celkový počet síťových adaptérů, které můžete přidat k virtuálnímu počítači. Další informace najdete v tématu [velikosti virtuálních počítačů Windows](sizes.md).

1. Nastavte přihlašovací údaje virtuálních počítačů `$cred` proměnná následujícím způsobem:

    ```powershell
    $cred = Get-Credential
    ```

2. Zadejte virtuální počítač s [nové AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). V následujícím příkladu definuje virtuální počítač s názvem *Můjvp* a používá velikost virtuálního počítače, který podporuje více než dva síťové adaptéry (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Vytvoření zbytek konfiguraci virtuálních počítačů s [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) a [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Připojit dvěma síťovými adaptéry, které jste předtím vytvořili pomocí [přidat AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Vytvoření virtuálního počítače s [nové AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Přidání tras pro sekundární síťové adaptéry operačního systému pomocí kroků v [konfigurace operačního systému pro několik síťových adaptérů](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Přidat síťovou kartu k existující virtuální počítač
Pokud chcete přidat do existující virtuální počítač virtuální síťovou kartu, můžete zrušit přidělení virtuálního počítače, přidejte virtuální síťovou kartu a pak spusťte virtuální počítač. Různé [velikosti virtuálních počítačů](sizes.md) podporu různých počet síťových adaptérů, takže odpovídajícím způsobem upravit velikost virtuálního počítače. V případě potřeby můžete [změnit velikost virtuálního počítače](resize-vm.md).

1. Zrušit přidělení virtuálního počítače s [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). Následující příklad zruší přidělení virtuálního počítače s názvem *Můjvp* v *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Získat existující konfiguraci virtuálního počítače s [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). Následující příklad načte informace pro virtuální počítač s názvem *Můjvp* v *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Následující příklad vytvoří virtuální síťový adaptér s [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) s názvem *myNic3* připojená k *mySubnetBackEnd*. Poté virtuální síťový adaptér je připojen k virtuální počítač s názvem *Můjvp* v *myResourceGroup* s [přidat AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primární virtuální síťové karty
    Mezi síťovými adaptéry na virtuálním počítači více síťovými Kartami, je potřeba primární. Pokud jeden z existující virtuální síťové adaptéry na virtuálním počítači je již nastavena jako primární, můžete tento krok přeskočit. Následující příklad předpokládá, že dva virtuální síťové karty jsou teď na virtuální počítač a chcete přidat první síťový adaptér (`[0]`) jako primární:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Spusťte virtuální počítač s [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Přidání tras pro sekundární síťové adaptéry operačního systému pomocí kroků v [konfigurace operačního systému pro několik síťových adaptérů](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Odeberte síťový adaptér ze stávajícího virtuálního počítače
Odebrat virtuální síťový adaptér ze stávajícího virtuálního počítače, můžete zrušit přidělení virtuálního počítače, odeberte virtuálního síťového adaptéru, a pak spusťte virtuální počítač.

1. Zrušit přidělení virtuálního počítače s [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). Následující příklad zruší přidělení virtuálního počítače s názvem *Můjvp* v *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Získat existující konfiguraci virtuálního počítače s [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). Následující příklad načte informace pro virtuální počítač s názvem *Můjvp* v *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Získat informace o odebrání síťový adaptér s [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). Následující příklad získá informace *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Odeberte na síťový adaptér s [odebrat AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) a aktualizujte virtuální počítač s [aktualizace-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). Následující příklad odebere *myNic3* získány `$nicId` v předchozím kroku:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Spusťte virtuální počítač s [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Vytvořit několik síťových adaptérů se šablonami
Šablony Azure Resource Manageru poskytují způsob, jak vytvořit více instancí prostředku během nasazení, jako je například vytváření několik síťových adaptérů. Deklarativní soubory JSON šablony Resource Manageru použít k definování prostředí. Další informace najdete v tématu [přehled nástroje Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Můžete použít *kopie* k určení počtu instancí vytvořit:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Další informace najdete v tématu [vytvoření více instancí pomocí *kopie*](../../resource-group-create-multiple.md). 

Můžete také použít `copyIndex()` číslo připojit k názvu zdroje. Potom můžete vytvořit *myNic1*, *MyNic2* a tak dále. Následující kód ukazuje příklad připojením index hodnoty:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Kompletní příklad, jak si můžete přečíst [vytvoření několik síťových adaptérů pomocí šablony Resource Manageru](../../virtual-network/template-samples.md).

Přidání tras pro sekundární síťové adaptéry operačního systému pomocí kroků v [konfigurace operačního systému pro několik síťových adaptérů](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaný operační systém pro několik síťových adaptérů

Azure přiřadí výchozí bránu na první (primární) síťové rozhraní připojené k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní může, ale komunikovat s prostředky mimo jejich podsíť, když postup povolení komunikace se liší pro různé operační systémy.

1. Z příkazového řádku Windows, spusťte `route print` příkaz, který vrátí výstup podobný následujícímu výstupu pro virtuální počítač s dvě rozhraní připojené síti:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    V tomto příkladu **Microsoft Hyper-V síťový adaptér č. 4** (rozhraní 7) je sekundární síťové rozhraní, který nemá přiřazen výchozí bránu.

2. Z příkazového řádku, spusťte `ipconfig` příkazu zobrazte IP adresu, která je přiřazena k sekundární síťové rozhraní. V tomto příkladu je 192.168.2.4 přiřazená rozhraní 7. Pro sekundární síťové rozhraní je vrácena žádná adresu výchozí brány.

3. Směrovat všechny přenosy určené pro adresy mimo podsíť sekundární síťové rozhraní k bráně pro podsíť, spusťte následující příkaz:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Adresa brány podsítě je první IP adresa (končící na.1) do rozsahu adres, které jsou definované pro podsíť. Pokud nechcete, aby směrovat všechny přenosy mimo podsítě, můžete přidat jednotlivých tras místo toho k určitým příjemcům. Například, pokud chcete směrovat přenosy z sekundární síťové rozhraní 192.168.3.0 síť, zadejte příkaz:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Pro potvrzení úspěšné komunikace s k prostředku 192.168.3.0 sítě, například zadejte následující příkaz k otestování příkazem ping 192.168.3.4 pomocí rozhraní 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Budete muset otevřít ICMP přes bránu Windows firewall, zařízení, které jste otestováním pomocí následujícího příkazu:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Pokud chcete potvrdit, cesta přidala je v tabulce směrování, zadejte `route print` příkaz, který vrátí výstup podobný následujícímu:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Trasy označené *192.168.1.1* pod **brány**, je trasy, která je k dispozici ve výchozím nastavení pro primární síťové rozhraní. Trasa *192.168.2.1* pod **brány**, je trasy, které jste přidali.

## <a name="next-steps"></a>Další postup
Zkontrolujte [velikosti virtuálních počítačů Windows](sizes.md) při pokusu o vytvoření virtuálního počítače, který má několik síťových adaptérů. Věnujte pozornost maximální počet síťových adaptérů, které podporuje každý velikost virtuálního počítače. 


