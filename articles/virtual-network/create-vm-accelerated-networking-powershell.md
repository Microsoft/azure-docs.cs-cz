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
ms.openlocfilehash: ef6086afa17f1ab864d70678a6da6df2a78e0c16
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190275"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Vytvořte virtuální počítač Windows s Akcelerovanými síťovými službami

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač Windows (VM) s Akcelerovanými síťovými službami. Vytvoření virtuálního počítače s Linuxem s Akcelerovanými síťovými službami, najdete v tématu [vytvoření virtuálního počítače s Linuxem s Akcelerovanými síťovými službami](create-vm-accelerated-networking-cli.md). Akcelerované síťové služby umožňuje jeden kořenový vstupně-výstupních operací virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, výrazně zlepšit sítě. Tato cesta výkonné obchází hostitele z datapath, snížení latence, zpoždění a využití procesoru pro použití s nejnáročnějších úloh sítě na podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikace mezi dvěma virtuálními počítači a nemusíte akcelerované síťové služby:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez akcelerované síťové služby, musí procházet veškerý síťový provoz do a z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, jako jsou skupiny zabezpečení sítě, seznamy řízení přístupu, izolace a jiné síťové virtualizované služby pro síťový provoz. Další informace o virtuální přepínače najdete v tématu [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx).

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

U instancí, které podporují hyperthreadingem Akcelerovanými síťovými službami se podporuje na instancích virtuálních počítačů s 4 nebo více virtuálních procesorů. Podporované řady jsou: D/Dsv3, E nebo Esv3, Fsv2, Lsv2, Ms a Mms a Ms/Mmsv2.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastech Azure a cloudu Azure Government.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení Akcelerovanými síťovými službami spuštěného virtuálního počítače
Podporované velikosti virtuálního počítače bez povolenými akcelerovanými síťovými službami může mít jenom funkce povolena, když se zastaví a uvolní.

### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Azure Resource Manageru
Nelze nasadit virtuální počítače (klasické) s Akcelerovanými síťovými službami.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače Windows s Akcelerovanými síťovými službami Azure
## <a name="portal-creation"></a>Vytvoření portálu
Přestože tento článek popisuje kroky k vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí Azure Powershellu, můžete také [vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí webu Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu v **vytvoření virtuálního počítače** okno, vyberte **sítě** kartu.  Na této kartě je možné pro **akcelerované síťové služby**.  Pokud jste vybrali [s podporovaným operačním systémem](#supported-operating-systems) a [velikost virtuálního počítače](#supported-vm-instances), tato možnost se automaticky vyplní pro "Na".  Pokud ne, bude naplnit možnost "Off" pro Akcelerovanými síťovými službami a uživateli přidělit důvod, proč není povolený.   
* *Poznámka:* Pouze podporované operační systémy je možné povolit prostřednictvím portálu.  Pokud používáte vlastní image a image podporuje Akcelerovanými síťovými službami, vytvořte prosím virtuální počítač použijte Powershell nebo rozhraní příkazového řádku. 

Po vytvoření virtuálního počítače můžete ověřit, že Akcelerovanými síťovými službami je povoleno podle pokynů v potvrďte, že akcelerovanými síťovými službami je povolená.

## <a name="powershell-creation"></a>Vytvoření prostředí PowerShell
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-az-ps) verze 1.0.0 nebo novějším. Aktuálně nainstalovanou verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, nainstalujte nejnovější verzi modulu Az z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az). V relaci Powershellu připojte se k účtu Azure pomocí [připojit AzAccount](/powershell/module/az.accounts/connect-azaccount).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých *myResourceGroup*, *myNic*, a *myVM*.

