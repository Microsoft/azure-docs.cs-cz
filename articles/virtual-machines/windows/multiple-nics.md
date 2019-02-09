---
title: Vytvoření a správa virtuálních počítačů Windows v Azure, použít několik síťových karet | Dokumentace Microsoftu
description: Zjistěte, jak vytvářet a spravovat virtuální počítač Windows s více síťovými kartami připojenými k němu pomocí šablon Resource Manageru nebo Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: cc4fb07874015112791ef2eaf9c39b31b690006c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978659"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Vytvoření a Správa virtuálního počítače Windows, který má více síťových rozhraní
Virtuální počítače (VM) v Azure může mít více adaptéry virtuální sítě (NIC) připojených k nim. Běžný scénář, kdy je, aby různé podsítě pro front-endu a back-end připojení k síti. Můžete přiřadit více síťových adaptérů na virtuálním počítači s několika podsítěmi, ale tyto podsítě musí nacházet ve stejné virtuální síti (vNet). Tento článek podrobně popisuje, jak vytvořit virtuální počítač s více síťovými kartami připojenými k němu. Také se dozvíte, jak k přidání nebo odebrání síťových rozhraní z existujícího virtuálního počítače. Různé [velikosti virtuálních počítačů](sizes.md) podporují různé počet síťových adaptérů, proto odpovídajícím způsobem upravit velikost virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup*, *myVnet*, a *myVM*.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-a-vm-with-multiple-nics"></a>Vytvoření virtuálního počítače s několika síťovými kartami
Nejprve vytvořte skupinu prostředků. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *EastUs* umístění:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Vytvoření virtuální sítě a podsítě
Běžný scénář, kdy je pro virtuální síť má dvě nebo více podsítí. Jedna podsíť může být pro front-endu provoz, druhé pro back-end provoz. Pro připojení k obě podsítě, pak použijte několik síťových adaptérů na virtuálním počítači.

