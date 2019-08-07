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
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812706"
---
# <a name="azure-network-connections-overview"></a>Přehled připojení k síti Azure

Při vytváření služby CloudSimple v oblasti:

* Vytvoří okruh Azure ExpressRoute a připojí ho ke službě v této oblasti.
* Umožňuje připojení ze sítě vaší oblasti CloudSimple k vaší virtuální síti Azure nebo místní síti pomocí Azure ExpressRoute.
* Poskytuje přístup ke službám, které běží ve vašem předplatném Azure, nebo v místní síti z vašeho privátního cloudového prostředí.

Připojení je:

* Zabezpečení
* Soukromé
* Velká šířka pásma
* Nízká latence

## <a name="benefits"></a>Výhody

Připojení k síti Azure vám umožní:

* Použijte Azure jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery služby správy klíčů v předplatném Azure pro šifrování úložiště dat privátního cloudu síti vSAN.
* Použijte hybridní aplikace, ve kterých se webová vrstva aplikace spouští ve veřejném cloudu, zatímco vrstvy aplikace a databáze běží ve vašem privátním cloudu.

## <a name="azure-virtual-network-connection"></a>Připojení k virtuální síti Azure

Privátní cloudy se dají připojit k prostředkům Azure pomocí ExpressRoute.  Toto připojení můžete použít pro přístup k různým prostředkům běžícím v předplatném Azure z vašeho privátního cloudu.  Toto připojení umožňuje rozšířenou síť privátního cloudu do vaší virtuální sítě Azure.

![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute připojení k místní síti

Stávající okruh Azure ExpressRoute můžete připojit k vaší CloudSimple oblasti. Funkce ExpressRoute Global Reach slouží k propojení obou okruhů mezi sebou.  Mezi místními a CloudSimple ExpressRoute okruhy se naváže připojení.  Toto připojení umožňuje rozšiřování místních sítí do sítě privátního cloudu.

![Místní ExpressRoute připojení – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Další postup

* [Získání informací o partnerském vztahu pro virtuální síť Azure a připojení CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Připojení z místního prostředí k CloudSimple pomocí ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
