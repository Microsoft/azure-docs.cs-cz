---
title: Vytvořte virtuální počítač Azure s Accelerated sítě | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem pomocí Accelerated sítě.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 718990b69cc75709af819ad7df9a77ad0f8f33ce
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking"></a>Vytvořit virtuální počítač s Linuxem pomocí Accelerated sítě

> [!IMPORTANT] 
> Virtuální počítače musí být vytvořeny pomocí Accelerated sítě povolené. Tato funkce nelze povolit na existujících virtuálních počítačů. Proveďte následující kroky k povolení Accelerated sítě:
>   1. Odstranění virtuálního počítače.
>   2. Znovu vytvořte virtuální počítač pomocí Accelerated sítě povolené.
>

V tomto kurzu zjistěte, jak vytvořit virtuální počítač (VM) s Linuxem pomocí Accelerated sítě. Zrychlený sítě umožňuje jeden kořenový vstupně-výstupních operací virtualizace (SR-IOV) na virtuální počítače, výrazně zlepšit sítě. Tato cesta vysoce výkonné obchází hostitel datapath, snižuje latence, zpoždění a využití procesoru pro použití s nejnáročnější zatížení sítě v podporované typy virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dva virtuální počítače a bez Zrychlený sítě:

![Porovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez Zrychlený sítě, musí procházet všechny síťové přenosy do/z virtuálního počítače hostitele a virtuálního přepínače. Virtuální přepínač poskytuje všechny vynucení zásad, například skupin zabezpečení sítě, seznamy řízení přístupu, izolace a dalším službám síťové virtualizované síťový provoz. Další informace o virtuální přepínače, přečtěte si [virtualizace sítě Hyper-V a virtuálního přepínače](https://technet.microsoft.com/library/jj945275.aspx) článku.

S Zrychlený sítě síťový provoz dorazí na rozhraní sítě Virtuálního počítače (NIC) a předá ji virtuálního počítače. Všechny zásady sítě, které se vztahuje na virtuální přepínač se teď se sníženou zátěží a použít v hardwaru. Použití zásad v hardwaru umožňuje síťový adaptér přesměrovat provoz sítě přímo k virtuálnímu počítači, obcházení hostitele a virtuálního přepínače, při zachování všech zásad, které se použijí v hostiteli.

Výhody Zrychlený sítě se vztahují pouze na virtuálním počítači, který je zapnutá. Nejlepších výsledků dosáhnete je ideální pro povolení této funkce na alespoň dva virtuální počítače připojené ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojování místní, tato funkce má minimální dopad na celkové latence.

## <a name="benefits"></a>Výhody
* **Nižší latenci vyšší pakety za sekundu (pps):** odebráním virtuálního přepínače datapath odebere čas, který potřebují paketů v hostiteli pro zpracování zásad a zvyšuje počet paketů, které lze zpracovat ve virtuálním počítači.
* **Snižuje zpoždění:** virtuální přepínač zpracování závisí na množství zásady, které je nutné použít a zatížení procesoru, který provádí zpracování. Snižování zátěže vynucení zásad do hardwaru, odstraní tento variabilita tím, že doručování paketů přímo k virtuálnímu počítači, odebrání hostitele do komunikace virtuálních počítačů a všechny softwaru přerušení a kontext přepínače.
* **Snížení využití procesoru:** obcházení virtuální přepínač na hostiteli vede k menší využití procesoru při zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy
* **Ubuntu 16.04**: 4.11.0-1013 nebo vyšší verzi jádra
* **SLES 12 SP3**: 4.4.92-6.18 nebo vyšší verzi jádra
* **RHEL 7.4**: 7.4.2017120423 nebo vyšší verzi jádra
* **CentOS 7.4**: 7.4.20171206 nebo vyšší verzi jádra

## <a name="supported-vm-instances"></a>Podporované instance virtuálních počítačů
Zrychlený sítě je podporována v nejvíce obecné účely a velikostí optimalizovaných výpočetní instance s 4 nebo více Vcpu. V instancích například D/DSv3 nebo E/ESv3, které podporují Hyper-threadingem Accelerated sítě je podporována v instance virtuálních počítačů s 8 nebo více Vcpu.  Jsou podporované řady: D/DSv2, D/DSv3, E nebo ESv3, F nebo služby Fs nebo Fsv2 a Ms a Mms. 

Další informace o instance virtuálních počítačů najdete v tématu [velikosti virtuálního počítače s Linuxem](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastí Azure a také Azure Government Cloudy.

## <a name="limitations"></a>Omezení
Při použití této funkce, existují tato omezení:

* **Vytvoření rozhraní sítě:** Accelerated sítě lze povolit pouze pro nový síťový adaptér. Nelze nastavit pro existující síťovou.
* **Vytvoření virtuálního počítače:** A síťovým Adaptérem s Zrychlený sítě povolené lze připojit pouze k virtuálnímu počítači, když je vytvořen virtuální počítač. Síťový adaptér nelze připojit k existující virtuální počítač. Pokud Přidání virtuálního počítače do existující dostupnosti nastavena, všechny virtuální počítače v sadě dostupnosti musí také mít accelerated sítě povolené.
* **Nasazení prostřednictvím Správce Azure Resource Manager pouze:** virtuálních počítačů (klasické) nelze nasadit pomocí Accelerated sítě.

I když tento článek obsahuje kroky k vytvoření virtuálního počítače pomocí Zrychlený sítě pomocí rozhraní příkazového řádku Azure, můžete také [vytvoření virtuálního počítače pomocí Zrychlený sítě pomocí portálu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu, v části **nastavení**, vyberte **povoleno**v části **Accelerated sítě**. Možnost povolit Zrychlený sítě nebude se zobrazovat na portálu, pokud jste vybrali [podporovaný operační systém](#supported-operating-systems) a [velikost virtuálního počítače](#supported-vm-instances). Po vytvoření virtuálního počítače, které potřebujete k dokončení podle pokynů v [zkontrolujte, zda je povoleno Zrychlený sítě](#confirm-that-accelerated-networking-is-enabled).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte si nejnovější verzi [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlaste se k Azure účet pomocí [az přihlášení](/cli/azure/reference-index#az_login). V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *myNic*, a *Můjvp*.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *centralus* umístění:

```azurecli
az group create --name myResourceGroup --location centralus
```

Musíte vybrat podporovanou oblast Linux uvedené v [Linux accelerated sítě](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVnet* s jednou podsítí:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě
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

## <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvořte síťové rozhraní s Zrychlený sítě

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

## <a name="create-a-vm-and-attach-the-nic"></a>Vytvoření virtuálního počítače a připojte síťový adaptér
Při vytváření virtuálního počítače, zadejte na síťový adaptér jste vytvořili pomocí `--nics`. Je nutné vybrat velikost a distribuce uvedené v [Linux accelerated sítě](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

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

Po vytvoření virtuálního počítače, je vrácen výstup podobný výstupu v následujícím příkladu. Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači v postupných krocích.

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

## <a name="confirm-that-accelerated-networking-is-enabled"></a>Zkontrolujte, zda je povoleno Zrychlený sítě

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
