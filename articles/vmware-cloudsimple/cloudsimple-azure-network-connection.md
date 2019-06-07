---
title: Řešení VMware podle CloudSimple – připojení k síti Azure
description: Další informace o připojení k síti CloudSimple oblasti vaší virtuální síti Azure
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497634"
---
# <a name="azure-network-connections-overview"></a>Přehled připojení sítě Azure

Při vytváření služby CloudSimple v oblasti, je:

* Vytvoří okruh Azure ExpressRoute a připojí ke službě v dané oblasti
* Umožňuje připojení z vaší sítě CloudSimple oblasti vaší virtuální sítí Azure a vaší místní sítí pomocí Azure ExpressRoute
* Poskytuje přístup ke službám, které běží na vaše předplatné Azure nebo v místní síti, z vašeho prostředí privátního cloudu

Připojení je:

* Zabezpečení
* Soukromé
* Velká šířka pásma
* Nízká latence

## <a name="benefits"></a>Výhody

Připojení k síti Azure vám umožní:

* Použití Azure jako cíl zálohování pro virtuální počítače na privátní Cloud.
* Nasaďte servery služby správy KLÍČŮ ve vašem předplatném Azure k zašifrování vaší sítě vSAN úložiště privátního cloudu.
* Pomocí hybridních aplikací, kde běží na úrovni webové aplikace ve veřejném cloudu při aplikaci a úrovně databáze spustit v privátního cloudu.

## <a name="azure-virtual-network-connection"></a>Připojení virtuální sítě Azure

Privátní Cloudy lze připojit k prostředkům Azure pomocí ExpressRoute.  Toto připojení můžete použít pro přístup k různým prostředkům spuštění ve vašem předplatném Azure z vaší privátní Cloud.  Toto připojení umožňuje rozšířit je privátní cloudové sítě ke službě Azure virtual network.

![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Připojení ExpressRoute do místní sítě

Stávající okruh Azure ExpressRoute můžete připojit k vaší CloudSimple oblasti. Globální dosah ExpressRoute funkce slouží k připojení dvou okruhů mezi sebou.  Vytvoří připojení mezi místními a okruhy CloudSimple ExpressRoute.  Toto připojení umožňuje rozšířit vaše místní sítě k síti privátního cloudu.

![Připojení ExpressRoute on-premises – globální dosah](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Další postup

* [Získat informace o vytvoření partnerského vztahu pro virtuální sítě Azure s CloudSimple připojení](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Připojení z místního na CloudSimple pomocí ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
