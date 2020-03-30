---
title: Pokyny pro plánování sítě Soubory Azure NetApp | Dokumenty společnosti Microsoft
description: Popisuje pokyny, které vám můžou pomoct navrhnout efektivní síťovou architekturu pomocí souborů Azure NetApp.
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
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242480"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Pokyny pro plánování sítě Azure NetApp Files

Plánování síťové architektury je klíčovým prvkem návrhu libovolné aplikační infrastruktury. Tento článek vám pomůže navrhnout efektivní síťovou architekturu pro vaše úlohy, abyste měli prospěch z bohatých funkcí souborů Azure NetApp.

Svazky souborů Azure NetApp jsou navržené tak, aby byly obsaženy v podsíti pro speciální účely nazývané [delegovaná podsíť](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) v rámci virtuální sítě Azure. Proto můžete přistupovat ke svazkům přímo z vaší virtuální sítě, z partnerských virtuálních sítí ve stejné oblasti nebo z místních prostředků přes bránu virtuální sítě (ExpressRoute nebo VPN Gateway) podle potřeby. Podsíť je vyhrazená pro soubory Azure NetApp a neexistuje žádné připojení k jiným službám Azure nebo k Internetu.

## <a name="considerations"></a>Požadavky  

Měli byste pochopit několik aspektů při plánování sítě Azure NetApp Files.

### <a name="constraints"></a>Omezení

Níže uvedené funkce nejsou aktuálně nepodporované pro soubory Azure NetApp: 

* Skupiny zabezpečení sítě (NSG) použité v delegované podsíti
* Uživatelem definované trasy (UDR) s předponou adresy jako podsíť souborů Azure NetApp
* Zásady Azure (například vlastní zásady pojmenování) v rozhraní Azure NetApp Files
* Nástroji pro vyrovnávání zatížení pro provoz souborů Azure NetApp

Následující síťová omezení platí pro soubory Azure NetApp:

* Počet IP adresy v ypoužívají ve virtuální síti se soubory Azure NetApp (včetně partnerských virtuálních sítí) nesmí překročit 1000. Pracujeme na zvýšení tohoto limitu, abychom vyhověli požadavkům na zákaznickou stupnici. V mezidobí, pokud požadujete více IP adresy, obraťte se na náš tým podpory s případem použití a požadovaným limitem.
* V každé virtuální síti Azure (VNet) lze delegovat jenom jednu podsíť na soubory Azure NetApp.


### <a name="supported-network-topologies"></a>Podporované síťové topologie

Následující tabulka popisuje topologie sítě podporované soubory Azure NetApp.  Také popisuje řešení pro nepodporované topologie. 

|    Topologie    |    Je podporována    |     Alternativní řešení    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Připojení ke svazku v místní virtuální síti    |    Ano    |         |
|    Připojení ke svazku v partnerské virtuální síti (stejná oblast)    |    Ano    |         |
|    Připojení ke svazku v partnerské virtuální síti (mezi oblastnebo globální partnerský vztah)    |    Ne    |    Žádný    |
|    Připojení ke svazku přes bránu ExpressRoute    |    Ano    |         |
|    Připojení z místního prostředí ke svazku ve virtuální síti s paprskem přes bránu ExpressRoute a partnerský vztah virtuální sítě s přenosem brány    |    Ano    |        |
|    Připojení z místního k svazku ve virtuální síti pro paprsky přes bránu VPN    |    Ano    |         |
|    Připojení z místního k svazku ve virtuální síti pro paprsku přes bránu VPN a partnerský vztah virtuální sítě s přenosem brány    |    Ano    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuální síť pro svazky souborů Azure NetApp

Tato část vysvětluje koncepty, které vám pomohou s plánováním virtuální sítě.

### <a name="azure-virtual-networks"></a>Virtuální sítě Azure

