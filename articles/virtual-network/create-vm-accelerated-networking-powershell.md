---
title: Vytvoření virtuálního počítače Azure s Akcelerovanými síťovými službami | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami.
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
ms.openlocfilehash: 3ba7e8129d577faa87544f8feded51a14559eb51
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435528"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Vytvořte virtuální počítač Windows s Akcelerovanými síťovými službami

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač Windows (VM) s Akcelerovanými síťovými službami. Vytvoření virtuálního počítače s Linuxem s Akcelerovanými síťovými službami, najdete v tématu [vytvoření virtuálního počítače s Linuxem s Akcelerovanými síťovými službami](create-vm-accelerated-networking-cli.md). Akcelerované síťové služby umožňuje jeden kořenový vstupně-výstupních operací virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, výrazně zlepšit sítě. Tato cesta výkonné obchází hostitele z datapath, snížení latence, zpoždění a využití procesoru pro použití s nejnáročnějších úloh sítě na podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikace mezi dvěma virtuálními počítači a nemusíte akcelerované síťové služby:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez akcelerované síťové služby, musí procházet veškerý síťový provoz do a z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, jako jsou skupiny zabezpečení sítě, seznamy řízení přístupu, izolace a jiné síťové virtualizované služby pro síťový provoz. Další informace o virtuální přepínače, [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx) článku.

S akcelerovanými síťovými službami, síťový provoz dorazí na síťové rozhraní Virtuálního počítače (NIC) a pak se předávají do virtuálního počítače. Všechny zásady sítě, které se vztahuje virtuální přepínač se teď se sníženou zátěží a použít v hardwaru. Použití zásad v hardwaru umožňuje síťovou kartu pro provoz sítě přímo k virtuálnímu počítači, vynechání hostitele a virtuálního přepínače, při zachování všech zásad, který se použije na hostiteli.

Výhody akcelerované síťové služby se vztahují jenom na virtuální počítač, který je zapnutá. Pro nejlepší výsledky je ideální k povolení této funkce na alespoň dva virtuální počítače připojené ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojení v místním, tato funkce má minimální dopad na celkové latenci.

## <a name="benefits"></a>Výhody
* **Nižší latenci za vyšší pakety za sekundu (pps):** Odebrání virtuálního přepínače datapath odebere čas, kdy pakety výdajů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které mohou být zpracovány v tomto virtuálním počítači.
* **Zmenšit zpoždění:** Virtuální přepínač zpracování závisí na množství zásady, které je potřeba použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad hardwaru odebere této variabilitě poskytováním pakety přímo k virtuálnímu počítači, odebrání hostitele tak, aby komunikace virtuálních počítačů a všechny softwaru přerušení a přepnutí kontextu.
* **Snížení využití procesoru:** Obcházení virtuální přepínač na hostiteli vede k méně využití procesoru pro zpracování síťového provozu.

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-operating-systems"></a>Podporované operační systémy
Připravených v galerii Azure jsou podporovány následující distribuce: 
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter** 

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované síťové služby se podporuje na nejvíce obecné účely a velikostí optimalizovaných pro výpočetní instance s 2 nebo více virtuálních procesorů.  Tyto podporované řady jsou: D/DSv2 a F/Fs

U instancí, které podporují hyperthreadingem Akcelerovanými síťovými službami se podporuje na instancích virtuálních počítačů s 4 nebo více virtuálních procesorů. Podporované řady jsou: D/DSv3, E nebo ESv3, Fsv2 a Ms a Mms

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastech Azure a cloudu Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení Akcelerovanými síťovými službami spuštěného virtuálního počítače
Podporované velikosti virtuálního počítače bez povolenými akcelerovanými síťovými službami může mít jenom funkce povolena, když se zastaví a uvolní.

### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Azure Resource Manageru
Nelze nasadit virtuální počítače (klasické) s Akcelerovanými síťovými službami.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače Windows s Akcelerovanými síťovými službami Azure

