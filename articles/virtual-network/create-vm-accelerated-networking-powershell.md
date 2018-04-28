---
title: Vytvořte virtuální počítač Azure s Accelerated sítě | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem pomocí Accelerated sítě.
services: virtual-network
documentationcenter: ''
author: jdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: 995f40599c059434c419bea95019f8700f756ad8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Vytvoření virtuálního počítače s Windows pomocí Accelerated sítě

> [!IMPORTANT]
> Virtuální počítače musí být vytvořeny pomocí Accelerated sítě povolené. Tato funkce nelze povolit na existujících virtuálních počítačů. Proveďte následující kroky k povolení Zrychlený sítě:
>   1. Odstranění virtuálního počítače
>   2. Znovu vytvořte virtuální počítač s Zrychlený sítě povolené
>

V tomto kurzu zjistěte, jak k vytvoření virtuálního počítače (VM) s Windows pomocí Accelerated sítě. Zrychlený sítě umožňuje jeden kořenový vstupně-výstupních operací virtualizace (SR-IOV) na virtuální počítače, výrazně zlepšit sítě. Tato cesta vysoce výkonné obchází hostitel datapath, snižuje latence, zpoždění a využití procesoru pro použití s nejnáročnější zatížení sítě v podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dva virtuální počítače a bez Zrychlený sítě:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez Zrychlený sítě, musí procházet všechny síťové přenosy do/z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, například skupin zabezpečení sítě, seznamy řízení přístupu, izolace a dalším službám síťové virtualizované síťový provoz. Další informace o virtuální přepínače, přečtěte si [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx) článku.

S Zrychlený sítě síťový provoz dorazí na rozhraní sítě Virtuálního počítače (NIC) a předá ji virtuálního počítače. Všechny zásady sítě, které se vztahuje na virtuální přepínač se teď se sníženou zátěží a použít v hardwaru. Použití zásad v hardwaru umožňuje síťový adaptér přesměrovat provoz sítě přímo k virtuálnímu počítači, obcházení hostitele a virtuálního přepínače, při zachování všech zásad, které se použijí v hostiteli.

Výhody Zrychlený sítě se vztahují pouze na virtuálním počítači, který je zapnutá. Nejlepších výsledků dosáhnete je ideální pro povolení této funkce na alespoň dva virtuální počítače připojené ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojování místní, tato funkce má minimální dopad na celkové latence.

## <a name="benefits"></a>Výhody
* **Nižší latenci vyšší pakety za sekundu (pps):** odebráním virtuálního přepínače datapath odebere čas, který potřebují paketů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které lze zpracovat ve virtuálním počítači.
* **Snižuje zpoždění:** virtuální přepínač zpracování závisí na množství zásady, které je nutné použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad do hardwaru, odstraní tento variabilita tím, že doručování paketů přímo k virtuálnímu počítači, odebrání hostitele do komunikace virtuálních počítačů a všechny softwaru přerušení a kontext přepínače.
* **Snížení využití procesoru:** obcházení virtuální přepínač na hostiteli vede k menší využití procesoru při zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy
Microsoft Windows Server 2012 R2 Datacenter a Windows Server 2016.

## <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Zrychlený sítě je podporována v nejvíce obecné účely a velikostí optimalizovaných výpočetní instance s 4 nebo více Vcpu. V instancích například D/DSv3 nebo E/ESv3, které podporují Hyper-threadingem Accelerated sítě je podporována v instance virtuálních počítačů s 8 nebo více Vcpu. Jsou podporované řady: D/DSv2, D/DSv3, E nebo ESv3, F nebo služby Fs nebo Fsv2 a Ms a Mms.

Další informace o instance virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastí Azure a cloudu Azure Government.

## <a name="limitations"></a>Omezení
Při použití této funkce, existují tato omezení:

* **Vytvoření rozhraní sítě:** Accelerated sítě lze povolit pouze pro nový síťový adaptér. Nelze nastavit pro existující síťovou.
* **Vytvoření virtuálního počítače:** A síťovým Adaptérem s Zrychlený sítě povolené lze připojit pouze k virtuálnímu počítači, když je vytvořen virtuální počítač. Síťový adaptér nelze připojit k existující virtuální počítač. Pokud Přidání virtuálního počítače do existující dostupnosti nastavena, všechny virtuální počítače v sadě dostupnosti musí také mít accelerated sítě povolené.
* **Nasazení prostřednictvím Správce Azure Resource Manager pouze:** virtuálních počítačů (klasické) nelze nasadit pomocí Accelerated sítě.

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
