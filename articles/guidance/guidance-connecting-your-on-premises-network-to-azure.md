---
title: Připojení vaší místní sítě do Azure | Microsoft Docs
description: Popisuje a porovná různé metody dostupné pro připojení ke cloudovým službám Microsoftu, jako je například Azure, Office 365 a Dynamics CRM Online.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2018
ms.locfileid: "23851012"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Připojení vaší místní sítě do Azure
Společnost Microsoft poskytuje několik typů cloudových služeb. Můžete se sice připojit ke všem službám prostřednictvím veřejného Internetu, můžete také připojit k některým službám pomocí tunelového propojení virtuální privátní sítě (VPN) přes Internet nebo prostřednictvím přímé a privátní připojení společnosti Microsoft. Tento článek vám pomůže určit možnosti připojení, kterou bude nejlépe vyhovovat vašim potřebám, v závislosti na typech cloudové služby společnosti Microsoft, které spotřebujete. Většina organizací využívat více typů připojení, které jsou popsané dál.

## <a name="connecting-over-the-public-internet"></a>Připojení prostřednictvím veřejného Internetu
Tento typ připojení poskytuje přístup ke cloudovým službám Microsoftu přímo přes Internet, jak je uvedeno níže.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Toto připojení je obvykle první typ používané pro připojení ke cloudovým službám Microsoftu. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Vyžaduje žádné změny v místní síti, dokud všechny klientská zařízení mají neomezený přístup ke všem IP adresy a porty v síti Internet. |I když provoz často šifrované pomocí protokolu HTTPS, mohou být zachyceny během přenosu, protože se prochází veřejného Internetu. |
| Můžete se připojit ke všem cloudovým službám Microsoftu zveřejněné do veřejného Internetu. |Nepředvídatelným latence vzhledem k tomu, že připojení prochází přes Internet. |
| Pomocí existujícího připojení k Internetu. | |
| Nevyžaduje správu zařízení žádné připojení. | |

Toto připojení nemá žádnou připojení nebo náklady na šířku pásma, protože používáte existující připojení k Internetu.

## <a name="connecting-with-a-point-to-site-connection"></a>Připojení pomocí připojení point-to-site
Tento typ připojení poskytuje přístup k některé cloudových služeb Microsoftu prostřednictvím tunelu Secure Socket Tunneling Protocol (SSTP) přes Internet, jak je uvedeno níže.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "připojeníPoint-to-site")

Připojení se provádí přes existující připojení k Internetu, ale vyžaduje použití služby Azure VPN Gateway. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Vyžaduje žádné změny v místní síti, dokud všechny klientská zařízení mají neomezený přístup ke všem IP adresy a porty v síti Internet. |I když přenosy jsou šifrována pomocí protokolu IPSec, mohou být zachyceny během přenosu, protože se prochází veřejného Internetu. |
| Pomocí existujícího připojení k Internetu. |Nepředvídatelným latence vzhledem k tomu, že připojení prochází přes Internet. |
| Propustnost až 200 Mb/s na bránu. |Vyžaduje vytváření a správa samostatné připojení mezi každé zařízení ve vaší místní síti a každou bránu, kterou každé zařízení potřebuje připojit k. |
| Slouží k připojení ke službám Azure, které lze připojit k virtuálním sítím Azure (VNet) jako jsou virtuální počítače Azure a cloudových služeb Azure. |Vyžaduje minimální průběžnou správu služby Azure VPN Gateway. |
| Nelze použít pro připojení k Microsoft Office 365 nebo Dynamics CRM Online. | |
| Nelze použít pro připojení ke službám Azure, které nelze se připojit k virtuální síti. | |

Další informace o [brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) služby, jeho [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)a odchozí přenosy dat [ceny](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Připojení pomocí připojení site-to-site
Tento typ připojení poskytuje přístup k některé cloudových služeb Microsoftu prostřednictvím tunelového propojení protokolu IPSec přes Internet, jak je uvedeno níže.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "připojení Site-to-site")

