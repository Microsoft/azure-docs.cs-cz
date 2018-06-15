---
title: Vytvořte virtuální počítač Azure s Accelerated sítě | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem pomocí Accelerated sítě.
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
ms.openlocfilehash: 0f7f389df96f38bea3634bf712af3f9bf4bdde09
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893945"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Vytvořit virtuální počítač s Linuxem pomocí Accelerated sítě

V tomto kurzu zjistěte, jak vytvořit virtuální počítač (VM) s Linuxem pomocí Accelerated sítě. Pokud chcete vytvořit virtuální počítač s Windows pomocí Accelerated sítě, najdete v části [vytvoření virtuálního počítače s Windows pomocí Accelerated sítě](create-vm-accelerated-networking-powershell.md). Zrychlený sítě umožňuje jeden kořenový vstupně-výstupních operací virtualizace (SR-IOV) na virtuální počítače, výrazně zlepšit sítě. Tato cesta vysoce výkonné obchází hostitel datapath, snižuje latence, zpoždění a využití procesoru pro použití s nejnáročnější zatížení sítě v podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dva virtuální počítače a bez Zrychlený sítě:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez Zrychlený sítě, musí procházet všechny síťové přenosy do/z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, například skupin zabezpečení sítě, seznamy řízení přístupu, izolace a dalším službám síťové virtualizované síťový provoz. Další informace o virtuální přepínače, přečtěte si [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx) článku.

S Zrychlený sítě síťový provoz dorazí na rozhraní sítě Virtuálního počítače (NIC) a předá ji virtuálního počítače. Všechny zásady sítě, které se vztahuje na virtuální přepínač se teď se sníženou zátěží a použít v hardwaru. Použití zásad v hardwaru umožňuje síťový adaptér přesměrovat provoz sítě přímo k virtuálnímu počítači, obcházení hostitele a virtuálního přepínače, při zachování všech zásad, které se použijí v hostiteli.

Výhody Zrychlený sítě se vztahují pouze na virtuálním počítači, který je zapnutá. Nejlepších výsledků dosáhnete je ideální pro povolení této funkce na alespoň dva virtuální počítače připojené ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojování místní, tato funkce má minimální dopad na celkové latence.

## <a name="benefits"></a>Výhody
* **Nižší latenci vyšší pakety za sekundu (pps):** odebráním virtuálního přepínače datapath odebere čas, který potřebují paketů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které lze zpracovat ve virtuálním počítači.
* **Snižuje zpoždění:** virtuální přepínač zpracování závisí na množství zásady, které je nutné použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad do hardwaru, odstraní tento variabilita tím, že doručování paketů přímo k virtuálnímu počítači, odebrání hostitele do komunikace virtuálních počítačů a všechny softwaru přerušení a kontext přepínače.
* **Snížení využití procesoru:** obcházení virtuální přepínač na hostiteli vede k menší využití procesoru při zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy
Podporovány jsou následující distribuce předinstalované z Galerie Azure: 
* **Ubuntu 16.04** 
* **SLES 12 SP3** 
* **RHEL 7.4**
* **CentOS 7.4**
* **Jádro operačního systému Linux**
* **Debian "Stretch" s backports jádra**
* **Oracle Linux 7.4**

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Zrychlený sítě je podporována v nejvíce obecné účely a velikostí optimalizovaných výpočetní instance s 2 nebo více Vcpu.  Tyto podporované řady jsou: D/DSv2 a F nebo služby Fs

U instancí, které podporují Hyper-threadingem Accelerated sítě je podporována v instance virtuálních počítačů s 4 nebo více Vcpu. Jsou podporované řady: D/DSv3, E nebo ESv3, Fsv2 a Ms a Mms.

