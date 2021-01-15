---
title: Vytvoření virtuálního počítače s Windows pomocí akcelerovaných síťových služeb – Azure PowerShell
description: Vytvořte virtuální počítač s Windows s akcelerovanými síťovými službami, který výrazně vylepšit výkon sítě.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b3728a2b67529bab0900d42b3e39140d9329bc83
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223631"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Vytvoření virtuálního počítače s Windows a akcelerovanými síťovými službami pomocí Azure PowerShellu

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows s akcelerovanými síťovými službami.

> [!NOTE]
> Pokud chcete používat akcelerované síťové služby s virtuálním počítačem se systémem Linux, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými sítěmi](create-vm-accelerated-networking-cli.md).

Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což výrazně zlepšuje výkon sítě. Tato cesta s vysokým výkonem obchází hostitele z cesty k datům, což snižuje latenci, kolísání a využití procesoru u nejnáročnějších síťových úloh na podporovaných typech virtuálních počítačů. Následující diagram znázorňuje, jak dva virtuální počítače komunikují s a bez urychlení sítě:

![Komunikace mezi virtuálními počítači Azure a bez urychlení sítě](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez urychlení sítě musí všechny síťové přenosy na virtuálním počítači a z něj projít hostitelem a virtuálním přepínačem. Virtuální přepínač poskytuje všechna vynucení zásad, například skupiny zabezpečení sítě, seznamy řízení přístupu, izolaci a další síťové virtualizované služby pro síťový provoz.

> [!NOTE]
> Další informace o virtuálních přepínačích najdete v tématu [virtuální přepínač technologie Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Pomocí akcelerovaných síťových přenosů dorazí síťový provoz na síťové rozhraní (NIC) virtuálního počítače a pak se přepošle do virtuálního počítače. Všechny zásady sítě, které virtuální přepínač platí, se teď přesměrují a použijí v hardwaru. Vzhledem k tomu, že se zásada používá v hardwaru, může síťová karta přesměrování síťového provozu přímo do virtuálního počítače. Síťová karta obchází hostitele a virtuální přepínač, a přitom udržuje všechny zásady, které v hostiteli použili.

Výhody akcelerovaných sítí se vztahují jenom na virtuální počítač, na kterém je povolený. Nejlepších výsledků dosáhnete, když tuto funkci povolíte aspoň na dvou virtuálních počítačích, které jsou připojené ke stejné službě Azure Virtual Network. Při komunikaci mezi virtuálními sítěmi nebo místním připojením má tato funkce minimální dopad na celkovou latenci.

## <a name="benefits"></a>Výhody

- **Nižší latence/vyšší počet paketů za sekundu (PPS)**: odstranění virtuálního přepínače z cesty k datům odebere čas strávený v hostiteli pro zpracování zásad. Také zvyšuje počet paketů, které lze zpracovat v rámci virtuálního počítače.

- **Snížená kolísání**: zpracování virtuálních přepínačů závisí na množství zásad, které je třeba použít. Také závisí na zatížení procesoru, který provádí zpracování. Přesměrování vynucování zásad na hardware odstraní tuto variabilitu tím, že pakety doručí přímo virtuálnímu počítači. Při snižování zátěže dojde také k odstranění komunikace mezi virtuálními počítači, všemi přerušeními softwaru a všemi přepínači kontextu.

- **Snížení využití CPU**: obejít virtuální přepínač v hostiteli vede k menšímu využití procesoru při zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy

Následující distribuce jsou podporovány přímo z Galerie Azure:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů

Akcelerované sítě se podporují na většině účelových a výpočetních instancí s optimálním využitím dvou nebo více virtuálních procesorů (vCPU).  Podporované řady jsou: Dv2/DSv2 a F/FS.

Na instancích podporujících multithreading se podporuje akcelerované sítě pro instance virtuálních počítačů se čtyřmi nebo více vCPU. Podporované řady jsou: D/Dsv3, D/Dsv4, da/Dasv4, E/Esv3, EA/Easv4, Fsv2, Lsv2, MS/MMS a MS/Mmsv2.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti pro virtuální počítače s Windows v Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Vlastní image

Pokud používáte vlastní image a vaše image podporuje akcelerované síťové služby, ujistěte se, že máte požadované ovladače, které pracují s Mellanox ConnectX a ConnectX-4 LX síťových adaptérů v Azure.

### <a name="regions"></a>Oblasti

Akcelerované síťové služby jsou dostupné ve všech globálních oblastech Azure a cloudu Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení akcelerovaných síťových služeb na běžícím virtuálním počítači

Podporovaná velikost virtuálního počítače bez aktivovaného urychlení sítě může mít povolenou funkci jenom v případě, že se zastaví a nadělí.

### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Azure Resource Manager

Virtuální počítače (Classic) nejde nasadit s akcelerovanými síťovými službami.

## <a name="vm-creation-using-the-portal"></a>Vytvoření virtuálního počítače pomocí portálu

I když tento článek popisuje kroky pro vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí Azure PowerShell, můžete taky [pomocí Azure Portal vytvořit virtuální počítač](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , který umožňuje akcelerované síťové služby. Když vytváříte virtuální počítač na portálu, klikněte na stránce **vytvořit virtuální počítač** na kartu **síť** . Tato karta nabízí možnost **zrychlit síťové služby**. Pokud jste zvolili [podporovaný operační systém](#supported-operating-systems) a [Velikost virtuálního počítače](#supported-vm-instances), tato možnost je automaticky nastavená na **zapnuto**. V opačném případě je možnost nastavená na **vypnuto** a Azure zobrazí důvod, proč se nedá povolit.

> [!NOTE]
> Prostřednictvím portálu lze povolit pouze podporované operační systémy. Pokud používáte vlastní image a vaše image podporuje akcelerované síťové služby, vytvořte si virtuální počítač pomocí rozhraní příkazového řádku nebo PowerShellu. 

Po vytvoření virtuálního počítače můžete zkontrolovat, jestli je povolené urychlené síťové služby. Postupujte podle těchto pokynů:

1. Pokud chcete spravovat své virtuální počítače, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. V seznamu virtuální počítač vyberte nový virtuální počítač.

3. V řádku nabídek virtuálního počítače vyberte **síť**.

V informacích o síťovém rozhraní vedle označení **akcelerované síťové služby** se portál zobrazuje buď **zakázaný** , nebo **povolený** pro akcelerovaný stav sítě.

## <a name="vm-creation-using-powershell"></a>Vytvoření virtuálního počítače pomocí PowerShellu

Než budete pokračovat, nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) verze 1.0.0 nebo novější. Pokud chcete zjistit aktuálně nainstalovanou verzi, spusťte příkaz `Get-Module -ListAvailable Az` . Pokud potřebujete nainstalovat nebo upgradovat, nainstalujte nejnovější verzi modulu AZ Module z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az). V relaci PowerShellu se přihlaste k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *myNic* a *myVM*.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příkaz vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *centralus* :

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Vytvořte konfiguraci podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Následující příkaz vytvoří podsíť s názvem *mySubnet*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)s podsítí *mySubnet* .

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Vytvořte pravidlo skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) a přiřaďte jí pravidlo zabezpečení *Allow-RDP-All* . Kromě pravidla *Allow-RDP-All* obsahuje skupina zabezpečení sítě několik výchozích pravidel. Jedno výchozí pravidlo zakáže veškerý příchozí přístup z Internetu. Po vytvoření se do skupiny zabezpečení sítě přiřadí pravidlo *Povolení-RDP-All* , abyste se mohli vzdáleně připojit k virtuálnímu počítači.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Přidružte skupinu zabezpečení sítě k podsíti *mySubnet* pomocí [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Pravidlo ve skupině zabezpečení sítě je platné pro všechny prostředky nasazené v podsíti.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvoření síťového rozhraní s akcelerovanými síťovými službami

1. Vytvořte veřejnou IP adresu pomocí [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Veřejná IP adresa není nutná, pokud neplánujete přístup k virtuálnímu počítači z Internetu. Je ale potřeba provést kroky v tomto článku.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Vytvořte síťové rozhraní s možnostmi [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) s povolenými akcelerovanými síťovými sítěmi a přiřaďte veřejnou IP adresu k síťovému rozhraní. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v podsíti *MySubnet* virtuální sítě *myVnet* , kterému se přiřadí veřejná IP adresa *myPublicIp* :

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Vytvoření virtuálního počítače a připojení síťového rozhraní

1. Nastavte přihlašovací údaje k VIRTUÁLNÍmu počítači na `$cred` proměnnou pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential), který vás vyzve k přihlášení:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Definujte svůj virtuální počítač pomocí [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Následující příkaz definuje virtuální počítač s názvem *myVM* s velikostí virtuálního počítače, který podporuje akcelerované síťové služby (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    Seznam všech velikostí a vlastností virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Vytvořte zbytek konfigurace virtuálního počítače pomocí [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Následující příkaz vytvoří virtuální počítač s Windows serverem 2016:

    ```azurepowershell
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

4. Připojte síťové rozhraní, které jste dříve vytvořili pomocí [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Vytvořte virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Potvrzení, že se na virtuálním počítači s Windows nainstaluje adaptér sítě Ethernet

Po vytvoření virtuálního počítače v Azure se připojte k virtuálnímu počítači a ujistěte se, že je nainstalovaný řadič sítě Ethernet ve Windows.

1. Pokud chcete spravovat své virtuální počítače, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

2. V seznamu virtuální počítač vyberte nový virtuální počítač.

3. Pokud je **stav** virtuálního počítače uvedený jako **Vytvoření**, na stránce Přehled virtuálního počítače počkejte, až Azure dokončí vytváření virtuálního počítače. Po dokončení vytváření virtuálního počítače se **stav** změní na **spuštěno** .

4. Na panelu nástrojů přehled virtuálního počítače vyberte **připojit**  >  **RDP**  >  **Stáhnout soubor RDP**.

5. Otevřete soubor. RDP a přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali v části [Vytvoření virtuálního počítače a připojení k síťovému rozhraní](#create-a-vm-and-attach-the-network-interface) . Pokud jste se nikdy nepřipojili k VIRTUÁLNÍmu počítači s Windows v Azure, přečtěte si téma [připojení k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. Po zobrazení relace vzdálené plochy pro váš virtuální počítač klikněte pravým tlačítkem na tlačítko Start Windows a vyberte **Device Manager**.

7. V okně **Device Manager** rozbalte uzel **síťové adaptéry** .

8. Potvrďte, že se zobrazuje **adaptér Ethernet virtuální funkce Mellanox ConnectX-3** , jak je znázorněno na následujícím obrázku:

    ![Mellanox ConnectX-3 virtuální funkce Ethernet Adapter, nový síťový adaptér pro akcelerované síťové služby, Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

Pro váš virtuální počítač je teď povolená akcelerovaná síť.

> [!NOTE]
> Pokud se nespustí adaptér Mellanox, otevřete výzvu správce v relaci vzdálené plochy a zadejte následující příkaz:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit akcelerované síťové služby na stávajících virtuálních počítačích

Pokud jste vytvořili virtuální počítač bez urychlení sítě, můžete tuto funkci povolit na stávajícím virtuálním počítači. Virtuální počítač musí podporovat urychlené síťové služby, a to splněním následujících požadavků, které jsou také uvedeny výše:

* Virtuální počítač musí mít podporovanou velikost pro akcelerované síťové služby.
* Virtuální počítač musí být podporovaná Image Galerie Azure (a verze jádra pro Linux).
* Předtím, než povolíte urychlené síťové služby na libovolném síťovém adaptéru, je nutné zastavit nebo zrušit přidělení všech virtuálních počítačů ve skupině dostupnosti nebo sady škálování virtuálních počítačů.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Jednotlivé virtuální počítače a virtuální počítače ve skupině dostupnosti

1. Zastavit nebo zrušit přidělení virtuálního počítače nebo, pokud je skupina dostupnosti, všechny virtuální počítače v sadě:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Když vytváříte virtuální počítač samostatně bez skupiny dostupnosti, stačí zastavit nebo zrušit přidělení jednotlivých virtuálních počítačů, aby se povolily urychlené síťové služby. Pokud jste virtuální počítač vytvořili pomocí skupiny dostupnosti, musíte zastavit nebo zrušit přidělení všech virtuálních počítačů obsažených ve skupině dostupnosti předtím, než povolíte akcelerované síťové služby na kterékoli z síťových adaptérů, aby se virtuální počítače ukončily v clusteru, který podporuje urychlené síťové služby. Požadavek na zastavení nebo uvolnění není potřebný, pokud zakážete akcelerované síťové služby, protože clustery podporující urychlené sítě také fungují bez problémů se síťovými kartami, které nepoužívají akcelerované síťové služby.

2. Povolte akcelerované síťové rozhraní na síťové kartě vašeho virtuálního počítače:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Restartujte virtuální počítač nebo, pokud je ve skupině dostupnosti, všechny virtuální počítače v dané sadě a potvrďte, že je povolené urychlení sítě:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

Sada škálování virtuálního počítače je mírně odlišná, ale sleduje stejný pracovní postup.

1. Zastavte virtuální počítače:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Aktualizujte vlastnost akcelerované sítě v síťovém rozhraní:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Nastavte použité aktualizace na automatické, aby se změny hned vybraly:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > Sada škálování má upgrady virtuálních počítačů, které používají aktualizace pomocí tří různých nastavení: automatické, zavedení a ruční. V těchto pokynech je zásada nastavená na hodnotu automaticky, takže sada škálování vybere změny hned po restartování.

4. Restartujte sadu škálování:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Po restartování počkejte, až se upgrady dokončí. Po dokončení upgradu se virtuální funkce (VF) objeví uvnitř virtuálního počítače. Ujistěte se, že používáte podporovaný operační systém a velikost virtuálního počítače.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti stávajících virtuálních počítačů pomocí akcelerovaných síťových služeb

Pokud má virtuální počítač aktivované akcelerované síťové služby, můžete změnit jeho velikost jenom na virtuální počítač, který podporuje akcelerované síťové služby.  

Virtuální počítač s povolenými akcelerovanými síťovými službami se nedá změnit na instanci virtuálního počítače, která nepodporuje urychlení sítě pomocí operace změny velikosti. Místo toho změňte velikost jednoho z těchto virtuálních počítačů:

1. Zastavte nebo zrušte přidělení virtuálního počítače. V případě sady dostupnosti nebo sady škálování zastavte nebo zrušte přidělení všech virtuálních počítačů ve skupině dostupnosti nebo sadě škálování.

2. Zakáže akcelerované sítě v síťovém adaptéru virtuálního počítače. V případě sady dostupnosti nebo sady škálování zakažte akcelerované sítě na síťových kartách všech virtuálních počítačů ve skupině dostupnosti nebo sadě škálování.

3. Až zakážete urychlené síťové služby, přesuňte virtuální počítač, skupinu dostupnosti nebo sadu škálování na novou velikost, která nepodporuje urychlené síťové služby, a pak je restartujte.