Připojení se provádí přes existující připojení k Internetu, ale vyžaduje použití služby Azure VPN Gateway s jeho přidružené ceny a odchozí přenosy dat – ceny. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Všechna zařízení ve vaší místní síti může komunikovat se službami Azure připojený k virtuální síti, takže není nutné ke konfiguraci jednotlivých připojení pro každé zařízení. |I když přenosy jsou šifrována pomocí protokolu IPSec, mohou být zachyceny během přenosu, protože se prochází veřejného Internetu. |
| Pomocí existujícího připojení k Internetu. |Nepředvídatelným latence vzhledem k tomu, že připojení prochází přes Internet. |
| Slouží k připojení ke službám Azure, které lze připojit k virtuální síti jako jsou virtuální počítače a cloudové služby. |Musíte nakonfigurovat a spravovat ověřené VPN zařízení * místně. |
| Propustnost až 200 Mb/s na bránu. |Vyžaduje minimální průběžnou správu služby Azure VPN Gateway. |
| Můžete vynutit odchozí provoz inicializována z cloudu virtuálních počítačů prostřednictvím místní sítě pro kontrolu a protokolování pomocí trasy definované uživatelem nebo je protokol BGP (Border Gateway) **. |Nelze použít pro připojení k Microsoft Office 365 nebo Dynamics CRM Online. |
| Nelze použít pro připojení ke službám Azure, které nelze se připojit k virtuální síti. | |
| Pokud byste používat služby, které zahájí připojení zpět na místní zařízení, a to vyžadují vaše zásady zabezpečení, musíte bránu firewall mezi místní sítí a Azure. | |

* * Zobrazení seznamu [ověřit zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Další informace o používání [trasy definované uživatelem](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) nebo [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) vynutit směrování z virtuální sítě Azure do místní zařízení.

## <a name="connecting-with-a-dedicated-private-connection"></a>Připojíte přes vyhrazené soukromé připojení
Tento typ připojení poskytuje přístup ke všem cloudovým službám Microsoftu přes vyhrazené soukromé připojení společnosti Microsoft, který není procházení Internetu, jak je uvedeno níže.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "připojení ExpressRoute")

Připojení vyžaduje použití služby ExpressRoute a připojení k poskytovatele připojení. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Provoz nelze zachytili při přenosu prostřednictvím veřejného Internetu, vzhledem k tomu, že se používá jako vyhrazené připojení prostřednictvím poskytovatele služeb. |Vyžaduje místní směrovač správy. |
| Šířka pásma až 10 Gb/s na okruh ExpressRoute a propustnost až 2 Gb/s pro každou bránu. |Vyžaduje vyhrazenou připojení pro poskytovatele připojení. |
| Předvídatelnou latenci, protože se jedná o vyhrazené připojení ke společnosti Microsoft, která není procházení Internetu. |Může vyžadovat minimální průběžnou správu jednu nebo více bran VPN Azure (Pokud je připojení okruhu do virtuální sítě). |
| Nevyžaduje šifrovanou komunikaci, i když můžete šifrovat provoz, v případě potřeby. |Pokud používáte cloudové služby, které zahájí připojení zpět na místní zařízení, musíte bránu firewall mezi místní sítí a Azure. |
| Mohou připojovat přímo k všem cloudovým službám Microsoftu, s několika výjimkami *. |Vyžaduje místní IP adres zadávat datových Center společnosti Microsoft pro služby, které nelze se připojit k VNet.* * překlad síťových adres (NAT) |
| Vynutit odchozí data odeslaná z cloudu virtuálních počítačů prostřednictvím místní sítě pro kontrolu a protokolování pomocí protokolu BGP. | |

* * Zobrazení [seznam služeb](../expressroute/expressroute-faqs.md#supported-services) , nelze použít s ExpressRoute. Vaše předplatné Azure musí být schválen pro připojení k Office 365.  Najdete v článku [Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) článku.
* ** Další informace o ExpressRoute [NAT](../expressroute/expressroute-nat.md) požadavky.

Další informace o [ExpressRoute](../expressroute/expressroute-introduction.md), přidružené [ceny](https://azure.microsoft.com/pricing/details/expressroute), a [poskytovatelé připojení](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Další aspekty
* Některé z výše uvedených možností mají různé maximální limit, který může podporovat pro připojení virtuální sítě, připojení brány a dalších kritérií. Doporučujeme, abyste si prošli Azure [sítě omezení](../azure-subscription-service-limits.md#networking-limits) pochopit, pokud některý z nich mít vliv na typy připojení chcete použít.
* Pokud máte v plánu připojit bránu z připojení VPN typu site-to-site ke stejné síti VNet jako bránu ExpressRoute, byste si měli přečíst s důležitá omezení nejdřív. Najdete v článku [konfigurace ExpressRoute a Site-to-Site připojení, která](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) další podrobnosti najdete v článku.

## <a name="next-steps"></a>Další postup
Níže uvedených zdrojů vysvětlují, jak implementovat typy připojení popsaná v tomto článku.

* [Implementace připojení point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementace připojení site-to-site](guidance-hybrid-network-vpn.md)
* [Implementace vyhrazené soukromé připojení](guidance-hybrid-network-expressroute.md)
* [Implementace vyhrazené soukromé připojení pomocí připojení site-to-site pro zajištění vysoké dostupnosti](guidance-hybrid-network-expressroute-vpn-failover.md)
