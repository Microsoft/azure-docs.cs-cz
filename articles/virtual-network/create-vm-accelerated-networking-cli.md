---
title: Vytvoření virtuálního počítače Azure s akcelerovanou sítí pomocí Azure CLI
description: Naučte se, jak vytvořit virtuální počítač se systémem Linux s povolenými akcelerovanými síťovými službami.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: bccbfed96dd6cd87bdfe986baf4b52817a160ac0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533357"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Vytvoření virtuálního počítače se systémem Linux s využitím akcelerované sítě pomocí Azure CLI

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač se systémem Linux s akcelerovanými síťovými službami. Informace o vytvoření virtuálního počítače s Windows a akcelerované sítě najdete v tématu [Vytvoření virtuálního počítače s Windows s akcelerovanými síťovými](create-vm-accelerated-networking-powershell.md)službami. Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což výrazně zlepšuje výkon sítě. Tato cesta s vysokým výkonem obchází hostitele z DataPath, snižuje latenci, kolísání a využití CPU a používá se u nejnáročnějších síťových úloh na podporovaných typech virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dvěma virtuálními počítači s a bez urychlení sítě:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez urychlení sítě musí všechny síťové přenosy na virtuálním počítači a z něj projít hostitelem a virtuálním přepínačem. Virtuální přepínač poskytuje všechna vynucení zásad, například skupiny zabezpečení sítě, seznamy řízení přístupu, izolaci a další síťové virtualizované služby pro síťový provoz. Další informace o virtuálních přepínačích najdete v článku [virtualizace sítě Hyper-V a virtuální přepínač](https://technet.microsoft.com/library/jj945275.aspx) .

V případě akcelerovaných síťových přenosů dorazí síťový provoz na síťové rozhraní (NIC) virtuálního počítače a pak se přepošle virtuálnímu počítači. Všechny zásady sítě, které virtuální přepínač platí, se teď přesměrují a použijí v hardwaru. Použití zásad v hardwaru umožňuje síťovému rozhraní přesměrovat síťový provoz přímo do virtuálního počítače, vynechá hostitele a virtuální přepínač a přitom zachová všechny zásady, které v hostiteli použili.

Výhody akcelerovaných sítí se vztahují jenom na virtuální počítač, na kterém je povolený. Pro dosažení nejlepších výsledků je ideální povolit tuto funkci aspoň na dvou virtuálních počítačích připojených ke stejné virtuální síti Azure (VNet). Při komunikaci přes virtuální sítě nebo místní připojení má tato funkce minimální dopad na celkovou latenci.

## <a name="benefits"></a>Výhody
* **Nižší latence/vyšší počet paketů za sekundu (PPS):** Když odeberete virtuální přepínač z vlastnosti DataPath, dojde k odebrání nepotřebných paketů v hostiteli pro zpracování zásad a zvýšení počtu paketů, které se dají zpracovat v rámci virtuálního počítače.
* **Zkrácená kolísání:** Zpracování virtuálních přepínačů závisí na množství zásad, které je třeba použít, a na zatížení procesoru, který provádí zpracování. Přesměrování vynucení zásad na hardware odstraní tuto variabilitu tím, že doručí pakety přímo virtuálnímu počítači, odebere hostitele do komunikace virtuálních počítačů a všechna přerušení softwaru a přepínače kontextu.
* **Snížení využití CPU:** Vynechání virtuálního přepínače v hostiteli vede k menšímu využití procesoru při zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure se podporují následující distribuce: 
* **Ubuntu 14,04 s jádrem pro Linux – Azure**
* **Ubuntu 16,04 nebo novější** 
* **SLES12 SP3 nebo novější** 
* **RHEL 7,4 nebo novější**
* **CentOS 7,4 nebo novější**
* **CoreOS Linux**
* **Debian "roztažení" s jádrem pro porty, Debian "Buster" nebo novějším**
* **Oracle Linux 7,4 a novější s jádrem kompatibilním s Red Hat (RHCK)**
* **Oracle Linux 7,5 a novější s UEK verze 5**
* **FreeBSD 10,4, 11,1 & 12,0 nebo novější**

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované sítě se podporují na většině účelových a výpočetních instancí optimalizovaných pro výpočty s 2 nebo více vCPU.  Tyto podporované řady: D/DSv2 a F/FS

Na instancích, které podporují multithreading, se zrychluje síť pro instance virtuálních počítačů se 4 nebo více vCPU. Podporované řady jsou: D/Dsv3, D/Dsv4, DD/Ddv4, da/Dasv4, E/Esv3, E/Esv4, Ed/Edsv4, EA/Easv4, Fsv2, Lsv2, MS/MMS a MS/Mmsv2.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů se systémem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Vlastní image
Pokud používáte vlastní image a vaše image podporuje akcelerované síťové služby, ujistěte se, že máte požadované ovladače pro práci s Mellanox ConnectX a ConnectX-4 LX síťových adaptérů v Azure.

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastech Azure a také Azure Governmentch cloudech.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení akcelerovaných síťových služeb na běžícím virtuálním počítači
Podporovaná velikost virtuálního počítače bez aktivovaného urychlení sítě může mít povolenou funkci jenom v případě, že je zastavená a navrácena.  
### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Azure Resource Manager
Virtuální počítače (Classic) nejde nasadit s akcelerovanými síťovými službami.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače se systémem Linux pomocí akcelerovaných síťových služeb Azure
## <a name="portal-creation"></a>Vytváření portálu
I když tento článek popisuje kroky pro vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí Azure CLI, můžete také [vytvořit virtuální počítač s akcelerovanými síťovými službami pomocí Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu klikněte v okně **vytvořit virtuální počítač** na kartu **síť** .  Na této kartě je možnost **zrychlit síťové služby**.  Pokud jste zvolili [podporovaný operační systém](#supported-operating-systems) a [Velikost virtuálního počítače](#supported-vm-instances), tato možnost se automaticky naplní na "zapnuto".  Pokud ne, naplní možnost "vypnuto" pro urychlené síťové služby a uvede uživateli důvod, proč není povolen.   

* *Poznámka:* Prostřednictvím portálu lze povolit pouze podporované operační systémy.  Pokud používáte vlastní image a vaše image podporuje akcelerované síťové služby, vytvořte si virtuální počítač pomocí rozhraní příkazového řádku nebo PowerShellu. 

Po vytvoření virtuálního počítače můžete potvrdit, že je povolené urychlení sítě, a to podle pokynů uvedených v části [potvrzení, že je povolené urychlené síťové služby](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Vytvoření rozhraní příkazového řádku
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte si nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index). V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují *myResourceGroup*, *myNic* a *myVm*.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *centralus* :

```azurecli
az group create --name myResourceGroup --location centralus
```

Vyberte podporovanou oblast systému Linux uvedenou v článku [urychlení sítě se systémem Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť s názvem *myVnet* s jednou podsítí:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Skupina zabezpečení sítě obsahuje několik výchozích pravidel, z nichž jeden zakáže veškerý příchozí přístup z Internetu. Otevřete port, který povolí přístup SSH k virtuálnímu počítači pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvoření síťového rozhraní s akcelerovanými síťovými službami

Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip). Veřejná IP adresa není povinná, pokud neplánujete přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku je to potřeba.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Vytvořte síťové rozhraní pomocí [AZ Network nic Create](/cli/azure/network/nic) s povolenými akcelerovanými síťovými službami. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v podsíti *MySubnet* virtuální sítě *myVnet* a přidruží skupinu zabezpečení sítě *myNetworkSecurityGroup* k síťovému rozhraní:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Vytvoření virtuálního počítače a připojení síťového rozhraní
Při vytváření virtuálního počítače zadejte síťovou kartu, pomocí které jste vytvořili `--nics` . Vyberte velikost a distribuci uvedenou v článku [urychlení sítě Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVM* s imagí UbuntuLTS a velikostí, která podporuje akcelerované síťové služby (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Seznam všech velikostí a vlastností virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů se systémem Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po vytvoření virtuálního počítače se vrátí výstup podobný následujícímu příkladu výstupu. Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači v následujících krocích.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Potvrďte, že je povolené urychlené síťové služby.

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Nahraďte `<your-public-ip-address>` veřejnou IP adresou přiřazenou k virtuálnímu počítači, který jste vytvořili, a nahraďte *azureuser* , pokud jste `--admin-username` při vytváření virtuálního počítače použili jinou hodnotu.

```bash
ssh azureuser@<your-public-ip-address>
```

V prostředí bash zadejte `uname -r` a potvrďte, že verze jádra je jedna z následujících verzí, nebo vyšší:

* **Ubuntu 16,04**: 4.11.0-1013
* **SLES SP3**: 4.4.92 – 6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Pomocí příkazu potvrďte, že se zařízení Mellanox VF zveřejňuje u virtuálního počítače `lspci` . Vrácený výstup je podobný následujícímu výstupu:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Vyhledat aktivitu na VF (virtuální funkce) pomocí `ethtool -S eth0 | grep vf_` příkazu Pokud se zobrazí výstup podobný následujícímu ukázkovému výstupu, je povolená a funkční akcelerace sítě.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Pro váš virtuální počítač je teď povolená akcelerovaná síť.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Zpracovat dynamickou vazbu a odvolat virtuální funkci 
Aplikace musí běžet přes syntetickou síťovou kartu, která je vystavená na virtuálním počítači. Pokud se aplikace spustí přímo přes síťovou kartu VF, neobdrží **všechny** pakety určené pro virtuální počítač, protože se některé pakety zobrazují přes syntetické rozhraní.
Pokud aplikaci spouštíte přes syntetickou síťovou kartu, zaručuje, že aplikace obdrží **všechny** pakety, které jsou určené pro ně. Také zajišťuje, aby aplikace běžela i v případě, že je VF při obsluhování hostitele odvoláno. Aplikace, které jsou vázány na syntetické síťové rozhraní, jsou **závazným** požadavkem pro všechny aplikace, které využívají **urychlené síťové služby**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit akcelerované síťové služby na stávajících virtuálních počítačích
Pokud jste vytvořili virtuální počítač bez urychlení sítě, je možné tuto funkci povolit na stávajícím virtuálním počítači.  Virtuální počítač musí podporovat urychlené síťové služby, a to splněním následujících požadavků, které jsou také uvedené výše:

* Virtuální počítač musí mít podporovanou velikost pro akcelerované síťové služby.
* Virtuální počítač musí být podporovaná Image Galerie Azure (a verze jádra pro Linux).
* Aby bylo možné zrychlit síťové rozhraní na libovolné síťové kartě, musí být všechny virtuální počítače ve skupině dostupnosti nebo VMSS zastavené nebo navrácené.

### <a name="individual-vms--vms-in-an-availability-set"></a>Jednotlivé virtuální počítače & virtuálních počítačů ve skupině dostupnosti
Nejdřív zastavte nebo zrušte přidělení virtuálního počítače, nebo pokud je nastavená Skupina dostupnosti, všechny virtuální počítače v sadě:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Důležité: Pokud jste virtuální počítač vytvořili samostatně bez skupiny dostupnosti, stačí zastavit nebo zrušit přidělení jednotlivých virtuálních počítačů, aby se povolily urychlené síťové služby.  Pokud byl virtuální počítač vytvořen pomocí skupiny dostupnosti, je nutné zastavit nebo zrušit přidělení všech virtuálních počítačů, které jsou obsaženy ve skupině dostupnosti, než povolíte urychlené síťové rozhraní na kterékoli síťové karty. 

Po zastavení povolte akcelerované síťové rozhraní pro virtuální počítač:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Restartujte virtuální počítač nebo, pokud je ve skupině dostupnosti, všechny virtuální počítače v sadě a potvrďte, že je povolené urychlení sítě: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí se stejným pracovním postupem.  Nejdřív zastavte virtuální počítače:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Po zastavení virtuálních počítačů aktualizujte vlastnost akcelerované sítě v síťovém rozhraní:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Upozorňujeme, že VMSS má upgrady virtuálních počítačů, které používají aktualizace, pomocí tří různých nastavení, automatických, ručních zavádění a ručních.  V těchto pokynech je zásada nastavená na hodnotu automaticky, aby VMSS po restartování vybrala změny hned.  Nastavení na automatické, aby se změny hned vybraly: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Nakonec restartujte VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Po restartování počkejte, až se upgrady dokončí, ale po dokončení se VF zobrazí ve virtuálním počítači.  (Ujistěte se prosím, že používáte podporovaný operační systém a velikost virtuálního počítače.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti stávajících virtuálních počítačů pomocí akcelerovaných síťových služeb

Virtuální počítače s povolenými akcelerovanými síťovými službami se můžou měnit jenom na virtuální počítače, které podporují urychlené síťové služby.  

Virtuální počítač s povolenými akcelerovanými síťovými službami se nedá změnit na instanci virtuálního počítače, která nepodporuje urychlení sítě pomocí operace změny velikosti.  Místo toho změňte velikost jednoho z těchto virtuálních počítačů: 

* Zastavte nebo zrušte přidělení virtuálního počítače, nebo pokud je ve skupině dostupnosti/VMSS, zastavte nebo zrušte přidělení všech virtuálních počítačů v set/VMSS.
* Akcelerované síťové služby musí být zakázané na síťové kartě virtuálního počítače, nebo pokud jsou v sadě dostupnosti/VMSS všechny virtuální počítače v set/VMSS.
* Jakmile budou urychlené síťové sítě zakázané, můžete virtuální počítač/sadu dostupnosti/VMSS přesunout na novou velikost, která nepodporuje urychlené síťové a restarty.  

