---
title: Pomocí interní DNS pro překlad názvů virtuálních počítačů v Azure | Microsoft Docs
description: Pomocí interní DNS pro překlad názvů virtuálních počítačů v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: 73ecbb5bfcc99eb4507cac24b0539a04c3d8f521
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917274"
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Pomocí interní DNS pro překlad názvů virtuálních počítačů v Azure

Tento článek ukazuje, jak nastavit statické interní názvy DNS pro virtuální počítače s Linuxem pomocí karty virtuální síťovou kartu (VNic) a štítek názvy DNS. Statické názvy DNS jsou použity pro trvalé infrastruktury služby jako server volaných sestavení, který se používá k tomuto dokumentu nebo Git serveru.

Požadavky:

* [Účet Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Soubory veřejného a privátního klíče SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy

Pokud budete potřebovat rychle dosáhnout, v následující části jsou příkazy potřebné. Podrobnější informace a kontext pro každý krok naleznete zbývající části dokumentu, [od zde](#detailed-walkthrough).  

Předběžných požadavků: NSG skupinu prostředků, virtuální síť, pomocí protokolu SSH příchozí, podsíť.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Vytvoření adaptéru VNic pomocí statické interní název DNS

`-r` Příznak rozhraní příkazového řádku je k nastavení DNS popisku, který poskytuje statické název DNS adaptér VNic.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Virtuální počítač nasaďte do sítě VNet, NSG a připojte adaptér VNic

`-N` Připojí VNic do nového virtuálního počítače během nasazení do Azure.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Podrobný postup

Úplné průběžnou integraci a průběžné nasazování (CiCd) vyžaduje určitých serverech být statické nebo dlohotrvající servery infrastruktury v Azure.  Doporučujeme, aby Azure prostředky jako virtuální sítě (virtuální sítě) a skupiny zabezpečení sítě (Nsg), by měla být statická a dlouhodobě prostředky, které jsou nasazeny zřídka.  Po nasazený virtuální síť, můžete použít znovu nová nasazení bez žádné negativní ovlivňuje infrastruktury.  Přidání k této síti statické server úložiště Git a automatizační server volaných přináší CiCd do vašeho prostředí pro vývoj nebo testování.  

Interní názvy DNS jsou pouze přeložit uvnitř virtuální sítě Azure.  Protože se jedná o interní názvy DNS, nejsou přeložit na mimo Internetu, poskytuje dodatečné zabezpečení infrastruktury.

_Nahradí všechny příklady vlastní názvy._

## <a name="create-the-resource-group"></a>Vytvořte skupinu prostředků

K uspořádání vše, co se nám vytvořit v tomto návodu je nutné skupinu prostředků.  Další informace o skupinách prostředků Azure najdete v tématu [přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Vytvoření sítě VNet

Prvním krokem je vytvoření virtuální sítě ke spuštění virtuálních počítačů do.  Virtuální sítě obsahuje jednu podsíť pro účely tohoto postupu.  Další informace o virtuálních sítí Azure najdete v tématu [vytvoření virtuální sítě](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Vytvoření NSG

Podsíť je postavený za existující skupinu zabezpečení sítě, takže jsme sestavení NSG před podsíť.  Brána firewall síťové vrstvy odpovídají Azure skupiny Nsg.  Další informace o Azure Nsg najdete v tématu [vytvoření skupin Nsg v rozhraní příkazového řádku Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Přidat pravidlo povolit příchozí SSH

Virtuální počítač s Linuxem potřebuje přístup z Internetu, aby bylo pravidlo povolující příchozí port 22 provoz předávané port 22 přes síť, na virtuální počítač s Linuxem.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Přidat podsíť k virtuální síti

Virtuální počítače v rámci virtuální sítě musí být umístěny v podsíti.  Každý virtuální sítě může mít několik podsítí.  Vytvořte podsíť a podsíť přidružit NSG, chcete-li přidat bránu firewall k podsíti.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Podsíť je nyní přidána uvnitř virtuální sítě a přidružené k této skupině a pravidla NSG.

## <a name="creating-static-dns-names"></a>Vytvoření statické názvy DNS

Azure je velmi flexibilní, ale pokud chcete použít názvy DNS pro překlad názvů virtuálních počítačů, musíte je vytvořit jako virtuální síťové karty (VNics) pomocí označování DNS.  VNics jsou důležité, protože můžete znovu použít, je jejich připojením do různých virtuálních počítačů, zůstanou adaptéru VNic jako statické prostředek při virtuálních počítačů může být dočasné.  Pomocí DNS označování na kartě VNic jsou povolit jednoduchý překladu názvů z jiných virtuálních počítačů ve virtuální síti.  Použití přeložit názvy umožňuje ostatním virtuálním počítačům přístup k serveru automatizace podle názvu DNS `Jenkins` nebo server Git jako `gitrepo`.  Vytvoření adaptéru VNic a přidružte ji k podsíť vytvořená v předchozím kroku.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Virtuální počítač nasaďte do virtuální sítě a NSG

Nyní je k dispozici virtuální síť, podsíť uvnitř této virtuální sítě a skupinu NSG, který funguje jako brána firewall blokuje veškerý příchozí provoz, s výjimkou port 22 pro SSH chránit naše podsítě.  Virtuální počítač teď můžou být nasazené uvnitř této stávající síťové infrastruktuře.

Použití Azure CLI a `azure vm create` příkaz virtuálního počítače s Linuxem se nasadí do existující skupiny prostředků Azure, virtuální síť, podsíť a VNic.  Další informace o nasazení dokončení virtuálního počítače pomocí rozhraní příkazového řádku najdete v tématu [vytvořit úplný prostředí Linux pomocí rozhraní příkazového řádku Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

Pomocí rozhraní příkazového řádku příznaky vyvolávající existující prostředky jsme vyzvat Azure k nasazení virtuálních počítačů uvnitř existující síť.  Chcete-li zopakovat, jakmile nasazených virtuálních sítí a podsítí, může být ponecháno jako statické nebo trvalé prostředky uvnitř vaší oblasti Azure.  

## <a name="next-steps"></a>Další postup
* [Přímé vytvoření vlastního prostředí pro virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure CLI](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Vytvoření virtuálního počítače s Linuxem v Azure pomocí šablony](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