1. Definovat dvě podsítě virtuální sítě s [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Následující příklad definuje podsítě pro *mySubnetFrontEnd* a *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Vytvoření virtuální sítě a podsítě s [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Vytvoření více síťových rozhraní
Vytvořit dva síťové adaptéry s [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface). Připojení k back endové podsítě jednu síťovou kartu pro front-endové podsítě a jednou síťovou KARTOU. Následující příklad vytvoří síťové adaptéry s názvem *myNic1* a *myNic2*:

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

Obvykle můžete také vytvořit [skupinu zabezpečení sítě](../../virtual-network/security-overview.md) filtrování provozu sítě k virtuálnímu počítači a [nástroj pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md) za účelem distribuce provozu napříč několika virtuálními počítači.

### <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače
Začněte vytvářet vaše konfigurace virtuálního počítače. Všechny velikosti virtuálních počítačů má limit pro celkový počet síťových adaptérů, které můžete přidat do virtuálního počítače. Další informace najdete v tématu [velikosti virtuálních počítačů s Windows](sizes.md).

1. Nastavte svoje přihlašovací údaje virtuálního počítače na `$cred` proměnné následujícím způsobem:

    ```powershell
    $cred = Get-Credential
    ```

2. Definování váš virtuální počítač s [nové AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig). Následující příklad definuje virtuální počítač s názvem *myVM* a používá velikost virtuálního počítače, který podporuje více než dva síťové adaptéry (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Vytvoření rest konfiguraci virtuálního počítače s [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) a [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows serverem 2016:

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

4. Připojit dva síťové adaptéry, které jste předtím vytvořili pomocí [přidat AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Vytvoření virtuálního počítače s [nové AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Přidání tras pro sekundární síťové karty pro operační systém podle postupu uvedeného v [konfigurace operačního systému pro několik síťových karet](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Přidat síťové rozhraní existujícímu virtuálnímu počítači
Chcete-li přidat virtuální síťovou kartu k existujícímu virtuálnímu počítači, uvolněte virtuální počítač, přidejte virtuální síťovou kartu a pak spusťte virtuální počítač. Různé [velikosti virtuálních počítačů](sizes.md) podporují různé počet síťových adaptérů, proto odpovídajícím způsobem upravit velikost virtuálního počítače. V případě potřeby můžete [změnit velikost virtuálního počítače](resize-vm.md).

1. Uvolněte virtuální počítač s [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM* v *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Získat existující konfiguraci virtuálního počítače pomocí [rutiny Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Následující příklad získá informace o virtuální počítač s názvem *myVM* v *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Následující příklad vytvoří virtuální síťovou kartu s [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) s názvem *myNic3* , který je připojen k *mySubnetBackEnd*. Virtuální síťová karta je pak připojené k virtuálnímu počítači s názvem *myVM* v *myResourceGroup* s [přidat AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface):

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
    Jeden ze síťových adaptérů na virtuálním počítači s více síťovými Kartami, musí být primární. Pokud některý existující virtuální síťové adaptéry na virtuálním počítači je již nastaven jako primární, můžete tento krok přeskočit. Následující příklad předpokládá, že dvě virtuální síťové adaptéry jsou teď k dispozici na virtuálním počítači a chcete přidat první síťové rozhraní (`[0]`) jako primární:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Spusťte virtuální počítač s [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Přidání tras pro sekundární síťové karty pro operační systém podle postupu uvedeného v [konfigurace operačního systému pro několik síťových karet](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Odstranit síťové rozhraní z existujícího virtuálního počítače
Odebrat virtuální síťovou kartu z existujícího virtuálního počítače, zrušit přidělení virtuálního počítače, odeberte virtuální síťovou kartu pak spusťte virtuální počítač.

1. Uvolněte virtuální počítač s [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm). V následujícím příkladu se uvolní virtuální počítač s názvem *myVM* v *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Získat existující konfiguraci virtuálního počítače pomocí [rutiny Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Následující příklad získá informace o virtuální počítač s názvem *myVM* v *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Získání informací o odebrat síťovou kartu s [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface). Následující příklad získá informace *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Odebrání síťového adaptéru s [odebrat AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmnetworkinterface) a pak aktualizujte virtuální počítač pomocí [rutiny Update-AzVm](https://docs.microsoft.com/powershell/module/az.compute/update-azvm). Následující příklad odebere *myNic3* získány `$nicId` v předchozím kroku:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Spusťte virtuální počítač s [Start-AzVm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Vytvořte několik síťových karet s využitím šablon
Šablony Azure Resource Manageru poskytnout způsob, jak vytvořit více instancí prostředku během nasazení, jako je vytvoření více síťových rozhraní. Šablony Resource Manageru pomocí deklarativní soubory JSON definovat vaše prostředí. Další informace najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md). Můžete použít *kopírování* k určení počtu instancí na vytvoření:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Další informace najdete v tématu [vytvoření víc instancí s použitím *kopírování*](../../resource-group-create-multiple.md). 

Můžete také použít `copyIndex()` připojte číslo k názvu prostředku. Potom můžete vytvořit *myNic1*, *MyNic2* a tak dále. Následující kód ukazuje příklad připojení hodnotu indexu:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Úplný příklad si můžete přečíst [vytvoření více síťových rozhraní pomocí šablon Resource Manageru](../../virtual-network/template-samples.md).

Přidání tras pro sekundární síťové karty pro operační systém podle postupu uvedeného v [konfigurace operačního systému pro několik síťových karet](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurace hostovaného operačního systému pro více síťových rozhraní

Azure přiřadí výchozí bránu první (primární) síťové rozhraní připojené k virtuálnímu počítači. Azure nepřiřazuje výchozí bránu dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní, ale komunikovat s prostředky mimo jejich podsíť, ale postup pro povolení komunikace se liší pro různé operační systémy.

1. Z příkazového řádku Windows, spusťte `route print` příkaz, který vrátí výstup podobný následujícímu výstupu pro virtuální počítač s dvě připojené síťové rozhraní:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    V tomto příkladu **Microsoft Hyper-V síťových adaptéru č. 4** (rozhraní 7) je sekundární síťové rozhraní, který nemá přiřazenou výchozí bránu.

2. Z příkazového řádku, spusťte `ipconfig` příkazu zobrazíte, které IP adresa se přiřadí k sekundárnímu síťovému rozhraní. V tomto příkladu je přiřazená 192.168.2.4 rozhraní 7. Pro sekundární síťové rozhraní je vrácena žádná adresa výchozí brány.

3. Pokud chcete směrovat veškerý provoz určený pro adresy mimo podsíť sekundárního síťového rozhraní k bráně pro podsíť, spusťte následující příkaz:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Adresy pro podsíť brány je první IP adresa (s koncovkou.1) v rozsahu adres, které jsou definovány pro podsíť. Pokud nechcete směrovat všechny přenosy mimo podsíť, můžete přidat jednotlivých tras místo toho k určitým příjemcům. Například, pokud chcete směrovat provoz ze sekundární síťové rozhraní 192.168.3.0 síť, zadejte příkaz:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Pro potvrzení úspěšné komunikace s prostředkem na 192.168.3.0 sítě, například zadejte následující příkaz pro odeslání příkazu ping 192.168.3.4 pomocí rozhraní 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Budete muset otevřít protokol ICMP přes bránu Windows firewall zařízení, které se příkazy ping pomocí následujícího příkazu:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Pokud chcete potvrdit, je přidání trasy ve směrovací tabulce, zadejte `route print` příkaz, který vrátí výstup podobný následujícímu textu:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Trasa s *192.168.1.1* pod **brány**, je trasy, která je k dispozici ve výchozím nastavení pro primární síťové rozhraní. Trasa s *192.168.2.1* pod **brány**, tras, které jste přidali.

## <a name="next-steps"></a>Další postup
Kontrola [velikosti virtuálních počítačů s Windows](sizes.md) když se snažíte vytvořit virtuální počítač, který má několik síťových karet. Věnujte pozornost maximální počet síťových adaptérů, které podporuje všechny velikosti virtuálních počítačů. 


