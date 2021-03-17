---
title: Řešení VMware podle CloudSimple – síťová připojení Azure
description: Přečtěte si informace o připojení virtuální sítě Azure k síti CloudSimple vaší oblasti.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025074"
---
# <a name="azure-network-connections-overview"></a>Přehled připojení k síti Azure

Když vytvoříte službu CloudSimple v oblasti a vytvoříte uzly, můžete:

* Vyžádejte si okruh Azure ExpressRoute a připojte ho k síti CloudSimple v této oblasti.
* Připojte síť CloudSimple region ke službě Azure Virtual Network nebo k vaší místní síti pomocí Azure ExpressRoute.
* Poskytněte přístup ke službám, které běží v předplatném Azure nebo v místní síti, z vašeho prostředí privátního cloudu.

Připojení ExpressRoute má velkou šířku pásma s nízkou latencí.

## <a name="benefits"></a>Výhody

Připojení k síti Azure vám umožní:

* Použijte Azure jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery služby správy klíčů v předplatném Azure pro šifrování úložiště dat privátního cloudu síti vSAN.
* Použijte hybridní aplikace, ve kterých se webová vrstva aplikace spouští ve veřejném cloudu, zatímco vrstvy aplikace a databáze běží ve vašem privátním cloudu.

## <a name="azure-virtual-network-connection"></a>Připojení k virtuální síti Azure

Privátní cloudy se dají připojit k prostředkům Azure pomocí ExpressRoute.  Připojení ExpressRoute umožňuje přístup k prostředkům běžícím v předplatném Azure z vašeho privátního cloudu.  Toto připojení umožňuje vaši síť privátního cloudu roztáhnout do vaší virtuální sítě Azure.  Trasy ze sítě CloudSimple budou vyměňovány pomocí služby Azure Virtual Network prostřednictvím protokolu BGP.  Pokud máte nakonfigurované partnerské vztahy virtuálních sítí, budou všechny virtuální sítě s partnerským vztahem dostupné ze sítě CloudSimple.

![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute připojení k místní síti

Stávající okruh Azure ExpressRoute můžete připojit k vaší CloudSimple oblasti. Funkce ExpressRoute Global Reach slouží k propojení obou okruhů mezi sebou.  Mezi místními a CloudSimple ExpressRoute okruhy se naváže připojení.  Toto připojení umožňuje rozšiřování místních sítí do sítě privátního cloudu. Trasy ze sítě CloudSimple budou vyměňovány prostřednictvím protokolu BGP s vaší místní sítí.

![Místní ExpressRoute připojení – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Připojení k místní síti a službě Azure Virtual Network

Připojení k místní síti a službě Azure Virtual Network můžou existovat ve vaší síti CloudSimple.  Připojení používá protokol BGP pro výměnu tras mezi místní sítí, službou Azure Virtual Network a CloudSimple sítí.  Když připojíte síť CloudSimple k vaší virtuální síti Azure v přítomnosti Global Reachho připojení, trasy virtuální sítě Azure budou viditelné ve vaší místní síti.  K trase Exchange dojde v Azure mezi hraničními směrovači.

![Připojení k virtuální síti Azure pomocí místního ExpressRoute připojení](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Důležité informace

Připojení k CloudSimple síti z místní sítě a z Azure Virtual Network umožňuje výměnu tras mezi všemi sítěmi.

* Virtuální síť Azure bude viditelná jak z místní sítě, tak z CloudSimple sítě.
* Pokud jste se připojili k virtuální síti Azure z místní sítě, připojení k CloudSimple síti pomocí Global Reach umožní přístup k virtuálním sítím ze sítě CloudSimple.
* Adresy podsítí **se nesmí** překrývat mezi žádnou připojenou sítí.
* CloudSimple nebude **inzerovat** výchozí trasu k připojením ExpressRoute.
* Pokud Váš místní směrovač inzeruje výchozí trasu, provoz ze sítě CloudSimple a virtuální sítě Azure bude používat inzerovanou výchozí trasu.  V důsledku toho se k virtuálním počítačům v Azure nelze dostat pomocí veřejných IP adres.

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure k CloudSimple pomocí ExpressRoute](virtual-network-connection.md)
* [Připojení z místního prostředí k CloudSimple pomocí ExpressRoute](on-premises-connection.md)