Vytvořte skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *centralus* umístění:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Nejprve vytvořte konfiguraci podsítě pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Následující příklad vytvoří podsíť s názvem *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Vytvoření virtuální sítě s [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), se *mySubnet* podsítě.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Nejprve vytvořte pravidlo skupiny zabezpečení sítě s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) a přiřadit *povolit RDP všech* pravidlo zabezpečení, které ho. Kromě *povolit RDP všech* pravidlo skupiny zabezpečení sítě obsahuje několik výchozích pravidel. Zakáže jeden výchozí pravidlo přístupu pro všechna příchozí připojení z Internetu, což je důvod, proč *povolit RDP všech* pravidlo je přiřazen ke skupině zabezpečení sítě tak, aby můžete vzdáleně připojit k virtuálnímu počítači po jeho vytvoření.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Přidružení skupiny zabezpečení sítě k *mySubnet* podsíť s [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Pravidlo skupiny zabezpečení sítě platí pro všechny prostředky nasazené v podsíti.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvořte síťové rozhraní s akcelerovanými síťovými službami
Vytvoření veřejné IP adresy s [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Veřejná IP adresa není povinné, když se nechystáte pro přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku, je povinný.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Vytvořte síťové rozhraní s [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) s akcelerovanými síťovými povolené službami a přiřazovat veřejné IP adresy k síťovému rozhraní. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v *mySubnet* podsíti *myVnet* virtuální sítě a přiřadí *myPublicIp*  k němu veřejnou IP adresu:

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

Nastavte svoje přihlašovací údaje virtuálního počítače na `$cred` proměnné pomocí [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Nejprve definujte váš virtuální počítač s [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Následující příklad definuje virtuální počítač s názvem *myVM* s velikostí virtuálních počítačů, která podporuje akcelerované síťové (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Seznam všech velikostí virtuálních počítačů a vlastnosti najdete v tématu [velikosti virtuálních počítačů s Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Vytvoření rest konfiguraci virtuálního počítače s [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) a [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Následující příklad vytvoří virtuální počítač s Windows serverem 2016:

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

Připojit síťové rozhraní, kterou jste vytvořili pomocí [přidat AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Nakonec vytvořte virtuální počítač s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
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
$nic = Get-AzureRMNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRMNetworkInterface
```

Restartování vašeho virtuálního počítače nebo, pokud ve skupině dostupnosti, všechny virtuální počítače v sadě a potvrďte, že je povoleno Akcelerovanými síťovými službami:

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí stejného pracovního postupu.  Nejdřív zastavte virtuální počítače:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Jakmile se virtuální počítače se zastaví, umožňuje aktualizujte vlastnost Akcelerovanými síťovými službami v části síťové rozhraní:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Poznámka: VMSS prosím má upgrady virtuálního počítače, které aktualizace pomocí tří různých nastavení automatické, Hromadná a ruční.  V těchto pokynech je zásada nastavená na hodnotu automaticky tak, aby VMSS vyzvedne, až bude změny okamžitě po restartování.  Nastavit ho na hodnotu automaticky, aby změny okamžitě, vyberou se:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

A konečně restartujte VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Jednou můžete znovu, počkejte upgrady dokončete ale po dokončení se zobrazí VF ve virtuálním počítači.  (Ujistěte se prosím, že používáte podporovanou velikost operačního systému a virtuálního počítače)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti stávajících virtuálních počítačů s Akcelerovanými síťovými službami

Virtuální počítače s Akcelerovanými síťovými službami povoleno pouze niž lze nastavit virtuální počítače, které podporují Akcelerovanými síťovými službami.  

Nelze změnit velikost virtuálního počítače s Akcelerovanými síťovými službami povolené k instanci virtuálního počítače, který nepodporuje Akcelerovanými síťovými službami pomocí operace změny velikosti.  Místo toho chcete změnit velikost jeden z těchto virtuálních počítačů:

* Zastavení nebo zrušení přidělení virtuálního počítače nebo pokud v sadě dostupnosti/VMSS, zastavit/uvolnit všechny virtuální počítače v sadě/škálovací sady.
* Akcelerované síťové služby, musí se zakázat na síťovém rozhraní virtuálního počítače nebo při použití ve dostupnost sady nebo škálovací sada, všechny virtuální počítače v sadě/škálovací sady.
* Jakmile Akcelerovanými síťovými službami je zakázaná, sady nebo škálovací sada virtuálních počítačů a dostupnost lze přesunout do novou velikost, která nepodporuje Akcelerovanými síťovými službami a restartovat.