Další informace o instance virtuálních počítačů najdete v tématu [velikosti virtuálního počítače s Linuxem](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastí Azure a také Azure Government Cloudy.

### <a name="network-interface-creation"></a>Vytvoření rozhraní sítě 
Zrychlený sítě lze povolit pouze pro nový síťový adaptér. Nelze nastavit pro existující síťovou.
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení Accelerated sítě na spuštění virtuálního počítače
Podporovaná velikost virtuálního počítače bez použití Zrychlený sítě povolené může mít pouze funkci povolit, pokud je zastavena a navrácena.  
### <a name="deployment-through-azure-resource-manager"></a>Nasazení pomocí Azure Resource Manager
Virtuální počítače (klasické) nelze nasadit pomocí Accelerated sítě.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače s Linuxem pomocí Azure Zrychlený sítě

I když tento článek obsahuje kroky k vytvoření virtuálního počítače pomocí Zrychlený sítě pomocí rozhraní příkazového řádku Azure, můžete také [vytvoření virtuálního počítače pomocí Zrychlený sítě pomocí portálu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu, v části **nastavení**, vyberte **povoleno**v části **Accelerated sítě**. Možnost povolit Zrychlený sítě nebude se zobrazovat na portálu, pokud jste vybrali [podporovaný operační systém](#supported-operating-systems) a [velikost virtuálního počítače](#supported-vm-instances). Po vytvoření virtuálního počítače, které potřebujete k dokončení podle pokynů v [zkontrolujte, zda je povoleno Zrychlený sítě](#confirm-that-accelerated-networking-is-enabled).

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte si nejnovější verzi [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login). V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *myNic*, a *Můjvp*.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *centralus* umístění:

```azurecli
az group create --name myResourceGroup --location centralus
```

Vyberte podporované oblasti Linux uvedené v [Linux accelerated sítě](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

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
Vytvořit skupinu zabezpečení sítě s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Skupina zabezpečení sítě obsahuje několik výchozích pravidel, z nichž jeden zakáže všechny příchozí přístup z Internetu. Otevřete port pro povolení přístupu SSH pro virtuální počítač s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvořte síťové rozhraní s Zrychlený sítě

Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). Veřejná IP adresa není povinné, pokud neplánujete pro přístup k virtuálnímu počítači z Internetu, ale pokud chcete provést kroky v tomto článku, je potřeba.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Vytvoření síťové rozhraní s [vytvořit síťových adaptérů sítě az](/cli/azure/network/nic#az_network_nic_create) s Zrychlený sítě povolené. Následující příklad vytvoří rozhraní sítě s názvem *myNic* v *mySubnet* podsíť *myVnet* virtuální sítě a známými  *myNetworkSecurityGroup* skupinu zabezpečení sítě pro síťové rozhraní:

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

### <a name="create-a-vm-and-attach-the-nic"></a>Vytvoření virtuálního počítače a připojte síťový adaptér
Při vytváření virtuálního počítače, zadejte na síťový adaptér jste vytvořili pomocí `--nics`. Vyberte velikost a distribuce uvedené v [Linux accelerated sítě](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* s UbuntuLTS bitové kopie a velikost, která podporuje Accelerated sítě (*Standard_DS4_v2*):

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

Po vytvoření virtuálního počítače, je vrácen výstup podobný výstupu v následujícím příkladu. Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači v postupných krocích.

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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Zkontrolujte, zda je povoleno Zrychlený sítě

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Nahraďte `<your-public-ip-address>` s veřejnou IP adresou přiřazené pro virtuální počítače, kterou jste vytvořili a nahraďte *azureuser* Pokud jste použili jinou hodnotu pro `--admin-username` při vytvoření virtuálního počítače.

```bash
ssh azureuser@<your-public-ip-address>
```

Z prostředí Bash, zadejte `uname -r` a ověřte, zda je verze jádra jednu z následujících verzí, nebo vyšší:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Potvrďte Mellanox VF zařízení má přístup k virtuálnímu počítači pomocí `lspci` příkaz. Vrácený výstup je podobná následující výstup:

```bash
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Zkontrolujte pro aktivity na VF (virtuální funkce) s `ethtool -S eth0 | grep vf_` příkaz. Pokud se zobrazí výstup podobně jako v následujícím příkladu výstupu Zrychlený sítě je povolená a funguje.

```bash
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Pro virtuální počítač je nyní k dispozici Zrychlený sítě.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolit Accelerated sítě na existující virtuální počítače
Pokud jste vytvořili virtuální počítač bez Accelerated sítě, je možné povolit tuto funkci na existující virtuální počítač.  Virtuální počítač musí podporovat Accelerated sítě prostřednictvím splňuje následující požadavky, které jsou také uvedeny výše:

* Virtuální počítač musí být podporovaná velikost Accelerated sítě
* Virtuální počítač musí být podporovaná bitová kopie Galerie Azure (a verze jádra pro Linux)
* Všechny virtuální počítače v nastavení dostupnosti nebo VMSS musí být zastavena navrácena před povolením Accelerated sítě na všechny síťové adaptéry

### <a name="individual-vms--vms-in-an-availability-set"></a>Jednotlivé virtuální počítače a virtuální počítače ve skupině dostupnosti nastavena
Nejdřív zastavte nebo zrušit přidělení virtuálního počítače, nebo pokud skupiny dostupnosti, všechny virtuální počítače v sadě:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Důležité, prosím Poznámka: Pokud je virtuální počítač vytvořený jednotlivě, bez nastavení dostupnosti, můžete pouze muset zastavit nebo navrácení jednotlivých virtuálních počítačů povolit Accelerated sítě.  Pokud váš virtuální počítač byl vytvořen pomocí nastavení dostupnosti, všechny virtuální počítače, které jsou obsažené v sadě dostupnosti bude nutné zastavit navrácena před povolením Accelerated sítě na žádném z síťovými adaptéry. 

Po zastavení povolte Accelerated sítě na síťový adaptér virtuálního počítače:

```azurecli
az network nic update \
    --name myVM -n myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Restartujte váš virtuální počítač nebo, pokud v skupiny dostupnosti, všechny virtuální počítače v sadě a zkontrolujte, zda je povoleno Accelerated sítě: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale následuje témže pracovním postupu.  Nejdřív zastavte virtuální počítače:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Jakmile jsou zastaveny virtuální počítače, aktualizujte vlastnost Accelerated sítě v části síťové rozhraní:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Poznámka: VMSS prosím má upgrady virtuálních počítačů, které aktualizace pomocí tří různých nastavení automatického, postupného a ruční.  V těchto pokynech je zásada nastavená na hodnotu automaticky tak, aby VMSS vyzvedne, až bude změny okamžitě po restartování.  Nastavte ji na automatické, aby změny jsou okamžitě zachyceny: 

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

Jednou budete restartovat, čekat na upgrade ukončíte, ale po dokončení, zobrazí se VF ve virtuálním počítači.  (Ověřte, že používáte podporovaná velikost operačního systému a virtuálního počítače.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti existujících virtuálních počítačů pomocí Accelerated sítě

Virtuální počítače pomocí Accelerated sítě povolené jde změnit jenom pro virtuální počítače, které podporují Accelerated sítě.  

Velikost virtuálního počítače s Accelerated sítě povolené nelze změnit na instanci virtuálního počítače, který nepodporuje Accelerated sítě pomocí operace změny velikosti.  Místo toho ke změně velikosti jednu z těchto virtuálních počítačů: 

* Zastavení nebo Deallocate virtuálního počítače nebo pokud v sadě dostupnosti nebo VMSS, zastavení nebo navrácení všechny virtuální počítače v sadě nebo VMSS.
* Zrychlený sítě musí být zakázáno na síťový adaptér virtuálního počítače nebo pokud se v dostupnosti sady nebo VMSS, všechny virtuální počítače v sadě nebo VMSS.
* Jakmile Accelerated sítě je zakázaná, sada virtuálních počítačů nebo dostupnosti nebo VMSS lze přesunout do nové velikosti, která nepodporuje Accelerated sítě a restartování.  

