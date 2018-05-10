---
title: Vytvořte virtuální počítač Azure s Accelerated sítě | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem pomocí Accelerated sítě.
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
ms.openlocfilehash: de69cdf69f30639d048dccd7d433c86f6cb9db7b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Vytvoření virtuálního počítače s Windows pomocí Accelerated sítě

V tomto kurzu zjistěte, jak k vytvoření virtuálního počítače (VM) s Windows pomocí Accelerated sítě. Vytvoření virtuálního počítače s Linuxem pomocí Accelerated sítě naleznete v tématu [vytvoření virtuálního počítače s Linuxem pomocí Accelerated sítě](create-vm-accelerated-networking-cli.md). Zrychlený sítě umožňuje jeden kořenový vstupně-výstupních operací virtualizace (SR-IOV) na virtuální počítače, výrazně zlepšit sítě. Tato cesta vysoce výkonné obchází hostitel datapath, snižuje latence, zpoždění a využití procesoru pro použití s nejnáročnější zatížení sítě v podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dva virtuální počítače a bez Zrychlený sítě:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez Zrychlený sítě, musí procházet všechny síťové přenosy do/z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, například skupin zabezpečení sítě, seznamy řízení přístupu, izolace a dalším službám síťové virtualizované síťový provoz. Další informace o virtuální přepínače, přečtěte si [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx) článku.

S Zrychlený sítě síťový provoz dorazí na rozhraní sítě Virtuálního počítače (NIC) a předá ji virtuálního počítače. Všechny zásady sítě, které se vztahuje na virtuální přepínač se teď se sníženou zátěží a použít v hardwaru. Použití zásad v hardwaru umožňuje síťový adaptér přesměrovat provoz sítě přímo k virtuálnímu počítači, obcházení hostitele a virtuálního přepínače, při zachování všech zásad, které se použijí v hostiteli.

Výhody Zrychlený sítě se vztahují pouze na virtuálním počítači, který je zapnutá. Nejlepších výsledků dosáhnete je ideální pro povolení této funkce na alespoň dva virtuální počítače připojené ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojování místní, tato funkce má minimální dopad na celkové latence.

## <a name="benefits"></a>Výhody
* **Nižší latenci vyšší pakety za sekundu (pps):** odebráním virtuálního přepínače datapath odebere čas, který potřebují paketů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které lze zpracovat ve virtuálním počítači.
* **Snižuje zpoždění:** virtuální přepínač zpracování závisí na množství zásady, které je nutné použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad do hardwaru, odstraní tento variabilita tím, že doručování paketů přímo k virtuálnímu počítači, odebrání hostitele do komunikace virtuálních počítačů a všechny softwaru přerušení a kontext přepínače.
* **Snížení využití procesoru:** obcházení virtuální přepínač na hostiteli vede k menší využití procesoru při zpracování síťového provozu.

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-operating-systems"></a>Podporované operační systémy
Podporovány jsou následující distribuce předinstalované z Galerie Azure: 
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter** 

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Zrychlený sítě je podporována v nejvíce obecné účely a velikostí optimalizovaných výpočetní instance s 2 nebo více Vcpu.  Tyto podporované řady jsou: D/DSv2 a F nebo služby Fs

U instancí, které podporují Hyper-threadingem Accelerated sítě je podporována v instance virtuálních počítačů s 4 nebo více Vcpu. Jsou podporované řady: D/DSv3, E nebo ESv3, Fsv2 a Ms a Mms

Další informace o instance virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastí Azure a cloudu Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení Accelerated sítě na spuštění virtuálního počítače
Podporovaná velikost virtuálního počítače bez použití Zrychlený sítě povolené může mít pouze funkci povolit, pokud je zastavena a navrácena.

### <a name="deployment-through-azure-resource-manager"></a>Nasazení pomocí Azure Resource Manager
Virtuální počítače (klasické) nelze nasadit pomocí Accelerated sítě.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Vytvořte virtuální počítač Windows s Azure Zrychlený sítě

