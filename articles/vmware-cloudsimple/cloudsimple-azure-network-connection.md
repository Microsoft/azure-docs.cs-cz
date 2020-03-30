---
title: Řešení VMware od CloudSimple – síťová připojení Azure
description: Informace o připojení virtuální sítě Azure k síti CloudSimple region
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025074"
---
# <a name="azure-network-connections-overview"></a>Přehled síťových připojení Azure

Když vytvoříte službu CloudSimple v oblasti a vytvoříte uzly, můžete:

* Požádejte o okruh Azure ExpressRoute a připojte jej k síti CloudSimple v této oblasti.
* Připojte svou síť CloudSimple region k virtuální síti Azure nebo místní síti pomocí Azure ExpressRoute.
* Poskytněte přístup ke službám spuštěným v předplatném Azure nebo místní síti z vašeho prostředí Privátního cloudu.

Připojení ExpressRoute má vysokou šířku pásma s nízkou latencí.

## <a name="benefits"></a>Výhody

Síťové připojení Azure umožňuje:

* Azure můžete použít jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery KMS ve svém předplatném Azure a šifrujte úložiště dat Private Cloud vSAN.
* Používejte hybridní aplikace, kde webová vrstva aplikace běží ve veřejném cloudu, zatímco vrstvy aplikací a databází běží ve vašem privátním cloudu.

## <a name="azure-virtual-network-connection"></a>Připojení virtuální sítě Azure

Privátní cloudy se můžou připojit k vašim prostředkům Azure pomocí ExpressRoute.  Připojení ExpressRoute umožňuje přístup k prostředkům spuštěném ve vašem předplatném Azure z vašeho privátního cloudu.  Toto připojení umožňuje rozšířit privátní cloudovou síť do virtuální sítě Azure.  Trasy ze sítě CloudSimple se budou vyměňovat s vaší virtuální sítí Azure prostřednictvím protokolu BGP.  Pokud máte nakonfigurovaný partnerský vztah virtuální sítě, všechny partnerské virtuální sítě budou přístupné z vaší sítě CloudSimple.

![Připojení K virtuální síti Azure ExpressRoute](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Připojení ExpressRoute k místní síti

Existující okruh Azure ExpressRoute můžete připojit k oblasti CloudSimple. Funkce ExpressRoute Global Reach se používá k vzájemnému propojení dvou okruhů.  Je navázáno připojení mezi místními a cloudsimple expressroute okruhy.  Toto připojení umožňuje rozšířit místní sítě do privátní cloudové sítě. Trasy z vaší sítě CloudSimple budou vyměňovány prostřednictvím protokolu BGP s místní sítí.

![Místní připojení ExpressRoute – globální dosah](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Připojení k místní síti a virtuální síti Azure

Připojení k místní síti a virtuální síti Azure mohou existovat společně z vaší sítě CloudSimple.  Připojení používá protokol BGP k výměně tras mezi místní sítí, virtuální sítí Azure a sítí CloudSimple.  Když připojíte svou síť CloudSimple k virtuální síti Azure v přítomnosti připojení Global Reach, trasy virtuální sítě Azure se budou viditelné v místní síti.  Výměna tras probíhá v Azure mezi hraničními směrovači.

![Místní připojení ExpressRoute s připojením virtuální sítě Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Důležité informace

Připojení k síti CloudSimple z místní sítě a z virtuální sítě Azure umožňuje výměnu tras mezi všemi sítěmi.

* Virtuální síť Azure bude viditelná jak z místní sítě, tak ze sítě CloudSimple.
* Pokud jste se připojili k virtuální síti Azure z místní sítě, připojení k síti CloudSimple pomocí globálního dosahu umožní přístup k virtuálním sítím ze sítě CloudSimple.
* Adresy podsítí **se nesmí** překrývat mezi žádnou připojenou sítí.
* CloudSimple **nebude** inzerovat výchozí trasu k připojení ExpressRoute
* Pokud váš místní směrovač inzeruje výchozí trasu, provoz ze sítě CloudSimple a virtuální sítě Azure použije inzerovanou výchozí trasu.  V důsledku toho virtuální počítače v Azure nelze přistupovat pomocí veřejných IP adres.

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure ke CloudSimple pomocí ExpressRoute](virtual-network-connection.md)
* [Připojení z místního prostředí ke CloudSimple pomocí ExpressRoute](on-premises-connection.md)
