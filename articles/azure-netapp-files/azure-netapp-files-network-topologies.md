---
title: Pokyny pro Azure NetApp Files plánování sítě | Microsoft Docs
description: Popisuje pokyny, které vám mohou pomoci při návrhu efektivní síťové architektury pomocí Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: 96d8ba058a33d408ec2ee2a1adfba9011f393da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184480"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Pokyny pro plánování sítě Azure NetApp Files

Plánování síťové architektury je klíčovým prvkem návrhu jakékoli aplikační infrastruktury. Tento článek vám pomůže navrhnout efektivní architekturu sítě pro vaše úlohy, aby bylo možné využít bohatých možností Azure NetApp Files.

Azure NetApp Files svazky jsou navržené tak, aby se obsahovaly v podsíti pro zvláštní účely označované jako [delegovaná podsíť](../virtual-network/virtual-network-manage-subnet.md) v rámci Azure Virtual Network. Proto můžete ke svazkům přistupovat přímo z vaší virtuální sítě, ze virtuální sítě partnerských uzlů ve stejné oblasti nebo z místní sítě přes bránu Virtual Network (ExpressRoute nebo VPN Gateway) podle potřeby. Podsíť je vyhrazená pro Azure NetApp Files a neexistuje žádné připojení k ostatním službám Azure nebo Internetu.

## <a name="considerations"></a>Požadavky  

Při plánování Azure NetApp Files sítě byste měli pochopit několik důležitých informací.

### <a name="constraints"></a>Omezení

Následující funkce jsou aktuálně pro Azure NetApp Files nepodporované: 

* Skupiny zabezpečení sítě (skupin zabezpečení sítě) použité pro delegovanou podsíť
* Trasy definované uživatelem (udr) použité pro delegovanou podsíť
* Zásady Azure (například vlastní zásady pojmenování) na rozhraní Azure NetApp Files
* Nástroje pro vyrovnávání zatížení pro Azure NetApp Files provoz
* Azure Virtual WAN 
* Zóny redundantní Virtual Network Branch (SKU brány pomocí AZ) 
* Aktivní/aktivní Virtual Network GWs 
* Síť VNet Dual Stack (IPv4 a IPv6)

Následující omezení sítě platí pro Azure NetApp Files:

* Počet IP adres, které se používají ve virtuální síti s Azure NetApp Files (včetně partnerských virtuální sítě), nesmí překročit 1000. Pracujeme na zvýšení tohoto limitu, aby se splnily požadavky na škálování zákazníka. 
* V každé službě Azure Virtual Network (VNet) je možné delegovat Azure NetApp Files jenom jednu podsíť.


### <a name="supported-network-topologies"></a>Podporované síťové topologie

Následující tabulka popisuje síťové topologie podporované nástrojem Azure NetApp Files.  Popisuje také alternativní řešení pro nepodporované topologie. 

|    Topologie    |    Je podporováno    |     Alternativní řešení    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Připojení ke svazku v místní síti VNet    |    Yes    |         |
|    Připojení ke svazku ve virtuální síti s partnerským vztahem (stejná oblast)    |    Yes    |         |
|    Připojení ke svazku ve virtuální síti s partnerským vztahem (mezi oblastí nebo globálním partnerským vztahem)    |    No    |    Žádné    |
|    Připojení ke svazku přes ExpressRoute bránu    |    Yes    |         |
|    Připojení z místního prostředí ke svazku ve virtuální síti rozbočovače prostřednictvím brány ExpressRoute a partnerského vztahu virtuálních sítí s přenosem brány    |    Yes    |        |
|    Připojení z místního prostředí k svazku ve virtuální síti rozbočovače přes bránu VPN    |    Yes    |         |
|    Připojení z místního prostředí k svazku ve virtuální síti rozbočovače prostřednictvím brány VPN a partnerského vztahu virtuálních sítí s přenosem brány    |    Yes    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuální síť pro Azure NetApp Files svazky

