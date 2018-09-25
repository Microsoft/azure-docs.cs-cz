---
title: Vytvoření virtuálního počítače Azure s Akcelerovanými síťovými službami | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem s Akcelerovanými síťovými službami povolena.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: b6aaf98ca3b5581691b6c70783be5250b506056c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990956"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Vytvoření virtuálního počítače s Linuxem s Akcelerovanými síťovými službami

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač s Linuxem (VM) s Akcelerovanými síťovými službami. Vytvoření virtuálního počítače s Windows s Akcelerovanými síťovými službami, najdete v tématu [vytvoření virtuálního počítače s Windows s Akcelerovanými síťovými službami](create-vm-accelerated-networking-powershell.md). Akcelerované síťové služby umožňuje jeden kořenový vstupně-výstupních operací virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, výrazně zlepšit sítě. Tato cesta výkonné obchází hostitele z datapath, snížení latence, zpoždění a využití procesoru pro použití s nejnáročnějších úloh sítě na podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikace mezi dvěma virtuálními počítači a nemusíte akcelerované síťové služby:

![porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez akcelerované síťové služby, musí procházet veškerý síťový provoz do a z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, jako jsou skupiny zabezpečení sítě, seznamy řízení přístupu, izolace a jiné síťové virtualizované služby pro síťový provoz. Další informace o virtuální přepínače, [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx) článku.

S akcelerovanými síťovými službami, síťový provoz dorazí na síťové rozhraní Virtuálního počítače (NIC) a pak se předávají do virtuálního počítače. Všechny zásady sítě, které se vztahuje virtuální přepínač se teď se sníženou zátěží a použít v hardwaru. Použití zásad v hardwaru umožňuje síťovou kartu pro provoz sítě přímo k virtuálnímu počítači, vynechání hostitele a virtuálního přepínače, při zachování všech zásad, který se použije na hostiteli.

Výhody akcelerované síťové služby se vztahují jenom na virtuální počítač, který je zapnutá. Pro nejlepší výsledky je ideální k povolení této funkce na alespoň dva virtuální počítače připojené ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojení v místním, tato funkce má minimální dopad na celkové latenci.

## <a name="benefits"></a>Výhody
* **Nižší latenci za vyšší pakety za sekundu (pps):** odebrání virtuálního přepínače datapath eliminuje čas věnovat paketů na hostiteli pro zpracování zásad a zvyšuje počet paketů, které mohou být zpracovány v tomto virtuálním počítači.
* **Snižuje zpoždění:** virtuální přepínač zpracování závisí na množství zásady, které je potřeba použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad hardwaru odebere této variabilitě poskytováním pakety přímo k virtuálnímu počítači, odebrání hostitele tak, aby komunikace virtuálních počítačů a všechny softwaru přerušení a přepnutí kontextu.
* **Snížení využití procesoru:** obcházení virtuální přepínač na hostiteli vede k méně využití procesoru pro zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy
Připravených v galerii Azure jsou podporovány následující distribuce: 
* **Ubuntu 16.04** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" s zpětné jádra**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Akcelerované síťové služby se podporuje na nejvíce obecné účely a velikostí optimalizovaných pro výpočetní instance s 2 nebo více virtuálních procesorů.  Jsou tyto podporované řady: D/DSv2 a F/Fs

U instancí, které podporují hyperthreadingem Akcelerovanými síťovými službami se podporuje na instancích virtuálních počítačů s 4 nebo více virtuálních procesorů. Jsou podporované řady: D/DSv3, E nebo ESv3, Fsv2 a Ms a Mms.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálního počítače s Linuxem](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastech Azure i Azure Government Clouds.

### <a name="network-interface-creation"></a>Vytvoření rozhraní sítě 
Akcelerované síťové služby jde Povolit jenom pro nový síťový adaptér. Není možné pro existující síťovou kartu
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení Akcelerovanými síťovými službami spuštěného virtuálního počítače
Podporované velikosti virtuálního počítače bez povolenými akcelerovanými síťovými službami může mít jenom funkce povolena, když se zastaví a uvolní.  
### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Azure Resource Manageru
Nelze nasadit virtuální počítače (klasické) s Akcelerovanými síťovými službami.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače s Linuxem s Akcelerovanými síťovými službami Azure

Přestože tento článek popisuje kroky k vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí rozhraní příkazového řádku Azure, můžete také [vytvoření virtuálního počítače s akcelerovanými síťovými službami pomocí webu Azure portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu v části **nastavení**vyberte **povoleno**v části **akcelerované síťové služby**. Možnost povolit akcelerované síťové služby nezobrazí na portálu, pokud jste vybrali [s podporovaným operačním systémem](#supported-operating-systems) a [velikost virtuálního počítače](#supported-vm-instances). Po vytvoření virtuálního počítače, které potřebujete k dokončení podle pokynů v [potvrďte, že je povoleno akcelerovanými síťovými službami](#confirm-that-accelerated-networking-is-enabled).

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a přihlaste se k Azure pomocí účtu [az login](/cli/azure/reference-index#az_login). V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Ukázkové názvy parametrů zahrnutých *myResourceGroup*, *myNic*, a *myVm*.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *centralus* umístění:

```azurecli
az group create --name myResourceGroup --location centralus
```

Vyberte podporovanou oblast Linux podle [Linux akcelerovanými síťovými službami](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVnet* s jednou podsítí:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
Vytvořte skupinu zabezpečení sítě pomocí [az network nsg vytvořit](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Skupina zabezpečení sítě obsahuje několik výchozích pravidel, z nichž jeden zakáže všechny příchozí přístup z Internetu. Otevřete port pro přístup SSH k virtuálnímu počítači s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvořte síťové rozhraní s akcelerovanými síťovými službami

Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Veřejná IP adresa není povinné, když se nechystáte pro přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku, je povinný.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Vytvořte síťové rozhraní s [az network nic vytvořit](/cli/azure/network/nic#az_network_nic_create) s povolenými akcelerovanými síťovými službami. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v *mySubnet* podsíti *myVnet* virtuální síť a přidruží  *myNetworkSecurityGroup* skupinu zabezpečení sítě k síťovému rozhraní:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Vytvoření virtuálního počítače a připojení síťového adaptéru
Když vytvoříte virtuální počítač, určit síťové rozhraní vytvořené pomocí `--nics`. Vyberte velikost a rozmístění, které jsou uvedeny v [Linux akcelerovanými síťovými službami](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVM* s obrázkem UbuntuLTS a velikostí, která podporuje akcelerované síťové (*Standard_DS4_v2*):

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

Seznam všech velikostí virtuálních počítačů a vlastnosti najdete v tématu [velikosti virtuálního počítače s Linuxem](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po vytvoření virtuálního počítače, vrátí se výstup podobný následující příklad výstupu. Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači v dalších krocích.

```azurecli
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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Potvrďte, že je povoleno akcelerované síťové služby

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Nahraďte `<your-public-ip-address>` s veřejnou IP adresu přiřazenou virtuální počítače, kterou jste vytvořili a nahraďte *azureuser* Pokud jste použili jinou hodnotu pro `--admin-username` při vytváření virtuálního počítače.

```bash
ssh azureuser@<your-public-ip-address>
```

Z prostředí Bash, zadejte `uname -r` a potvrďte, že verze jádra je jednou z následujících verzí, nebo vyšší:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Potvrďte zařízení Mellanox VF zpřístupněn do virtuálního počítače s `lspci` příkazu. Vrácené výstup se podobá následující výstup:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Vyhledat aktivitu na VF (virtuální funkci) se `ethtool -S eth0 | grep vf_` příkazu. Pokud se zobrazí výstup, podobně jako v následujícím příkladu výstupu, je povoleno akcelerované síťové služby a práci.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Pro váš virtuální počítač je nyní k dispozici akcelerované síťové služby.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit akcelerované síťové na stávajících virtuálních počítačů
Pokud jste vytvořili virtuální počítač bez Akcelerovanými síťovými službami, je možné povolit tuto funkci na existující virtuální počítač.  Virtuální počítač musí podporovat Akcelerovanými síťovými službami při splnění následujících požadavků, které jsou také uvedené výše:

* Virtuální počítač musí být podporovaná velikost pro Akcelerovanými síťovými službami
* Virtuální počítač musí být podporované image z Galerie Azure (a verze jádra pro Linux)
* Všechny virtuální počítače ve skupině dostupnosti nebo VMSS musí být zastaveno a před povolením Akcelerovanými síťovými službami na libovolné síťové rozhraní

### <a name="individual-vms--vms-in-an-availability-set"></a>Nastavení jednotlivých virtuálních počítačů a virtuálních počítačů ve skupině dostupnosti
Nejprve zastavit/zrušit přidělení virtuálního počítače nebo v případě dostupnosti, všechny virtuální počítače v sadě:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Důležité, prosím Poznámka: Pokud se váš virtuální počítač jednotlivě, vytvořil bez skupinu dostupnosti, můžete pouze muset zastavit/uvolnit konkrétního virtuálního počítače povolit Akcelerovanými síťovými službami.  Pokud váš virtuální počítač byl vytvořen pomocí nastavení dostupnosti, všechny virtuální počítače, které jsou obsaženy ve skupině dostupnosti bude potřeba zastavit/uvolnit před povolením Akcelerovanými síťovými službami na libovolné síťové rozhraní. 

Po zastavení povolte akcelerované síťové na síťovém rozhraní virtuálního počítače:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Restartování vašeho virtuálního počítače nebo, pokud do skupiny dostupnosti, všechny virtuální počítače v sadě a potvrďte, že je povoleno Akcelerovanými síťovými službami: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí stejného pracovního postupu.  Nejdřív zastavte virtuální počítače:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Jakmile se virtuální počítače se zastaví, umožňuje aktualizujte vlastnost Akcelerovanými síťovými službami v části síťové rozhraní:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Poznámka: VMSS prosím má upgrady virtuálního počítače, které aktualizace pomocí tří různých nastavení automatické, Hromadná a ruční.  V těchto pokynech je zásada nastavená na hodnotu automaticky tak, aby VMSS vyzvedne, až bude změny okamžitě po restartování.  Nastavit ho na hodnotu automaticky, aby změny okamžitě, vyberou se: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

A konečně restartujte VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Jednou můžete znovu, počkejte upgrady dokončete ale po dokončení se zobrazí VF ve virtuálním počítači.  (Ujistěte se prosím, že používáte podporovanou velikost operačního systému a virtuálního počítače.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti stávajících virtuálních počítačů s Akcelerovanými síťovými službami

Virtuální počítače s Akcelerovanými síťovými službami povoleno pouze niž lze nastavit virtuální počítače, které podporují Akcelerovanými síťovými službami.  

Nelze změnit velikost virtuálního počítače s Akcelerovanými síťovými službami povolené k instanci virtuálního počítače, který nepodporuje Akcelerovanými síťovými službami pomocí operace změny velikosti.  Místo toho chcete změnit velikost jeden z těchto virtuálních počítačů: 

* Zastavení nebo zrušení přidělení virtuálního počítače nebo pokud v sadě dostupnosti/VMSS, zastavit/uvolnit všechny virtuální počítače v sadě/škálovací sady.
* Akcelerované síťové služby, musí se zakázat na síťovém rozhraní virtuálního počítače nebo při použití ve dostupnost sady nebo škálovací sada, všechny virtuální počítače v sadě/škálovací sady.
* Jakmile Akcelerovanými síťovými službami je zakázaná, sady nebo škálovací sada virtuálních počítačů a dostupnost lze přesunout do novou velikost, která nepodporuje Akcelerovanými síťovými službami a restartovat.  

