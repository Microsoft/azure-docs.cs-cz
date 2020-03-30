---
title: Vytvoření virtuálního počítače Azure s akcelerací sítí pomocí azure cli
description: Přečtěte si, jak vytvořit virtuální počítač s Linuxem s povolenou zrychlenou sítí.
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
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 05f8430efa31b39d49025fb8456108da229d3d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239817"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Vytvoření virtuálního počítače s Linuxem se zrychlenou sítí pomocí azure CLI

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač (VM) Linux u akcelerované sítě. Pokud chcete vytvořit virtuální hod Windows se zrychlenou sítí, přečtěte si téma [Vytvoření virtuálního aplikace pro Windows s akceleračnou sítí](create-vm-accelerated-networking-powershell.md). Zrychlená síť umožňuje virtualizaci vstupně-videa jednoho kořene (SR-IOV) do virtuálního počítače, což výrazně zlepšuje jeho síťový výkon. Tato cesta vysokého výkonu obchází hostitele z cesty data, snižuje latenci, kolísání a využití procesoru pro použití s nejnáročnějšími síťovými úlohami na podporovaných typech virtuálních počítačů. Následující obrázek znázorňuje komunikaci mezi dvěma virtuálními zařízeními se zrychleným vytvářením sítí a bez něj:

![Srovnání](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez zrychlené sítě, všechny síťové provozy do a z virtuálního počítače musí procházet hostitele a virtuální přepínač. Virtuální přepínač poskytuje všechny zásady vynucení, jako jsou skupiny zabezpečení sítě, seznamy řízení přístupu, izolace a další síťové virtualizované služby pro síťový provoz. Další informace o virtuálních přepínačích najdete v článku [virtualizace sítě Hyper-V a virtuální přepínač.](https://technet.microsoft.com/library/jj945275.aspx)

Díky zrychlenému síťovému provozu se síťový provoz dostane do síťového rozhraní virtuálního počítače (NIC) a pak se přesměruje na virtuální počítač. Všechny zásady sítě, které virtuální přepínač platí, jsou nyní převedeny a použity v hardwaru. Použití zásad v hardwaru umožňuje síťové karty předávat síťový provoz přímo do virtuálního počítače, obcházet hostitele a virtuální přepínač při zachování všech zásad, které použila v hostiteli.

Výhody zrychleného vytváření sítí platí jenom pro virtuální počítač, na který je povolen. Pro dosažení nejlepších výsledků je ideální povolit tuto funkci alespoň na dvou virtuálních počítačích připojených ke stejné virtuální síti Azure (VNet). Při komunikaci mezi virtuálními sítěmi nebo připojení k místním, tato funkce má minimální dopad na celkovou latenci.

## <a name="benefits"></a>Výhody
* **Nižší latence / vyšší pakety za sekundu (pps):** Odebrání virtuálního přepínače z datové cesty odebere čas, který pakety stráví v hostiteli pro zpracování zásad, a zvýší počet paketů, které mohou být zpracovány uvnitř virtuálního počítače.
* **Snížená nervozita:** Zpracování virtuálního přepínače závisí na množství zásad, které je třeba použít, a na zatížení procesoru, který provádí zpracování. Překládka vynucení zásad na hardware odstraní tuto variabilitu doručením paketů přímo do virtuálního počítače, odebráním hostitele do komunikace virtuálního počítače a veškerých softwarových přerušení a přepnutí kontextu.
* **Snížené využití procesoru:** Vynechání virtuálního přepínače v hostiteli vede k menšímu využití procesoru pro zpracování síťového provozu.

## <a name="supported-operating-systems"></a>Podporované operační systémy
Z Galerie Azure jsou podporované následující distribuce: 
* **Ubuntu 14.04 s jádrem linux-azure**
* **Ubuntu 16.04 nebo novější** 
* **SLES12 SP3 nebo novější** 
* **RHEL 7.4 nebo novější**
* **CentOS 7.4 nebo novější**
* **CoreOS Linux**
* **Debian "Stretch" s jádrem backports**
* **Oracle Linux 7.4 a novější s jádrem kompatibilním s Red Hat (RHCK)**
* **Oracle Linux 7.5 a novější s UEK verze 5**
* **FreeBSD 10.4, 11.1 & 12.0**

## <a name="limitations-and-constraints"></a>Omezení a omezení

### <a name="supported-vm-instances"></a>Podporované instance virtuálních ms
Akcelerovaná síť je podporována pro většinu velikostí instancí pro obecné účely a optimalizované pro výpočetní výkon y se 2 nebo více virtuálními procesory.  Tyto podporované řady jsou: D/DSv2 a F/Fs

V instancích, které podporují hyperthreading, akcelerované sítě je podporována na instancích virtuálních počítačích se 4 nebo více virtuálních procesorů. Podporované řady jsou: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms a Ms/Mmsv2.

Další informace o instancích virtuálních počítačů najdete v tématu [velikosti virtuálních počítačích v Linuxu](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="custom-images"></a>Vlastní image
Pokud používáte vlastní bitovou kopii a vaše image podporuje akcelerované sítě, ujistěte se, že máte požadované ovladače pro práci s Mellanox ConnectX-3 a ConnectX-4 Lx síťové karty v Azure.

### <a name="regions"></a>Oblasti
K dispozici ve všech veřejných oblastech Azure i v cloudech Azure Government.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Povolení zrychlené sítě na spuštěném virtuálním počítači
Podporovaná velikost virtuálního počítače bez povolené zrychlené sítě může mít tuto funkci povolenou pouze v případě, že je zastavena a vrácena.  
### <a name="deployment-through-azure-resource-manager"></a>Nasazení prostřednictvím Správce prostředků Azure
Virtuální počítače (klasické) nelze nasadit s akcelerované sítě.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Vytvoření virtuálního počítače s Linuxem s Azure Accelerated Networking
## <a name="portal-creation"></a>Vytvoření portálu
I když tento článek obsahuje kroky k vytvoření virtuálního počítače s akcelerací sítí pomocí Azure CLI, můžete také [vytvořit virtuální počítač s akcelerované sítě pomocí portálu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Při vytváření virtuálního počítače na portálu zvolte v okně **Vytvořit virtuální počítač** kartu **Síť.**  Na této kartě je možnost **akcelerované sítě**.  Pokud jste zvolili [podporovaný operační systém](#supported-operating-systems) a velikost [virtuálního počítače](#supported-vm-instances), tato možnost se automaticky naplní na "Zapnuto".  Pokud ne, naplní možnost "Vypnuto" pro akcelerované sítě a poskytne uživateli důvod, proč není povolena.   

* *Poznámka:* Prostřednictvím portálu lze povolit pouze podporované operační systémy.  Pokud používáte vlastní image a vaše image podporuje akcelerované sítě, vytvořte virtuální počítač pomocí cli nebo PowerShell. 

Po vytvoření virtuálního počítače můžete potvrdit, že je povolena akcelerovaná síť podle pokynů v části [Potvrzení, že je povolena akcelerovaná síť](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Vytvoření cli
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Nainstalujte nejnovější [azure cli](/cli/azure/install-azure-cli) a přihlaste se k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index). V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnovaly *myResourceGroup*, *myNic*a *myVm*.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *centralus:*

```azurecli
az group create --name myResourceGroup --location centralus
```

Vyberte podporovanou oblast Linuxu uvedenou v [linuxovém zrychleném networkingu](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

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
Vytvořte skupinu zabezpečení sítě pomocí vytvoření [sítě az nsg](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Skupina zabezpečení sítě obsahuje několik výchozích pravidel, z nichž jedno zakáže veškerý příchozí přístup z Internetu. Otevřením portu povolíte přístup SSH k virtuálnímu počítači s [vytvořením pravidla az network nsg](/cli/azure/network/nsg/rule):

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

### <a name="create-a-network-interface-with-accelerated-networking"></a>Vytvoření síťového rozhraní se zrychleným vytvářením sítí

Vytvořte veřejnou IP adresu pomocí příkazu [az network public-ip create](/cli/azure/network/public-ip). Veřejná IP adresa není vyžadována, pokud nemáte v plánu získat přístup k virtuálnímu počítači z Internetu, ale k dokončení kroků v tomto článku je to nutné.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Vytvořte síťové rozhraní s [az network nic vytvořit](/cli/azure/network/nic) s povolenou zrychlenou sítí. Následující příklad vytvoří síťové rozhraní s názvem *myNic* v podsíti *mySubnet* virtuální sítě *myVNET* a přidruží k síťovému rozhraní skupinu zabezpečení sítě *myNetworkSecurityGroup:*

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

### <a name="create-a-vm-and-attach-the-nic"></a>Vytvoření virtuálního virtuálního připojení a připojení nic
Při vytváření virtuálního virtuálního soudu `--nics`zadejte nic, které jste vytvořili s . Vyberte velikost a distribuci uvedenou v [Linuxu akcelerované sítě](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální hod s názvem *myVM* s bitovou kopii UbuntuLTS a velikost, která podporuje akcelerované sítě *(Standard_DS4_v2*):

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

Seznam všech velikostí a charakteristik virtuálních počítačích najdete v tématu [Velikosti virtuálních počítačových uživatelů linuxu](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po vytvoření virtuálního virtuálního zařízení je vrácen výstup podobný následujícímu příkladu výstupu. Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu mněmu v následujících krocích.

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

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Potvrzení, že je povolena zrychlená síť

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Nahraďte `<your-public-ip-address>` veřejnou IP adresou přiřazenou virtuálnímu počítači, který jste `--admin-username` vytvořili, a nahraďte *azureusera,* pokud jste při vytváření virtuálního počítače použili jinou hodnotu.

```bash
ssh azureuser@<your-public-ip-address>
```

Z prostředí Bash `uname -r` zadejte a potvrďte, že verze jádra je jedna z následujících verzí nebo vyšší:

* **Ubuntu 16.04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **Centos**: 7.4.20171206


Potvrďte, že zařízení Mellanox VF `lspci` je vystaveno virtuálnímu virtuálnímu zařízení pomocí příkazu. Vrácený výstup je podobný následujícímu výstupu:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Zkontrolujte aktivitu na VF (virtuální `ethtool -S eth0 | grep vf_` funkce) s příkazem. Pokud obdržíte výstup podobný následujícímu ukázkovému výstupu, je povolena akcelerovaná síť a funguje.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Akcelerovaná síť je teď povolená pro váš virtuální počítač.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Zpracování dynamické vazby a zrušení virtuální funkce 
Aplikace musí spustit přes syntetickou kartu, která je vystavena ve virtuálním provozu. Pokud aplikace běží přímo přes vf nic, nepřijímá **všechny** pakety, které jsou určeny pro virtuální modul, protože některé pakety se zobrazí přes syntetické rozhraní.
Pokud spustíte aplikaci přes syntetickou nic, zaručuje, že aplikace obdrží **všechny** pakety, které jsou určeny k němu. Také zajišťuje, že aplikace udržuje spuštěna, i v případě, že VF je odvolán při provozu hostitele. Aplikace vazby na syntetickou síťovou kartu je **povinný** požadavek pro všechny aplikace s **využitím akcelerované sítě**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Povolení zrychlených sítí na stávajících virtuálních počítačích
Pokud jste vytvořili virtuální počítač bez akcelerované sítě, je možné povolit tuto funkci na existujícím virtuálním počítači.  Virtuální ho virtuálního zařízení musí podporovat zrychlené vytváření sítí splněním následujících požadavků, které jsou také popsané výše:

* Virtuální modul musí mít podporovanou velikost pro akcelerované sítě.
* Virtuální počítač musí být podporovaná image Galerie Azure (a verze jádra pro Linux)
* Všechny virtuální ho disponibilní v sadě dostupnosti nebo v VMSS musí být zastaveny nebo deallocated před povolením akcelerované sítě na libovolné síťové korynici

### <a name="individual-vms--vms-in-an-availability-set"></a>Jednotlivé virtuální virtuální aplikace & virtuálních mětek v sadě dostupnosti
První stop/deallocate virtuálního jevu nebo, pokud sada dostupnosti, všechny virtuální chody v sadě:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Důležité, upozorňujeme, že pokud váš virtuální počítač byl vytvořen jednotlivě, bez sady dostupnosti, stačí zastavit/navrátit jednotlivé virtuální počítač, aby bylo možné povolit akcelerované sítě.  Pokud váš virtuální počítač byl vytvořen s sadou dostupnosti, všechny virtuální počítače obsažené v sadě dostupnosti bude nutné zastavit nebo deallocated před povolením akcelerované sítě na některý z síťových připojení. 

Po zastavení povolte akcelerované sítě na síťové kasenici virtuálního počítače:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Restartujte virtuální počítač nebo, pokud v sadě dostupnosti, všechny virtuální počítače v sadě a zkontrolujte, že je povolená akcelerovaná síť: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>VMSS
VMSS se mírně liší, ale řídí se stejným pracovním postupem.  Nejdřív zastavte virtuální chod:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Po zastavení virtuálních zařízení aktualizujte vlastnost Accelerated Networking pod síťovým rozhraním:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Upozorňujeme, že služba VMSS má upgrady virtuálních zařízení, které používají aktualizace pomocí tří různých nastavení, automatického, postupného a ručního.  V těchto pokynech je zásada nastavena na automatické tak, aby VMSS bude vyzvednout změny ihned po restartování.  Chcete-li nastavit automatické tak, aby změny jsou okamžitě zvedl: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Nakonec restartujte Službu VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Po restartování počkejte na dokončení upgradů, ale po dokončení se v rámci virtuálního počítače zobrazí vrámci virtuálního počítače VF.  (Ujistěte se, že používáte podporovanou velikost operačního systému a virtuálního počítače.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Změna velikosti existujících virtuálních zařízení pomocí akcelerované sítě

Virtuální stránky s povolenou zrychlenou sítí lze velikost pouze na virtuální chod, které podporují akcelerované sítě.  

Virtuální ho virtuálního počítače s povolenou zrychlenou sítí nelze změnit velikost instance virtuálního počítače, která nepodporuje akcelerované sítě pomocí operace změny velikosti.  Místo toho chcete změnit velikost jednoho z těchto virtuálních počítače: 

* Stop/Deallocate virtuálního virtuálního serveru nebo pokud v dostupnosti set/VMSS, stop/navrátit všechny virtuální chody v sadě/VMSS.
* Akcelerované sítě musí být zakázány na síťové konto virtuálního počítači nebo pokud v dostupnosti set/VMSS, všechny virtuální chody v sadě/VMSS.
* Po zakázání akcelerované sítě lze sadu virtuálních her/dostupnosti/vmss přesunout do nové velikosti, která nepodporuje akcelerované sítě a restartován.  

