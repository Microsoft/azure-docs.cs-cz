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
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563159"
---
# <a name="azure-network-connections-overview"></a>Přehled připojení k síti Azure

Při vytváření služby CloudSimple v oblasti:

* Vytvoří okruh Azure ExpressRoute a připojí ho ke službě v této oblasti.
* Připojí vaši síť CloudSimple vaší oblasti k vaší virtuální síti Azure nebo k vaší místní síti pomocí Azure ExpressRoute.
* Poskytuje přístup ke službám, které běží v předplatném Azure nebo v místní síti z vašeho privátního cloudového prostředí.

Připojení ExpressRoute má velkou šířku pásma s nízkou latencí.

## <a name="benefits"></a>Výhody

Připojení k síti Azure vám umožní:

* Použijte Azure jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery služby správy klíčů v předplatném Azure pro šifrování úložiště dat privátního cloudu síti vSAN.
* Použijte hybridní aplikace, ve kterých se webová vrstva aplikace spouští ve veřejném cloudu, zatímco vrstvy aplikace a databáze běží ve vašem privátním cloudu.

## <a name="azure-virtual-network-connection"></a>Připojení k virtuální síti Azure

Privátní cloudy se dají připojit k prostředkům Azure pomocí ExpressRoute.  Připojení ExpressRoute umožňuje přístup k prostředkům běžícím v předplatném Azure z vašeho privátního cloudu.  Toto připojení umožňuje vaši síť privátního cloudu roztáhnout do vaší virtuální sítě Azure.

[![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute připojení k místní síti

Stávající okruh Azure ExpressRoute můžete připojit k vaší CloudSimple oblasti. Funkce ExpressRoute Global Reach slouží k propojení obou okruhů mezi sebou.  Mezi místními a CloudSimple ExpressRoute okruhy se naváže připojení.  Toto připojení umožňuje rozšiřování místních sítí do sítě privátního cloudu.

![Místní ExpressRoute připojení – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Další kroky

* [Připojení virtuální sítě Azure k CloudSimple pomocí ExpressRoute](virtual-network-connection.md)
* [Připojení z místního prostředí k CloudSimple pomocí ExpressRoute](on-premises-connection.md)
