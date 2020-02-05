---
title: Řešení VMware (AVS) – síťová připojení Azure
description: Přečtěte si informace o připojení virtuální sítě Azure k síti vaší sítě služby AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025074"
---
# <a name="azure-network-connections-overview"></a>Přehled připojení k síti Azure

Když vytvoříte službu AVS v oblasti a vytvoříte uzly, můžete:

* Vyžádejte si okruh Azure ExpressRoute a připojte ho k síti služby AVS v této oblasti.
* Připojte vaši síť služby AVS do vaší virtuální sítě Azure nebo místní sítě pomocí Azure ExpressRoute.
* Poskytněte přístup ke službám, které běží v předplatném Azure nebo v místní síti, z vašeho prostředí privátního cloudu.

Připojení ExpressRoute má velkou šířku pásma s nízkou latencí.

## <a name="benefits"></a>Výhody

Připojení k síti Azure vám umožní:

* Použijte Azure jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery služby správy klíčů v předplatném Azure pro šifrování úložiště dat privátního cloudu síti vSAN.
* Použijte hybridní aplikace, ve kterých se webová vrstva aplikace spouští ve veřejném cloudu služby AVS, zatímco vrstvy aplikace a databáze běží ve vašem privátním cloudu.

## <a name="azure-virtual-network-connection"></a>Připojení k virtuální síti Azure

Privátní cloudy služby AVS můžete připojit k prostředkům Azure pomocí ExpressRoute. Připojení ExpressRoute umožňuje přístup k prostředkům běžícím v předplatném Azure z vašeho privátního cloudu služby AVS. Toto připojení umožňuje roztáhnout privátní cloudovou síť služby AVS do vaší virtuální sítě Azure. Trasy ze sítě služby AVS budou vyměňovány pomocí služby Azure Virtual Network prostřednictvím protokolu BGP. Pokud máte nakonfigurované partnerské vztahy virtuálních sítí, všechny partnerské virtuální sítě budou přístupné ze sítě AVS.

![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute připojení k místní síti

Stávající okruh Azure ExpressRoute můžete připojit k vaší oblasti služby AVS. Funkce ExpressRoute Global Reach slouží k propojení obou okruhů mezi sebou. Navázalo se připojení mezi místními a ExpressRoute okruhy AVS. Toto připojení umožňuje rozšiřování místních sítí do sítě privátního cloudu služby AVS. Trasy ze sítě služby AVS budou vyměňovány prostřednictvím protokolu BGP s vaší místní sítí.

![Místní ExpressRoute připojení – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Připojení k místní síti a službě Azure Virtual Network

Připojení k místní síti a službě Azure Virtual Network se můžou nacházet ze sítě služby AVS. Připojení používá protokol BGP pro výměnu tras mezi místní sítí, virtuální sítí Azure a sítí služby AVS. Když připojíte síť služby AVS ke službě Azure Virtual Network v přítomnosti Global Reachho připojení, budou se v místní síti zobrazovat trasy virtuální sítě Azure. K trase Exchange dojde v Azure mezi hraničními směrovači.

![Připojení k virtuální síti Azure pomocí místního ExpressRoute připojení](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Důležité informace

Připojení k síti služby AVS z místní sítě a/nebo z Azure Virtual Network umožňuje výměnu tras mezi všemi sítěmi.

* Virtuální síť Azure bude viditelná jak v místní síti, tak i v síti služby AVS.
* Pokud jste se připojili k virtuální síti Azure z místní sítě, připojení k síti služby AVS pomocí Global Reach umožní přístup k virtuálním sítím ze sítě AVS.
* Adresy podsítí **se nesmí** překrývat mezi žádnou připojenou sítí.
* AVS **neoznamuje výchozí** trasu k připojením ExpressRoute
* Pokud Váš místní směrovač inzeruje výchozí trasu, provoz ze sítě AVS a virtuální sítě Azure bude používat inzerovanou výchozí trasu. V důsledku toho se k virtuálním počítačům v Azure nelze dostat pomocí veřejných IP adres.

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure ke službě AVS pomocí ExpressRoute](virtual-network-connection.md)
* [Připojení z místního prostředí k funkci AVS pomocí ExpressRoute](on-premises-connection.md)
