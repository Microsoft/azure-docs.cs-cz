---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97665000"
---
|  | **Point-to-site** | **Site-to-site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Podporované služby Azure** |Cloud Services a Virtual Machines |Cloud Services a Virtual Machines |[Seznam služeb](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Typické šířky pásma** |Podle skladové položky (SKU) brány |Obvykle celkem < 1 Gb/s |50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s |
| **Podporované protokoly** |Protokol SSTP (Secure Sockets Tunneling Protocol), OpenVPN a IPsec |IPsec |Přímé připojení přes sítě VLAN, technologie VPN od poskytovatelů síťových služeb (MPLS, VPLS…) |
| **Směrování** |RouteBased (dynamické) |Podporujeme sítě VPN se statickým směrováním (na základě zásad – PolicyBased) nebo dynamickým směrováním (na základě trasy – RouteBased). |BGP |
| **Odolnost připojení** |aktivní-pasivní |aktivní-aktivní nebo aktivní-pasivní |aktivní-aktivní |
| **Typický případ použití** |Zabezpečený přístup k virtuálním sítím Azure pro vzdálené uživatele |Scénáře pro vývoj/testování/laboratorní prostředí a pro cloudové služby a virtuální počítače s malým až středním škálováním |Přístup ke všem službám Azure (ověřený seznam), úlohy podnikové třídy a kritické úlohy, zálohování, velké objemy dat, Azure jako web pro zotavení po havárii |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Ceny** |[Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Ceny](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Technická dokumentace** |[Dokumentace k VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentace k VPN Gateway](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Dokumentace k ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **Nejčastější dotazy** |[Nejčastější dotazy k branám VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Nejčastější dotazy k branám VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute – nejčastější dotazy](../articles/expressroute/expressroute-faqs.md) |
