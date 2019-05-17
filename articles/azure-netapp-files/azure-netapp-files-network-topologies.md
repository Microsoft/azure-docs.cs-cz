---
title: Pokyny pro soubory Azure NetApp sítě plánování | Dokumentace Microsoftu
description: Popisuje pokyny, které vám může pomoci navrhnout efektivní síťové architektury s využitím Azure NetApp soubory.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
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
ms.openlocfilehash: 2afd5f0a574fd15c4327b141901d2651dbe2b9e5
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524232"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Pokyny pro plánování sítě Azure NetApp Files

Plánování architektury sítě je klíčovým prvkem návrhu jakékoli aplikační infrastruktury. Tento článek vám pomůže navrhnout efektivní síťové architekturu pro vaše úlohy využívat bohaté možnosti NetApp soubory Azure.

Azure svazky NetApp soubory jsou určeny k umístit do speciální podsítě s názvem [delegovaný podsítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) ve vaší virtuální síti Azure. Proto jsou přístupné svazky přímo z vaší virtuální sítě, z partnerských virtuálních sítích ve stejné oblasti nebo z místního Brána virtuální sítě (ExpressRoute nebo VPN Gateway) podle potřeby. Podsíť je vyhrazená pro soubory Azure NetApp a není k dispozici připojení k jiným službám Azure nebo k Internetu.

## <a name="considerations"></a>Požadavky  

Při plánování síťových souborů NetApp Azure, měli byste porozumět několik důležitých informací.

### <a name="constraints"></a>Omezení

Následující funkce nejsou aktuálně podporovány pro soubory Azure NetApp: 

* Skupiny zabezpečení sítě (Nsg) v podsíti
* Trasy definované uživatelem (udr) s dalším segmentem směrování podsítě souborů Azure NetApp
* Zásady Azure (například vlastní zásady přiřazování názvů) na rozhraní NetApp soubory Azure
* Nástroje pro vyrovnávání zatížení pro přenosy souborů NetApp Azure

Platí následující omezení sítě do služby soubory Azure NetApp:

* Číslo z IP adresy z virtuální sítě (včetně partnerských virtuálních sítích) připojení svazku ve virtuální síti nemůže být delší než 1000.
* V každé Azure Virtual Network (VNet) je možné jenom jednu podsíť delegovat do služby soubory Azure NetApp.


### <a name="supported-network-topologies"></a>Podporované topologie sítě

Následující tabulka popisuje síťové topologie, podporovaných souborů NetApp Azure.  Také popisuje alternativní řešení pro nepodporované topologie. 

|    Topologie    |    je podporováno    |     Alternativní řešení:    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Připojení ke svazku v místní virtuální sítě    |    Ano    |         |
|    Připojení ke svazku v partnerské virtuální síti (stejné oblasti)    |    Ano    |         |
|    Připojení ke svazku v partnerské virtuální síti (pro různé oblasti nebo globální partnerský vztah)    |    Ne    |    Žádný    |
|    Připojení ke svazku přes bránu ExpressRoute    |    Ano    |         |
|    Připojení z místního na svazek v paprsku virtuální sítě přes bránu ExpressRoute a partnerský vztah s průchod bránou    |    Ne    |    Vytvoření delegovaného podsítě ve virtuální síti (Azure VNet s bránou) centra    |
|    Připojení z místního na svazek v paprsku virtuální sítě přes bránu VPN    |    Ano    |         |
|    Připojení z místního na svazek v paprsku virtuální síti přes VPN gateway a partnerský vztah s průchod bránou    |    Ano    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuální sítě pro svazky souborů Azure NetApp

Tato část vysvětluje koncepty, které vám pomůžou při plánování virtuální sítě.

### <a name="azure-virtual-networks"></a>Virtuální sítě Azure

