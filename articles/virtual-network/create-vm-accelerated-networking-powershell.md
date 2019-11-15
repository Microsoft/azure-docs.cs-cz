---
title: Vytvoření virtuálního počítače Azure s akcelerovanými síťovými službami – Azure PowerShell
description: Naučte se, jak vytvořit virtuální počítač se systémem Linux s akcelerovanými síťovými službami.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083698"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Vytvoření virtuálního počítače s Windows pomocí akcelerovaných síťových služeb pomocí Azure PowerShell

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Windows s akcelerovanými síťovými službami. Informace o vytvoření virtuálního počítače se systémem Linux s akcelerovanými sítěmi najdete v tématu [Vytvoření virtuálního počítače se systémem Linux s akcelerovanými síťovými](create-vm-accelerated-networking-cli.md)službami. Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což výrazně zlepšuje výkon sítě. Tato cesta s vysokým výkonem obchází hostitele z DataPath, snižuje latenci, kolísání a využití CPU a používá se u nejnáročnějších síťových úloh na podporovaných typech virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dvěma virtuálními počítači s a bez urychlení sítě:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez urychlení sítě musí všechny síťové přenosy na virtuálním počítači a z něj projít hostitelem a virtuálním přepínačem. Virtuální přepínač poskytuje všechna vynucení zásad, například skupiny zabezpečení sítě, seznamy řízení přístupu, izolaci a další síťové virtualizované služby pro síťový provoz. Další informace o virtuálních přepínačích najdete v tématu [virtualizace sítě Hyper-V a virtuální přepínač](https://technet.microsoft.com/library/jj945275.aspx).

V případě akcelerovaných síťových přenosů dorazí síťový provoz na síťové rozhraní (NIC) virtuálního počítače a pak se přepošle na virtuální počítač. Všechny zásady sítě, které virtuální přepínač platí, se teď přesměrují a použijí v hardwaru. Použití zásad v hardwaru umožňuje síťovému rozhraní přesměrovat síťový provoz přímo do virtuálního počítače, vynechá hostitele a virtuální přepínač a přitom zachová všechny zásady, které v hostiteli použili.

Výhody akcelerovaných sítí se vztahují jenom na virtuální počítač, na kterém je povolený. Pro dosažení nejlepších výsledků je ideální povolit tuto funkci aspoň na dvou virtuálních počítačích, které jsou připojené ke stejné službě Azure Virtual Network (VNet). Při komunikaci přes virtuální sítě nebo místní připojení má tato funkce minimální dopad na celkovou latenci.

## <a name="benefits"></a>Výhody
* **Nižší latence/vyšší počet paketů za sekundu (PPS):** Když odeberete virtuální přepínač z vlastnosti DataPath, dojde k odebrání nepotřebných paketů v hostiteli pro zpracování zásad a zvýšení počtu paketů, které se dají zpracovat v rámci virtuálního počítače.
* **Zkrácená kolísání:** Zpracování virtuálních přepínačů závisí na množství zásad, které je třeba použít, a na zatížení procesoru, který provádí zpracování. Přesměrování vynucení zásad na hardware odstraní tuto variabilitu tím, že doručí pakety přímo virtuálnímu počítači, odebere hostitele do komunikace virtuálních počítačů a všechna přerušení softwaru a přepínače kontextu.
* **Snížení využití CPU:** Vynechání virtuálního přepínače v hostiteli vede k menšímu využití procesoru při zpracování síťového provozu.

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure se podporují následující distribuce:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované sítě se podporují na většině účelových a výpočetních instancí optimalizovaných pro výpočty s 2 nebo více vCPU.  Tyto podporované řady: D/DSv2 a F/FS

Na instancích, které podporují multithreading, se zrychluje síť pro instance virtuálních počítačů se 4 nebo více vCPU. Podporované řady jsou: D/Dsv3, E/Esv3, Fsv2, Lsv2, MS/MMS a MS/Mmsv2.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Regions
K dispozici ve všech veřejných oblastech Azure a v cloudu Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení akcelerovaných síťových služeb na běžícím virtuálním počítači
Podporovaná velikost virtuálního počítače bez aktivovaného urychlení sítě může mít povolenou funkci jenom v případě, že je zastavená a navrácena.

### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Azure Resource Manager
Virtuální počítače (Classic) nejde nasadit s akcelerovanými síťovými službami.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače s Windows pomocí akcelerovaných síťových služeb Azure
## <a name="portal-creation"></a>Vytváření portálu
I když tento článek popisuje kroky pro vytvoření virtuálního počítače s akcelerovanou sítí pomocí Azure PowerShellu, můžete také [vytvořit virtuální počítač s akcelerovanými síťovými službami pomocí Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu klikněte v okně **vytvořit virtuální počítač** na kartu **síť** .  Na této kartě je možnost **zrychlit síťové služby**.  Pokud jste zvolili [podporovaný operační systém](#supported-operating-systems) a [Velikost virtuálního počítače](#supported-vm-instances), tato možnost se automaticky naplní na "zapnuto".  Pokud ne, naplní možnost "vypnuto" pro urychlené síťové služby a uvede uživateli důvod, proč není povolen.   
* *Poznámka:* Prostřednictvím portálu lze povolit pouze podporované operační systémy.  Pokud používáte vlastní image a vaše image podporuje akcelerované síťové služby, vytvořte si virtuální počítač pomocí rozhraní příkazového řádku nebo PowerShellu. 

Po vytvoření virtuálního počítače můžete potvrdit, že je povolené urychlení sítě, a to podle pokynů uvedených v části potvrzení, že je povolené urychlené síťové služby.

## <a name="powershell-creation"></a>Vytvoření PowerShellu
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) verze 1.0.0 nebo novější. Pokud chcete zjistit aktuálně nainstalovanou verzi, spusťte `Get-Module -ListAvailable Az`. Pokud potřebujete nainstalovat nebo upgradovat, nainstalujte nejnovější verzi modulu AZ Module z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az). V relaci PowerShellu se přihlaste k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *myNic*a *myVM*.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *centralus* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Nejdřív vytvořte konfiguraci podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Následující příklad vytvoří podsíť s názvem *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)s podsítí *mySubnet* .

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Nejdřív vytvořte pravidlo skupiny zabezpečení sítě pomocí [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

```powershell
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

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) a přiřaďte jí pravidlo zabezpečení *Allow-RDP-All* . Kromě pravidla *Allow-RDP-All* obsahuje skupina zabezpečení sítě několik výchozích pravidel. Jedno výchozí pravidlo zakáže veškerý příchozí přístup z Internetu, což znamená, že pravidlo *Povolit-RDP-All* je přiřazeno ke skupině zabezpečení sítě, abyste se mohli vzdáleně připojit k virtuálnímu počítači po jeho vytvoření.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Přidružte skupinu zabezpečení sítě k podsíti *mySubnet* pomocí [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Pravidlo ve skupině zabezpečení sítě je platné pro všechny prostředky nasazené v podsíti.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvoření síťového rozhraní s akcelerovanými síťovými službami
Vytvořte veřejnou IP adresu pomocí [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Veřejná IP adresa není povinná, pokud neplánujete přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku je to potřeba.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Vytvořte síťové rozhraní pomocí [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) s povolenými akcelerovanými síťovými sítěmi a přiřaďte veřejnou IP adresu k síťovému rozhraní. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v podsíti *MySubnet* virtuální sítě *myVnet* a přiřadí mu veřejnou IP adresu *myPublicIp* :

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Nastavte přihlašovací údaje k VIRTUÁLNÍmu počítači na `$cred`ovou proměnnou pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Nejdřív definujte svůj virtuální počítač pomocí [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Následující příklad definuje virtuální počítač s názvem *myVM* s velikostí virtuálního počítače, který podporuje akcelerované síťové služby (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Seznam všech velikostí a vlastností virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Vytvořte zbytek konfigurace virtuálního počítače pomocí [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows serverem 2016:

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

Připojte síťové rozhraní, které jste dříve vytvořili pomocí [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Nakonec vytvořte virtuální počítač pomocí příkazu [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Potvrďte, že je ovladač nainstalovaný v operačním systému.

Po vytvoření virtuálního počítače v Azure se připojte k virtuálnímu počítači a ujistěte se, že je ovladač nainstalovaný v systému Windows.

1. V internetovém prohlížeči otevřete Azure [Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Do pole, které obsahuje *prostředky vyhledávání* textu v horní části Azure Portal zadejte *myVm*. Pokud se ve výsledcích hledání zobrazí **myVm** , klikněte na něj. Pokud se **vytváření** zobrazuje pod tlačítkem **připojit** , Azure ještě nedokončil vytváření virtuálního počítače. V levém horním rohu přehledu klikněte na **připojit** až po zobrazení **vytváření** pod tlačítkem **připojit** .
3. Zadejte uživatelské jméno a heslo, které jste zadali při [vytváření virtuálního počítače](#create-the-virtual-machine). Pokud jste se nikdy nepřipojili k VIRTUÁLNÍmu počítači s Windows v Azure, přečtěte si téma [připojení k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Klikněte pravým tlačítkem myši na tlačítko Start v systému Windows a klikněte na příkaz **Device Manager**. Rozbalte uzel **síťové adaptéry** . Potvrďte, že se zobrazuje **adaptér Ethernet virtuální funkce Mellanox ConnectX-3** , jak je znázorněno na následujícím obrázku:

    ![Device Manager](./media/create-vm-accelerated-networking/device-manager.png)

Pro váš virtuální počítač je teď povolená akcelerovaná síť.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit akcelerované síťové služby na stávajících virtuálních počítačích
Pokud jste vytvořili virtuální počítač bez urychlení sítě, je možné tuto funkci povolit na stávajícím virtuálním počítači.  Virtuální počítač musí podporovat urychlené síťové služby, a to splněním následujících požadavků, které jsou také uvedené výše:

* Virtuální počítač musí mít podporovanou velikost pro akcelerované síťové služby.
* Virtuální počítač musí být podporovaná Image Galerie Azure (a verze jádra pro Linux).
* Aby bylo možné zrychlit síťové rozhraní na libovolné síťové kartě, musí být všechny virtuální počítače ve skupině dostupnosti nebo VMSS zastavené nebo navrácené.

### <a name="individual-vms--vms-in-an-availability-set"></a>Jednotlivé virtuální počítače & virtuálních počítačů ve skupině dostupnosti
Nejdřív zastavte nebo zrušte přidělení virtuálního počítače, nebo pokud je nastavená Skupina dostupnosti, všechny virtuální počítače v sadě:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Důležité: Pokud jste virtuální počítač vytvořili samostatně bez skupiny dostupnosti, stačí zastavit nebo zrušit přidělení jednotlivých virtuálních počítačů, aby se povolily urychlené síťové služby.  Pokud byl virtuální počítač vytvořen pomocí skupiny dostupnosti, je nutné zastavit nebo zrušit přidělení všech virtuálních počítačů, které jsou obsaženy ve skupině dostupnosti, než povolíte urychlené síťové rozhraní na kterékoli síťové karty. 

Po zastavení povolte akcelerované síťové rozhraní pro virtuální počítač:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Restartujte virtuální počítač nebo, pokud je ve skupině dostupnosti, všechny virtuální počítače v dané sadě a potvrďte, že je povolené urychlení sítě:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí se stejným pracovním postupem.  Nejdřív zastavte virtuální počítače:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po zastavení virtuálních počítačů aktualizujte vlastnost akcelerované sítě v síťovém rozhraní:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Upozorňujeme, že VMSS má upgrady virtuálních počítačů, které používají aktualizace, pomocí tří různých nastavení, automatických, ručních zavádění a ručních.  V těchto pokynech je zásada nastavená na hodnotu automaticky, aby VMSS po restartování vybrala změny hned.  Nastavení na automatické, aby se změny hned vybraly:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Nakonec restartujte VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po restartování počkejte, až se upgrady dokončí, ale po dokončení se VF zobrazí ve virtuálním počítači.  (Ujistěte se prosím, že používáte podporovaný operační systém a velikost virtuálního počítače.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti stávajících virtuálních počítačů pomocí akcelerovaných síťových služeb

Virtuální počítače s povolenými akcelerovanými síťovými službami se můžou měnit jenom na virtuální počítače, které podporují urychlené síťové služby.  

Virtuální počítač s povolenými akcelerovanými síťovými službami se nedá změnit na instanci virtuálního počítače, která nepodporuje urychlení sítě pomocí operace změny velikosti.  Místo toho změňte velikost jednoho z těchto virtuálních počítačů:

* Zastavte nebo zrušte přidělení virtuálního počítače, nebo pokud je ve skupině dostupnosti/VMSS, zastavte nebo zrušte přidělení všech virtuálních počítačů v set/VMSS.
* Akcelerované síťové služby musí být zakázané na síťové kartě virtuálního počítače, nebo pokud jsou v sadě dostupnosti/VMSS všechny virtuální počítače v set/VMSS.
* Jakmile budou urychlené síťové sítě zakázané, můžete virtuální počítač/sadu dostupnosti/VMSS přesunout na novou velikost, která nepodporuje urychlené síťové a restarty.