Před zřízením svazku souborů Azure NetApp je potřeba vytvořit virtuální síť Azure (VNet) nebo použít ten, který už ve vašem předplatném existuje. Virtuální síť definuje síť hranice svazku.  Další informace o vytváření virtuálních sítí najdete v [dokumentaci k virtuální síti Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Podsítě

Podsítě segmentvirtuální sítě do samostatných adresových prostorů, které jsou použitelné prostředky Azure v nich.  Svazky souborů Azure NetApp jsou obsaženy v podsíti pro speciální účely nazývané [delegovaná podsíť](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Delegování podsítě uděluje explicitní oprávnění službě Soubory Azure NetApp k vytvoření prostředků specifických pro službu v podsíti.  Používá jedinečný identifikátor při nasazování služby. V takovém případě je vytvořeno síťové rozhraní umožňující připojení k souborům Azure NetApp.

Pokud používáte novou virtuální síť, můžete vytvořit podsíť a delegovat podsíť na soubory Azure NetApp podle pokynů v [příkazech delegovat podsíť na soubory Azure NetApp](azure-netapp-files-delegate-subnet.md). Můžete také delegovat existující prázdnou podsíť, která ještě není delegována na jiné služby.

Pokud virtuální síť je partnerský vztah s jinou virtuální sítí, nelze rozbalit adresní prostor virtuální sítě. Z tohoto důvodu je třeba vytvořit novou delegovanou podsíť v adresním prostoru virtuální sítě. Pokud potřebujete rozšířit adresní prostor, musíte před rozbalením adresního prostoru odstranit partnerský vztah virtuální sítě.

### <a name="udrs-and-nsgs"></a>UDR a NSG

Uživatelem definované trasy (UDR) a skupiny zabezpečení sítě (NSG) nejsou podporovány v delegovaných podsítích pro soubory Azure NetApp.

Jako řešení můžete použít skupiny zabezpečení sítě pro jiné podsítě, které buď povolit nebo odepřít provoz do a z azure netapp soubory delegované podsítě.  

## <a name="azure-native-environments"></a>Nativní prostředí Azure

Následující diagram znázorňuje nativní prostředí Azure:

![Síťové prostředí nativní pro Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Místní virtuální síť

Základním scénářem je vytvoření nebo připojení ke svazku souborů Azure NetApp z virtuálního počítače (VM) ve stejné virtuální síti. Pro virtuální síť 2 ve výše uvedeném diagramu se svazek 1 vytvoří v delegované podsíti a lze jej připojit na Virtuální počítač 1 ve výchozí podsíti.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Pokud máte další virtuální sítě ve stejné oblasti, které potřebují přístup k navzájem prostředky, virtuální sítě můžete připojit pomocí [partnerského vztahu virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) k povolení zabezpečené připojení prostřednictvím infrastruktury Azure. 

Zvažte Virtuální síť 2 a Virtuální síť 3 ve výše uvedeném diagramu. Pokud se virtuální montovna 1 potřebuje připojit k Virtuálnímu zařízení 2 nebo ke svazku 2 nebo pokud se virtuální montovna 2 potřebuje připojit k Virtuálnímu virtuálnímu zařízení 1 nebo ke svazku 1, musíte povolit partnerský vztah virtuální sítě mezi virtuální sítí 2 a virtuální sítí 3. 

Kromě toho zvažte scénář, kde virtuální síť 1 je partnerský vztah s virtuální sítí 2 a virtuální síť 2 je partnerský vztah s Virtuální síť 3 ve stejné oblasti. Prostředky z virtuální sítě 1 se můžou připojit k prostředkům ve virtuální síti 2, ale nemohou se připojit k prostředkům ve Virtuální síti 3, pokud nejsou partnerskými partnery virtuální sítě 1 a 3. 

Ve výše uvedeném diagramu, i když v Dot 3 můžete připojit ke svazku 1, VM 4 nelze připojit ke svazku 2.  Důvodem je, že virtuální sítě paprsku nejsou partnerským vztahem a _směrování tranzitu není podporováno přes partnerský vztah virtuální sítě_.

## <a name="hybrid-environments"></a>Hybridní prostředí

Následující diagram znázorňuje hybridní prostředí: 

![Hybridní síťové prostředí](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

V hybridním scénáři potřebují aplikace z místních datových center přístup k prostředkům v Azure.  To je případ, zda chcete rozšířit své datové centrum do Azure, nebo chcete použít nativní služby Azure nebo pro zotavení po havárii. Informace o tom, jak připojit více místních prostředků k prostředkům v Azure prostřednictvím sítě VPN nebo ExpressRoute, najdete v tématu [možnosti plánování brány VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)

V topologii hybridních uzlů funguje virtuální síť rozbočovače v Azure jako centrální bod připojení k vaší místní síti. Paprsky jsou virtuální sítě partnerský s rozbočovačem a lze je použít k izoluje úlohy.

V závislosti na konfiguraci můžete připojit místní prostředky k prostředkům v centru a paprsky.

V topologii ilustrované výše je místní síť připojena k virtuální síti rozbočovače v Azure a ve stejné oblasti jsou virtuální sítě s 2 paprsky ve stejné oblasti, která je propojena s virtuální sítí rozbočovače.  V tomto scénáři možnosti připojení podporované pro svazky Souborů Azure NetApp jsou následující:

* Místní prostředky VM 1 a VM 2 se můžou připojit ke svazku 1 v rozbočovači přes okruh VPN nebo ExpressRoute. 
* Místní prostředky VM 1 a VM 2 se můžou připojit ke svazku 2 nebo svazku 3 přes síť VPN typu site-to-site a regionální partnerský vztah virtuální sítě.
* Virtuální virtuální připojení 3 ve virtuální síti rozbočovače se může připojit ke svazku 2 ve virtuální síti 1 s paprskem a ve svazku 3 ve virtuální síti 2 s paprskem.
* Virtuální va 4 z virtuální sítě 1 a Virtuálního Měna 5 z virtuální sítě s paprskem se můžou připojit ke svazku 1 ve virtuální síti rozbočovače.

Virtuální měna 4 ve virtuální síti 1 s paprskem se nemůže připojit ke svazku 3 ve virtuální síti 2 s paprskem. Také VM 5 ve virtuální síti s paprskem se nemůže připojit ke svazku 2 ve virtuální síti 1 s paprskem. To je případ, protože virtuální sítě paprsku nejsou partnerský vztah a _směrování tranzitu není podporováno přes partnerský vztah virtuální sítě_.

## <a name="next-steps"></a>Další kroky

[Delegování podsítě do Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