Přestože tento článek popisuje kroky k vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí Azure Powershellu, můžete také [vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí webu Azure portal](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu v části **nastavení**vyberte **povoleno**v části **akcelerované síťové služby**. Možnost povolit akcelerované síťové služby nezobrazí na portálu, pokud jste vybrali [s podporovaným operačním systémem](#supported-operating-systems) a [velikost virtuálního počítače](#supported-vm-instances). Po vytvoření virtuálního počítače, které potřebujete k dokončení podle pokynů v [Potvrdit instalaci ovladače v operačním systému](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte [prostředí Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) verze 5.1.1 nebo novější. Aktuálně nainstalovanou verzi zjistíte spuštěním `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, nainstalujte nejnovější verzi modulu AzureRM z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureRM). V relaci Powershellu připojte se k účtu Azure pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých *myResourceGroup*, *myNic*, a *myVM*.

Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *centralus* umístění:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Nejprve vytvořte konfiguraci podsítě pomocí [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). Následující příklad vytvoří podsíť s názvem *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Vytvoření virtuální sítě s [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), se *mySubnet* podsítě.

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

Vytvořit skupinu zabezpečení sítě s [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) a přiřaďte *povolit RDP všech* pravidlo zabezpečení, které ho. Kromě *povolit RDP všech* pravidlo skupiny zabezpečení sítě obsahuje několik výchozích pravidel. Zakáže jeden výchozí pravidlo přístupu pro všechna příchozí připojení z Internetu, což je důvod, proč *povolit RDP všech* pravidlo je přiřazen ke skupině zabezpečení sítě tak, aby můžete vzdáleně připojit k virtuálnímu počítači po jeho vytvoření.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Přidružení skupiny zabezpečení sítě k *mySubnet* podsíť s [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Pravidlo skupiny zabezpečení sítě platí pro všechny prostředky nasazené v podsíti.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvořte síťové rozhraní s akcelerovanými síťovými službami
Vytvořte veřejnou IP adresu pomocí rutiny [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Veřejná IP adresa není povinné, když se nechystáte pro přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku, je povinný.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Vytvořte síťové rozhraní s [New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) s akcelerovanými síťovými povolené službami a přiřazovat veřejné IP adresy k síťovému rozhraní. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v *mySubnet* podsíti *myVnet* virtuální sítě a přiřadí *myPublicIp*  k němu veřejnou IP adresu:

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

Nastavte svoje přihlašovací údaje virtuálního počítače na `$cred` proměnné pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Nejprve definujte váš virtuální počítač s [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Následující příklad definuje virtuální počítač s názvem *myVM* s velikostí virtuálních počítačů, která podporuje akcelerované síťové (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Seznam všech velikostí virtuálních počítačů a vlastnosti najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Vytvoření rest konfiguraci virtuálního počítače s [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) a [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows serverem 2016:

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

Připojit síťové rozhraní, kterou jste vytvořili pomocí [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Nakonec vytvořte virtuální počítač s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Potvrďte, že instalaci ovladače v operačním systému

Po vytvoření virtuálního počítače v Azure, připojte se k virtuálnímu počítači a zkontrolujte, zda je ovladač nainstalovány ve Windows.

1. V internetovém prohlížeči otevřete Azure [portál](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Do pole, které obsahuje text *vyhledat prostředky* v horní části stránky na webu Azure portal, zadejte *myVm*. Když **myVm** se zobrazí ve výsledcích hledání klikněte na něj. Pokud **vytváření** je zobrazen pod **připojit** tlačítko Azure ještě nebyla dokončena vytváření virtuálního počítače. Klikněte na tlačítko **připojit** v levém horním rohu přehledu až po již neuvidí **vytváření** pod **připojit** tlačítko.
3. Zadejte uživatelské jméno a heslo, které jste zadali v [vytvořit virtuální počítač](#create-the-virtual-machine). Pokud jste ještě nikdy nepřipojili k virtuálnímu počítači s Windows v Azure, přečtěte si téma [připojit k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Klikněte pravým tlačítkem na tlačítko Windows Start a klikněte na tlačítko **Správce zařízení**. Rozbalte **síťové adaptéry** uzlu. Ujistěte se, že **ethernetový adaptér Mellanox ConnectX-3 virtuální funkce** se zobrazí, jak je znázorněno na následujícím obrázku:

    ![Správce zařízení](./media/create-vm-accelerated-networking/device-manager.png)

Pro váš virtuální počítač je nyní k dispozici akcelerované síťové služby.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit akcelerované síťové na stávajících virtuálních počítačů
Pokud jste vytvořili virtuální počítač bez Akcelerovanými síťovými službami, je možné povolit tuto funkci na existující virtuální počítač.  Virtuální počítač musí podporovat Akcelerovanými síťovými službami při splnění následujících požadavků, které jsou také uvedené výše:

* Virtuální počítač musí být podporovaná velikost pro Akcelerovanými síťovými službami
* Virtuální počítač musí být podporované image z Galerie Azure (a verze jádra pro Linux)
* Všechny virtuální počítače ve skupině dostupnosti nebo VMSS musí být zastaveno a před povolením Akcelerovanými síťovými službami na libovolné síťové rozhraní

### <a name="individual-vms--vms-in-an-availability-set"></a>Nastavení jednotlivých virtuálních počítačů a virtuálních počítačů ve skupině dostupnosti
Nejprve zastavit/zrušit přidělení virtuálního počítače nebo v případě dostupnosti, všechny virtuální počítače v sadě:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Důležité, prosím Poznámka: Pokud se váš virtuální počítač jednotlivě, vytvořil bez skupinu dostupnosti, můžete pouze muset zastavit/uvolnit konkrétního virtuálního počítače povolit Akcelerovanými síťovými službami.  Pokud váš virtuální počítač byl vytvořen pomocí nastavení dostupnosti, všechny virtuální počítače, které jsou obsaženy ve skupině dostupnosti bude potřeba zastavit/uvolnit před povolením Akcelerovanými síťovými službami na libovolné síťové rozhraní. 

Po zastavení povolte akcelerované síťové na síťovém rozhraní virtuálního počítače:

```azurepowershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRmNetworkInterface
```

Restartování vašeho virtuálního počítače nebo, pokud do skupiny dostupnosti, všechny virtuální počítače v sadě a potvrďte, že je povoleno Akcelerovanými síťovými službami: 

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí stejného pracovního postupu.  Nejdřív zastavte virtuální počítače:

```azurepowershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Jakmile se virtuální počítače se zastaví, umožňuje aktualizujte vlastnost Akcelerovanými síťovými službami v části síťové rozhraní:

```azurepowershell
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Poznámka: VMSS prosím má upgrady virtuálního počítače, které aktualizace pomocí tří různých nastavení automatické, Hromadná a ruční.  V těchto pokynech je zásada nastavená na hodnotu automaticky tak, aby VMSS vyzvedne, až bude změny okamžitě po restartování.  Nastavit ho na hodnotu automaticky, aby změny okamžitě, vyberou se: 

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

A konečně restartujte VMSS:

```azurepowershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

Jednou můžete znovu, počkejte upgrady dokončete ale po dokončení se zobrazí VF ve virtuálním počítači.  (Ujistěte se prosím, že používáte podporovanou velikost operačního systému a virtuálního počítače)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti stávajících virtuálních počítačů s Akcelerovanými síťovými službami

Virtuální počítače s Akcelerovanými síťovými službami povoleno pouze niž lze nastavit virtuální počítače, které podporují Akcelerovanými síťovými službami.  

Nelze změnit velikost virtuálního počítače s Akcelerovanými síťovými službami povolené k instanci virtuálního počítače, který nepodporuje Akcelerovanými síťovými službami pomocí operace změny velikosti.  Místo toho chcete změnit velikost jeden z těchto virtuálních počítačů: 

* Zastavení nebo zrušení přidělení virtuálního počítače nebo pokud v sadě dostupnosti/VMSS, zastavit/uvolnit všechny virtuální počítače v sadě/škálovací sady.
* Akcelerované síťové služby, musí se zakázat na síťovém rozhraní virtuálního počítače nebo při použití ve dostupnost sady nebo škálovací sada, všechny virtuální počítače v sadě/škálovací sady.
* Jakmile Akcelerovanými síťovými službami je zakázaná, sady nebo škálovací sada virtuálních počítačů a dostupnost lze přesunout do novou velikost, která nepodporuje Akcelerovanými síťovými službami a restartovat.  



