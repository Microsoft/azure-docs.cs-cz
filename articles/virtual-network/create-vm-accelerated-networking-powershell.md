---
title: Vytvoření virtuálního počítače Azure s akcelerací sítí – Azure PowerShell
description: Naučte se, jak vytvořit virtuální počítač linuxu s akcelerací sítí.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245054"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Vytvoření virtuálního počítače s Windows se zrychlenými sítěmi pomocí Azure PowerShellu

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač (VM) windows s akcelerované sítě. Pokud chcete vytvořit virtuální počítač s Linuxem se zrychlenou sítí, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem se zrychlenou sítí](create-vm-accelerated-networking-cli.md). Zrychlená síť umožňuje virtualizaci vstupně-videa jednoho kořene (SR-IOV) do virtuálního počítače, což výrazně zlepšuje jeho síťový výkon. Tato cesta vysokého výkonu obchází hostitele z cesty data, snižuje latenci, kolísání a využití procesoru pro použití s nejnáročnějšími síťovými úlohami na podporovaných typech virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dvěma virtuálními zařízeními se zrychleným vytvářením sítí a bez něj:

![Srovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez zrychlené sítě, všechny síťové provozy do a z virtuálního počítače musí procházet hostitele a virtuální přepínač. Virtuální přepínač poskytuje všechny zásady vynucení, jako jsou skupiny zabezpečení sítě, seznamy řízení přístupu, izolace a další síťové virtualizované služby pro síťový provoz. Další informace o virtuálních přepínačích najdete v tématu [Virtualizace sítě Hyper-V a virtuální přepínač](https://technet.microsoft.com/library/jj945275.aspx).

Díky zrychlené síti, síťový provoz dorazí na síťové rozhraní virtuálního zařízení (NIC) a pak se předává do virtuálního soudu. Všechny zásady sítě, které virtuální přepínač platí, jsou nyní převedeny a použity v hardwaru. Použití zásad v hardwaru umožňuje síťové karty předávat síťový provoz přímo do virtuálního počítače, obcházet hostitele a virtuální přepínač při zachování všech zásad, které použila v hostiteli.

Výhody zrychleného vytváření sítí platí jenom pro virtuální počítač, na který je povolen. Pro dosažení nejlepších výsledků je ideální povolit tuto funkci alespoň na dvou virtuálních počítačích připojených ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojení k místním, tato funkce má minimální dopad na celkovou latenci.

## <a name="benefits"></a>Výhody
* **Nižší latence / vyšší pakety za sekundu (pps):** Odebrání virtuálního přepínače z datové cesty odebere čas, který pakety stráví v hostiteli pro zpracování zásad, a zvýší počet paketů, které mohou být zpracovány uvnitř virtuálního počítače.
* **Snížená nervozita:** Zpracování virtuálního přepínače závisí na množství zásad, které je třeba použít, a na zatížení procesoru, který provádí zpracování. Překládka vynucení zásad na hardware odstraní tuto variabilitu doručením paketů přímo do virtuálního počítače, odebráním hostitele do komunikace virtuálního počítače a veškerých softwarových přerušení a přepnutí kontextu.
* **Snížené využití procesoru:** Vynechání virtuálního přepínače v hostiteli vede k menšímu využití procesoru pro zpracování síťového provozu.

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure jsou podporované následující distribuce:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Podporované instance virtuálních ms
Akcelerovaná síť je podporována pro většinu velikostí instancí pro obecné účely a optimalizované pro výpočetní výkon y se 2 nebo více virtuálními procesory.  Tyto podporované řady jsou: D/DSv2 a F/Fs

V instancích, které podporují hyperthreading, akcelerované sítě je podporována na instancích virtuálních počítačích se 4 nebo více virtuálních procesorů. Podporované řady jsou: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms a Ms/Mmsv2.

Další informace o instancích virtuálních počítačů najdete v [tématu Velikosti virtuálních počítačových sítí v systému Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Oblasti
Dostupné ve všech veřejných oblastech Azure a Azure Government Cloud.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení zrychlené sítě na spuštěném virtuálním počítači
Podporovaná velikost virtuálního počítače bez povolené zrychlené sítě může mít tuto funkci povolenou pouze v případě, že je zastavena a vrácena.

### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Správce prostředků Azure
Virtuální počítače (klasické) nelze nasadit s akcelerované sítě.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače s Windows s azure accelerated networking
## <a name="portal-creation"></a>Vytvoření portálu
I když tento článek obsahuje kroky k vytvoření virtuálního počítače s akcelerací sítí pomocí Azure Powershellu, můžete také [vytvořit virtuální počítač s akcelerované sítě pomocí portálu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu zvolte v okně **Vytvořit virtuální počítač** kartu **Síť.**  Na této kartě je možnost **akcelerované sítě**.  Pokud jste zvolili [podporovaný operační systém](#supported-operating-systems) a velikost [virtuálního počítače](#supported-vm-instances), tato možnost se automaticky naplní na "Zapnuto".  Pokud ne, naplní možnost "Vypnuto" pro akcelerované sítě a poskytne uživateli důvod, proč není povolena.   
* *Poznámka:* Prostřednictvím portálu lze povolit pouze podporované operační systémy.  Pokud používáte vlastní image a vaše image podporuje akcelerované sítě, vytvořte virtuální počítač pomocí cli nebo Powershellu. 

Po vytvoření virtuálního počítače můžete potvrdit, že je povoleno zrychlené vytváření sítí podle pokynů v potvrzení, že je povolena akcelerovaná síť.

## <a name="powershell-creation"></a>Vytvoření prostředí Powershell
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) verze 1.0.0 nebo novější. Chcete-li najít aktuálně nainstalovanou verzi, spusťte aplikaci `Get-Module -ListAvailable Az`. Pokud potřebujete nainstalovat nebo upgradovat, nainstalujte nejnovější verzi modulu Az z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az). V relaci PowerShellu se přihlaste k účtu Azure pomocí [connect-azaccount](/powershell/module/az.accounts/connect-azaccount).

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnovaly *myResourceGroup*, *myNic*a *myVM*.

Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *centralus:*

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Nejprve vytvořte konfiguraci podsítě pomocí [nástroje New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Následující příklad vytvoří podsíť s názvem *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Vytvořte virtuální síť s [novou virtuální sítí](/powershell/module/az.Network/New-azVirtualNetwork)s podsítí *myPodnet.*

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Nejprve vytvořte pravidlo skupiny zabezpečení sítě pomocí funkce [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Vytvořte skupinu zabezpečení sítě s [novou skupinou AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) a přiřaďte jí pravidlo zabezpečení *Povolit RDP-All.* Kromě pravidla *Povolit VŠE v protokolu RDP* obsahuje skupina zabezpečení sítě několik výchozích pravidel. Jedno výchozí pravidlo zakáže veškerý příchozí přístup z Internetu, což je důvod, proč je pravidlo *Povolit VŠE* přiřazeno skupině zabezpečení sítě, abyste se po jeho vytvoření mohli vzdáleně připojit k virtuálnímu počítači.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Přidružte skupinu zabezpečení sítě k podsíti *myPodnet* k [programu Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Pravidlo ve skupině zabezpečení sítě je účinné pro všechny prostředky nasazené v podsíti.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvoření síťového rozhraní se zrychleným vytvářením sítí
Vytvořte veřejnou IP adresu s [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Veřejná IP adresa není vyžadována, pokud nemáte v plánu získat přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku je to nutné.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Vytvořte síťové rozhraní s [rozhraním New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) s povolenou zrychlenou sítí a přiřaďte síťovému rozhraní veřejnou ADRESU IP. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v podsíti *mySubnet* virtuální sítě *myVnet* a přiřadí mu veřejnou IP adresu *myPublicIp:*

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

Nastavte pověření virtuálního počítače `$cred` na proměnnou pomocí [get-credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Nejprve definujte virtuální počítač pomocí [aplikace New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Následující příklad definuje virtuální hosti s názvem *myVM* s velikostí virtuálního počítače, který podporuje akcelerované sítě *(Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Seznam všech velikostí a charakteristik virtuálních počítače najdete v [tématu velikosti virtuálních aplikací windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Vytvořte zbytek konfigurace virtuálního počítače pomocí [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) a [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Následující příklad vytvoří virtuální ms Windows Server 2016:

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

Připojte dříve vytvořené síťové rozhraní pomocí [rozhraní Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Nakonec vytvořte virtuální počítač s [Novým AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Zkontrolujte, zda je ovladač nainstalován v operačním systému

Po vytvoření virtuálního počítače v Azure se připojte k virtuálnímu počítači a ověřte, že ovladač je nainstalovaný v systému Windows.

1. Z internetového prohlížeče otevřete [portál](https://portal.azure.com) Azure a přihlaste se pomocí svého účtu Azure.
2. Do pole, které obsahuje textové *prostředky hledání* v horní části portálu Azure, zadejte *myVm*. Když se **myVm** zobrazí ve výsledcích hledání, klikněte na něj. Pokud **vytváření** je viditelné pod tlačítko **Připojit,** Azure ještě nedokončil vytvoření virtuálního počítače. V levém horním rohu přehledu klikněte na **Připojit** až poté, co se pod tlačítkem **Připojit** už nezobrazí **možnost Vytvořit.**
3. Zadejte uživatelské jméno a heslo, které jste zadali [v okně Vytvořit virtuální počítač](#create-the-virtual-machine). Pokud jste se nikdy nepřipojili k virtuálnímu počítači s Windows v Azure, [přečtěte si tématu Připojení k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Klepněte pravým tlačítkem myši na tlačítko Start systému Windows a klepněte na příkaz **Správce zařízení**. Rozbalte uzel **síťové adaptéry.** Zkontrolujte, zda se zobrazí **adaptér Mellanox ConnectX-3 Virtual Function Ethernet,** jak je znázorněno na následujícím obrázku:

    ![Správce zařízení](./media/create-vm-accelerated-networking/device-manager.png)

Akcelerovaná síť je teď povolená pro váš virtuální počítač.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolení zrychlených sítí na stávajících virtuálních počítačích
Pokud jste vytvořili virtuální počítač bez akcelerované sítě, je možné povolit tuto funkci na existujícím virtuálním počítači.  Virtuální ho virtuálního zařízení musí podporovat zrychlené vytváření sítí splněním následujících požadavků, které jsou také popsané výše:

* Virtuální modul musí mít podporovanou velikost pro akcelerované sítě.
* Virtuální počítač musí být podporovaná image Galerie Azure (a verze jádra pro Linux)
* Všechny virtuální ho disponibilní v sadě dostupnosti nebo v VMSS musí být zastaveny nebo deallocated před povolením akcelerované sítě na libovolné síťové korynici

### <a name="individual-vms--vms-in-an-availability-set"></a>Jednotlivé virtuální virtuální aplikace & virtuálních mětek v sadě dostupnosti
První stop/deallocate virtuálního jevu nebo, pokud sada dostupnosti, všechny virtuální chody v sadě:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Důležité, upozorňujeme, že pokud váš virtuální počítač byl vytvořen jednotlivě, bez sady dostupnosti, stačí zastavit/navrátit jednotlivé virtuální počítač, aby bylo možné povolit akcelerované sítě.  Pokud váš virtuální počítač byl vytvořen s sadou dostupnosti, všechny virtuální počítače obsažené v sadě dostupnosti bude nutné zastavit nebo deallocated před povolením akcelerované sítě na některý z síťových připojení. 

Po zastavení povolte akcelerované sítě na síťové kasenici virtuálního počítače:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Restartujte virtuální počítač nebo, pokud v sadě dostupnosti, všechny virtuální počítače v sadě a zkontrolujte, že je povolená akcelerovaná síť:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí se stejným pracovním postupem.  Nejdřív zastavte virtuální chod:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po zastavení virtuálních zařízení aktualizujte vlastnost Accelerated Networking pod síťovým rozhraním:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Upozorňujeme, že služba VMSS má upgrady virtuálních zařízení, které používají aktualizace pomocí tří různých nastavení, automatického, postupného a ručního.  V těchto pokynech je zásada nastavena na automatické tak, aby VMSS bude vyzvednout změny ihned po restartování.  Chcete-li nastavit automatické tak, aby změny jsou okamžitě zvedl:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Nakonec restartujte Službu VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Po restartování počkejte na dokončení upgradů, ale po dokončení se v rámci virtuálního počítače zobrazí vrámci virtuálního počítače VF.  (Ujistěte se, že používáte podporovanou velikost operačního systému a virtuálního počítače)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti existujících virtuálních zařízení pomocí akcelerované sítě

Virtuální stránky s povolenou zrychlenou sítí lze velikost pouze na virtuální chod, které podporují akcelerované sítě.  

Virtuální ho virtuálního počítače s povolenou zrychlenou sítí nelze změnit velikost instance virtuálního počítače, která nepodporuje akcelerované sítě pomocí operace změny velikosti.  Místo toho chcete změnit velikost jednoho z těchto virtuálních počítače:

* Stop/Deallocate virtuálního virtuálního serveru nebo pokud v dostupnosti set/VMSS, stop/navrátit všechny virtuální chody v sadě/VMSS.
* Akcelerované sítě musí být zakázány na síťové konto virtuálního počítači nebo pokud v dostupnosti set/VMSS, všechny virtuální chody v sadě/VMSS.
* Po zakázání akcelerované sítě lze sadu virtuálních her/dostupnosti/vmss přesunout do nové velikosti, která nepodporuje akcelerované sítě a restartován.