Před zřizováním soubory Azure NetApp svazku, budete muset vytvořit virtuální sítě Azure (VNet) nebo použijte takový, který již existuje v rámci vašeho předplatného. Virtuální síť definuje ohraničení sítě svazku.  Další informace o vytvoření virtuální sítě, najdete v článku [dokumentace ke službě Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Podsítě

Podsítě segmentaci virtuální sítě do samostatných adresní prostory, které jsou použitelné pro prostředky Azure v nich.  Azure souborů NetApp svazky jsou obsaženy ve speciálním účelem podsítě s názvem [delegovaný podsítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Podsíť delegování dává explicitní oprávnění pro službu Azure NetApp Files vytvářet prostředky specifické pro služby v podsíti.  Používá jedinečný identifikátor v nasazení služby. V takovém případě se vytvoří síťové rozhraní umožňuje připojení k Azure NetApp Files.

Pokud používáte novou virtuální síť, můžete vytvořit podsíť a delegovat podsítě do služby soubory Azure NetApp podle pokynů v [delegovat podsítě do služby soubory Azure NetApp](azure-netapp-files-delegate-subnet.md). Můžete taky delegovat existující prázdnou podsíť, která není již k jiným službám delegovat.

Pokud virtuální síť je v partnerském vztahu s jinou virtuální sítí, nelze rozšířit adresní prostor virtuální sítě. Z tohoto důvodu novou podsíť delegované musí být vytvořeny v rámci adresního prostoru virtuální sítě. Pokud potřebujete rozšířit adresní prostor, musíte odstranit partnerský vztah před rozšířením adresní prostor virtuální sítě.

### <a name="udrs-and-nsgs"></a>Trasy definované uživatelem a skupin zabezpečení sítě

Skupiny zabezpečení sítě (Nsg) s dalším segmentem směrování do nelze použít jako delegovaný podsítě pro soubory Azure NetApp. Podobně uživatelem definované trasy (udr) také nejsou podporovány. 

Jako alternativní řešení můžete použít skupiny Nsg do jiných podsítí, které buď povolovat nebo zamítat provoz do a z Azure Files NetApp delegovaný podsítě.  

## <a name="azure-native-environments"></a>Nativní prostředí Azure

Následující diagram znázorňuje prostředí Azure-native:

![Síťové prostředí Azure nativní](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Místní virtuální sítě

Základní scénář je vytvořit nebo připojit k Azure NetApp Files svazku z virtuálních počítačů (VM) ve stejné virtuální síti. 2 virtuální sítě ve výše uvedeném diagramu svazku 1 se vytvoří v delegované podsítě a je možné připojit na 1 virtuální počítač ve výchozím nastavení podsítě.

### <a name="vnet-peering"></a>VNET Peering

Pokud máte další virtuální sítě ve stejné oblasti, které potřebují přístup k prostředkům druhé strany, virtuální sítě můžou být připojené pomocí [VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) povolit zabezpečené připojení přes infrastrukturu Azure. 

Zvažte možnost virtuální sítě 2 a 3 virtuální sítě ve výše uvedeném diagramu. Pokud virtuální počítač 1 potřebuje pro připojení k virtuálnímu počítači 2 a svazek 2, nebo pokud virtuální počítač 2 vyžaduje připojení k virtuální počítač 1 nebo 1 svazek, je potřeba povolit partnerský vztah virtuální sítě 2 až 3 virtuální sítě. 

Kromě toho vezměte v úvahu scénář, kde 1 virtuální sítě je v partnerském vztahu s virtuální sítí 2 a partnerský vztah virtuální sítě 2 s 3 virtuální sítě ve stejné oblasti. Prostředky z virtuální sítě 1 může připojit k prostředkům ve virtuální síti 2, ale se nemůže připojit k prostředkům ve virtuální síti 3, pokud partnerský vztah virtuální sítě 1 a 3 virtuální sítě. 

Ve výše uvedeném diagramu i když 3 virtuálního počítače může připojit k svazku 1 nemůže 4 virtuální počítač připojit k 2 svazku.  Důvodem je, že nejsou v partnerském vztahu virtuálních sítí paprsků, a _směrování provozu není podporováno přes partnerský vztah virtuální sítě_.

## <a name="hybrid-environments"></a>Hybridní prostředí

Následující diagram znázorňuje hybridním prostředí: 

![Hybridní síťové prostředí](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

Aplikace z místních datových center v hybridním scénáři potřebují přístup k prostředkům v Azure.  To platí, zda chcete rozšířit svoje datové centrum do Azure, nebo chcete použít nativní služby Azure nebo zotavení po havárii. Zobrazit [VPN Gateway možnosti plánování](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) o tom, jak připojit více prostředkům na pracovišti na prostředky v Azure prostřednictvím sítě site-to-site VPN nebo ExpressRoute.

V hybridní topologii centra s paprsky centrum, virtuální síť v Azure funguje jako ústřední bod připojení k vaší místní síti. Paprsky jsou virtuální sítě v partnerském vztahu s centrem a slouží k izolaci úloh.

V závislosti na konfiguraci. Prostředky můžete připojit z místního na prostředky v centru a aby se paprsky mezi.

V topologii uvedená výše a místní sítí se připojila k rozbočovači virtuální síť v Azure a existují 2 paprsku, který virtuálních sítí v partnerském vztahu s virtuální síti centra.  V tomto scénáři jsou možnosti připojení, nepodporuje pro soubory Azure NetApp svazky:

* Místní prostředky virtuálních počítačů 1 a 2 virtuálního počítače může připojit k svazku 1 v centru přes síť site-to-site VPN nebo ExpressRoute. 
* Místní prostředky virtuálních počítačů 1 a 2 virtuálního počítače může připojit k svazku 2 nebo 3 svazku.
* 3 virtuální počítač v centru virtuální sítě můžete připojit k svazku v paprsku 1 virtuální sítě 2 a 3 svazku v paprsku 2 virtuální sítě.
* 4 virtuální počítač v paprsku 1 virtuální síť a 5 virtuálních počítačů z paprskem 2 virtuální sítě můžete připojit k svazku 1 ve virtuální síti centra.

4 virtuální počítač v paprsku 1 virtuální síť nemůže připojit ke svazku 3 v paprsku 2 virtuální sítě. Navíc 5 virtuálních počítačů v paprsku ze sítě VNet2 nemůže připojit ke svazku 2 v paprsku 1 virtuální sítě. To je případ, protože nejsou v partnerském vztahu virtuálních sítí paprsků a _směrování provozu není podporováno přes partnerský vztah virtuální sítě_.

## <a name="next-steps"></a>Další postup

[Delegát podsítě do služby soubory Azure NetApp](azure-netapp-files-delegate-subnet.md)