V této části najdete vysvětlení konceptů, které vám pomůžou s plánováním virtuálních sítí.

### <a name="azure-virtual-networks"></a>Virtuální sítě Azure

Před zřízením Azure NetApp Filesho svazku je potřeba vytvořit virtuální síť Azure (VNet) nebo použít ji, která už ve vašem předplatném existuje. Virtuální síť definuje hranice sítě pro daný svazek.  Další informace o vytváření virtuálních sítí najdete v dokumentaci k [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsítě

Podsítě segmentují virtuální síť do samostatných adresních prostorů, které jsou použitelné pro prostředky Azure v nich.  Azure NetApp Files svazky jsou obsaženy v podsíti pro speciální účely označované jako [delegovaná podsíť](../virtual-network/virtual-network-manage-subnet.md). 

Delegování podsítě poskytuje explicitní oprávnění pro službu Azure NetApp Files k vytváření prostředků specifických pro službu v podsíti.  Používá jedinečný identifikátor při nasazení služby. V takovém případě se vytvoří síťové rozhraní, které umožňuje připojení k Azure NetApp Files.

Pokud používáte novou virtuální síť, můžete vytvořit podsíť a delegovat ji tak, aby se Azure NetApp Files, a to podle pokynů v tématu [delegování podsítě na Azure NetApp Files](azure-netapp-files-delegate-subnet.md). Můžete také delegovat existující prázdnou podsíť, která už není delegovaná na jiné služby.

Pokud je virtuální síť v partnerském vztahu s jinou virtuální sítí, nemůžete rozšířit adresní prostor virtuální sítě. Z tohoto důvodu je potřeba novou delegovanou podsíť vytvořit v adresním prostoru virtuální sítě. Pokud potřebujete rozšířit adresní prostor, musíte před rozšířením adresního prostoru odstranit partnerský vztah virtuální sítě.

### <a name="udrs-and-nsgs"></a>Udr a skupin zabezpečení sítě

Trasy definované uživatelem (udr) a skupiny zabezpečení sítě (skupin zabezpečení sítě) nejsou podporovány v delegovaných podsítích pro Azure NetApp Files. Udr a skupin zabezpečení sítě ale můžete použít i v jiných podsítích, a to i v rámci stejné virtuální sítě jako podsíť delegované pro Azure NetApp Files.

* Udr pak nadefinujte toky přenosů z ostatních podsítí do Azure NetApp Files delegované podsítě. To pomáhá zajistit, aby se tento tok zarovnal do přenosu dat zpět od Azure NetApp Files k ostatním podsítím pomocí systémových tras.  
* Skupin zabezpečení sítě pak buď povolte, nebo zakažte provoz do a z Azure NetApp Files delegované podsítě. 

## <a name="azure-native-environments"></a>Nativní prostředí Azure

Následující diagram znázorňuje prostředí Azure Native:

![Azure – nativní síťové prostředí](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Místní virtuální síť

Základní scénář je vytvořit nebo připojit se k Azure NetApp Files svazku z virtuálního počítače ve stejné virtuální síti. Pro virtuální síť 2 v diagramu výše se svazek 1 vytvoří v delegované podsíti a dá se připojit k virtuálnímu počítači 1 ve výchozí podsíti.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Pokud máte další virtuální sítě ve stejné oblasti, která potřebuje přístup k prostředkům ostatních zdrojů, virtuální sítě se dá připojit pomocí [partnerského vztahu](../virtual-network/virtual-network-peering-overview.md) virtuálních sítí a povolit tak zabezpečené připojení prostřednictvím infrastruktury Azure. 

V diagramu výše zvažte virtuální síť 2 a virtuální síť 3. Pokud se virtuální počítač 1 potřebuje připojit k virtuálnímu počítači 2 nebo ke svazku 2 nebo pokud se virtuální počítač 2 potřebuje připojit k virtuálnímu počítači 1 nebo ke svazku 1, musíte povolit partnerský vztah virtuálních sítí mezi virtuálními sítěmi 2 a virtuální sítě 3. 

Zvažte také situaci, kdy je virtuální síť 1 partnerského vztahu s virtuální sítí 2 a virtuální síť 2 je v partnerském vztahu s virtuální sítí 3 ve stejné oblasti. Prostředky z virtuální sítě 1 se můžou připojit k prostředkům ve virtuální síti 2, ale nemůžou se připojit k prostředkům ve virtuální síti 3, pokud virtuální síť 1 a virtuální síť 3 nejsou partnerského vztahu. 

I když se virtuální počítač 3 může připojit ke svazku 1 výše, virtuální počítač 4 se nemůže připojit ke svazku 2.  Důvodem je to, že paprskový virtuální sítě nemá partnerský vztah a _směrování přenosu není podporováno prostřednictvím partnerského vztahu virtuálních sítí_.

## <a name="hybrid-environments"></a>Hybridní prostředí

Následující diagram znázorňuje hybridní prostředí: 

![Hybridní síťové prostředí](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

V hybridním scénáři potřebují aplikace z místních datových center přístup k prostředkům v Azure.  Jedná se o případ, že chcete své datacentrum rozšíříte do Azure nebo pokud chcete používat nativní služby Azure nebo pro zotavení po havárii. Informace o tom, jak propojit více prostředků v místním prostředí s prostředky v Azure pomocí sítě VPN typu Site-to-site nebo ExpressRoute, najdete VPN Gateway v tématu [Možnosti plánování](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) .

Virtuální síť rozbočovače v Azure v hybridní hvězdicové topologii funguje jako centrální bod připojení k vaší místní síti. Paprsky jsou virtuální sítě partnerského vztahu s rozbočovačem a lze je použít k izolaci úloh.

V závislosti na konfiguraci můžete k prostředkům v centru a paprskech připojit místní prostředky.

V topologii zobrazené výše je místní síť připojená k virtuální síti rozbočovače v Azure a ve stejné oblasti, ve které je partnerský virtuální síť rozbočovače, je 2 paprsková virtuální sítěá.  V tomto scénáři jsou možnosti připojení podporované pro Azure NetApp Files svazky následující:

* Virtuální počítače s místními prostředky a virtuálním počítačem 2 se můžou připojit ke svazku 1 v centru prostřednictvím sítě VPN typu Site-to-site nebo okruhu ExpressRoute. 
* Virtuální počítače s místními prostředky a VIRTUÁLNÍm počítačem 2 se mohou připojit ke svazku 2 nebo 3 prostřednictvím sítě VPN typu Site-to-site a regionálního partnerského vztahu VNET.
* Virtuální počítač 3 v síti rozbočovače se může připojit ke svazku 2 v síti VNet s paprsky 1 a svazkem 3 v síti VNet-paprsek 2.
* Virtuální počítač 4 z rozbočovače s koncovými virtuálními počítači 1 a virtuálního počítače 5 z virtuální sítě rozbočovače 2 se může připojit ke svazku 1 v síti VNet.
* Virtuální počítač 4 ve virtuální síti rozbočovače 1 se nemůže připojit ke svazku 3 ve virtuální síti rozbočovače 2. Virtuální počítač 5 v paprskovém VNet2 se navíc nemůže připojit ke svazku 2 v síti VNet-paprsek 1. Jedná se o případ, že virtuální sítě paprsků není v partnerském vztahu virtuálních _sítí podporována a směrování přenosu není podporováno_.
* V případě, že ve výše uvedené architektuře existuje brána ve virtuální síti rozbočovače, připojení k ANF svazku z Prem připojení přes bránu v centru bude ztraceno. Podle návrhu by se měla tato brána předávat bráně ve virtuální síti rozbočovače, takže se k ANF svazku připojí jenom počítače připojující se přes tuto bránu.

## <a name="next-steps"></a>Další kroky

[Delegování podsítě na službu Azure NetApp Files](azure-netapp-files-delegate-subnet.md)