I když tento článek obsahuje kroky k vytvoření virtuálního počítače pomocí Zrychlený sítě pomocí prostředí Azure PowerShell, můžete také [vytvoření virtuálního počítače pomocí Zrychlený sítě pomocí portálu Azure](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu, v části **nastavení**, vyberte **povoleno**v části **Accelerated sítě**. Možnost povolit Zrychlený sítě nebude se zobrazovat na portálu, pokud jste vybrali [podporovaný operační systém](#supported-operating-systems) a [velikost virtuálního počítače](#supported-vm-instances). Po vytvoření virtuálního počítače, které potřebujete k dokončení podle pokynů v [Přesvědčte se ovladače je nainstalována v operačním systému](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) verze 5.1.1 nebo novější. Chcete-li najít aktuálně nainstalovanou verzi, spusťte `Get-Module -ListAvailable AzureRM`. Pokud potřebujete nainstalovat nebo upgradovat, nainstalujte nejnovější verzi modulu AzureRM z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureRM). V relaci prostředí PowerShell přihlásit k účtu Azure pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *myNic*, a *Můjvp*.

Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *centralus* umístění:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Nejprve vytvořte podsíť konfigurace s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). Následující příklad vytvoří podsíť s názvem *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Vytvoření virtuální sítě s [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), s *mySubnet* podsítě.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Nejprve vytvořte pravidlo skupiny zabezpečení sítě s [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
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

Vytvořit skupinu zabezpečení sítě s [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) a přiřaďte *povolit-RDP-All* pravidlo zabezpečení. Kromě *povolit-RDP-All* pravidla, skupinu zabezpečení sítě obsahuje několik výchozích pravidel. Zakáže jeden výchozí pravidlo přístupu pro všechny příchozí připojení z Internetu, které je důvod, proč *povolit-RDP-All* skupinu zabezpečení sítě je přiřazeno pravidlo, takže můžete vzdáleně připojit k virtuálnímu počítači po jeho vytvoření.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Přidružení skupiny zabezpečení sítě k *mySubnet* podsíť s [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Pravidla v této skupině zabezpečení sítě je platná pro všechny prostředky nasazené v podsíti.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvořte síťové rozhraní s Zrychlený sítě
Vytvořte veřejnou IP adresu pomocí rutiny [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Veřejná IP adresa není povinné, pokud neplánujete pro přístup k virtuálnímu počítači z Internetu, ale pokud chcete provést kroky v tomto článku, je potřeba.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Vytvoření síťové rozhraní s [New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) s accelerated sítě povolené a veřejnou IP adresu přiřadit síťové rozhraní. Následující příklad vytvoří rozhraní sítě s názvem *myNic* v *mySubnet* podsíť *myVnet* virtuální sítě a přiřadí *myPublicIp*  veřejnou IP adresu, která ho:

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Vytvoření virtuálního počítače

Nastavte přihlašovací údaje virtuálních počítačů `$cred` proměnné pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Nejprve definovat virtuální počítač s [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). V následujícím příkladu definuje virtuální počítač s názvem *Můjvp* s velikostí virtuálních počítačů, která podporuje Accelerated sítě (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Seznam všech velikostí virtuálních počítačů a vlastnosti najdete v tématu [velikosti virtuálních počítačů Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Vytvoření zbytek konfiguraci virtuálních počítačů s [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) a [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows Server 2016:

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

Připojit síťové rozhraní, který jste dříve vytvořili s [přidat AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Nakonec vytvořte virtuální počítač s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Zkontrolujte, zda že je instalován ovladač v operačním systému

Po vytvoření virtuálního počítače v Azure, připojení k virtuálnímu počítači a zkontrolujte, že je ovladač nainstalovaná v systému Windows.

1. Z internetového prohlížeče, otevřete Azure [portál](https://portal.azure.com) a přihlaste se pomocí účtu Azure.
2. Do pole, která obsahuje text *vyhledávání prostředků* v horní části portálu Azure, zadejte *Můjvp*. Když **Můjvp** se zobrazí ve výsledcích hledání klikněte na něj. Pokud **vytváření** se zobrazí v části **Connect** tlačítko Azure ještě nebyla dokončena vytváření virtuálního počítače. Klikněte na tlačítko **připojit** v levém horním rohu přehled až po už vidět **vytváření** pod **připojit** tlačítko.
3. Zadejte uživatelské jméno a heslo zadané v [vytvořit virtuální počítač](#create-the-virtual-machine). Pokud jste ještě nikdy nepřipojili k virtuální počítač s Windows v Azure, najdete v části [připojit k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Klikněte pravým tlačítkem na tlačítko Start systému Windows a klikněte na tlačítko **Správce zařízení**. Rozbalte **síťové adaptéry** uzlu. Potvrďte, že **Mellanox ConnectX 3 virtuální adaptér Ethernet funkce** se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Správce zařízení](./media/create-vm-accelerated-networking/device-manager.png)

Pro virtuální počítač je nyní k dispozici Zrychlený sítě.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit Accelerated sítě na existující virtuální počítače
Pokud jste vytvořili virtuální počítač bez Accelerated sítě, je možné povolit tuto funkci na existující virtuální počítač.  Virtuální počítač musí podporovat Accelerated sítě prostřednictvím splňuje následující požadavky, které jsou také uvedeny výše:

* Virtuální počítač musí být podporovaná velikost Accelerated sítě
* Virtuální počítač musí být podporovaná bitová kopie Galerie Azure (a verze jádra pro Linux)
* Všechny virtuální počítače v nastavení dostupnosti nebo VMSS musí být zastavena navrácena před povolením Accelerated sítě na všechny síťové adaptéry

### <a name="individual-vms--vms-in-an-availability-set"></a>Jednotlivé virtuální počítače a virtuální počítače ve skupině dostupnosti nastavena
Nejdřív zastavte nebo zrušit přidělení virtuálního počítače, nebo pokud skupiny dostupnosti, všechny virtuální počítače v sadě:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Důležité, prosím Poznámka: Pokud je virtuální počítač vytvořený jednotlivě, bez nastavení dostupnosti, můžete pouze muset zastavit nebo navrácení jednotlivých virtuálních počítačů povolit Accelerated sítě.  Pokud váš virtuální počítač byl vytvořen pomocí nastavení dostupnosti, všechny virtuální počítače, které jsou obsažené v sadě dostupnosti bude nutné zastavit navrácena před povolením Accelerated sítě na žádném z síťovými adaptéry. 

Po zastavení povolte Accelerated sítě na síťový adaptér virtuálního počítače:

```azurepowershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRmNetworkInterface
```

Restartujte váš virtuální počítač nebo, pokud v skupiny dostupnosti, všechny virtuální počítače v sadě a zkontrolujte, zda je povoleno Accelerated sítě: 

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale následuje témže pracovním postupu.  Nejdřív zastavte virtuální počítače:

```azurepowershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Jakmile jsou zastaveny virtuální počítače, aktualizujte vlastnost Accelerated sítě v části síťové rozhraní:

```azurepowershell
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Poznámka: VMSS prosím má upgrady virtuálních počítačů, které aktualizace pomocí tří různých nastavení automatického, postupného a ruční.  V těchto pokynech je zásada nastavená na hodnotu automaticky tak, aby VMSS vyzvedne, až bude změny okamžitě po restartování.  Nastavte ji na automatické, aby změny jsou okamžitě zachyceny: 

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Nakonec restartujte VMSS:

```azurepowershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Jednou budete restartovat, čekat na upgrade ukončíte, ale po dokončení, zobrazí se VF ve virtuálním počítači.  (Ujistěte se, že používáte podporovaná velikost operačního systému a virtuálních počítačů)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti existujících virtuálních počítačů pomocí Accelerated sítě

Virtuální počítače pomocí Accelerated sítě povolené jde změnit jenom pro virtuální počítače, které podporují Accelerated sítě.  

Velikost virtuálního počítače s Accelerated sítě povolené nelze změnit na instanci virtuálního počítače, který nepodporuje Accelerated sítě pomocí operace změny velikosti.  Místo toho ke změně velikosti jednu z těchto virtuálních počítačů: 

* Zastavení nebo Deallocate virtuálního počítače nebo pokud v sadě dostupnosti nebo VMSS, zastavení nebo navrácení všechny virtuální počítače v sadě nebo VMSS.
* Zrychlený sítě musí být zakázáno na síťový adaptér virtuálního počítače nebo pokud se v dostupnosti sady nebo VMSS, všechny virtuální počítače v sadě nebo VMSS.
* Jakmile Accelerated sítě je zakázaná, sada virtuálních počítačů nebo dostupnosti nebo VMSS lze přesunout do nové velikosti, která nepodporuje Accelerated sítě a restartování.  



