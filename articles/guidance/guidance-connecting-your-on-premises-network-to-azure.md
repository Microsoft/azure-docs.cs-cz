---
title: Připojení místní sítě k Azure | Dokumentace Microsoftu
description: Popisuje a porovnává různé dostupné metody pro připojení ke cloudovým službám Microsoftu, jako je například Azure, Office 365 a Dynamics CRM Online.
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
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "23851012"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Připojení místní sítě k Azure
Společnost Microsoft poskytuje několik typů cloudových služeb. Zatímco přes veřejný Internet, můžete se připojit ke všem službám, můžete také připojit k některým službám přes tunelové připojení virtuální privátní sítě (VPN) přes Internet nebo přes vyhrazené soukromé připojení k Microsoft. Tento článek pomůže určit, jaké možnosti připojení bude nejlépe vyhovovat vašim potřebám na základě typů cloudovým službám Microsoftu, které skutečně využijete. Většina organizací využívat několik typů připojení je popsáno níže.

## <a name="connecting-over-the-public-internet"></a>Připojení přes veřejný Internet
Tento typ připojení poskytuje přístup ke cloudovým službám Microsoftu přímo přes Internet, jak je znázorněno níže.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Toto připojení je obvykle první typ použitý pro připojení ke cloudovým službám Microsoftu. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Vyžaduje žádné změny vaší místní síti, tak dlouho, dokud všechna zařízení klienta mají neomezený přístup ke všem IP adresy a porty v síti Internet. |Když provoz je často šifrován pomocí protokolu HTTPS, mohou být zachyceny při přenosu, protože se prochází přes veřejný Internet. |
| Můžete se připojit ke všem cloudovým službám Microsoftu přístupný z veřejného Internetu. |Nepředvídatelné latence vzhledem k tomu, že připojení prochází Internetem. |
| Používá vaše stávající připojení k Internetu. | |
| Nevyžaduje žádné připojení k zařízení. | |

Toto připojení nemá žádný připojení nebo nákladů na šířku pásma, protože používat vaše stávající připojení k Internetu.

## <a name="connecting-with-a-point-to-site-connection"></a>Připojení pomocí připojení point-to-site
Tento typ připojení poskytuje přístup k některé cloudovým službám Microsoftu přes tunelové připojení Secure Socket Tunneling Protocol (SSTP) přes Internet, jak je znázorněno níže.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "Point-to-site připojení")

Připojení se provádí přes vaše stávající připojení k Internetu, ale vyžaduje použití služby Azure VPN Gateway. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Vyžaduje žádné změny vaší místní síti, tak dlouho, dokud všechna zařízení klienta mají neomezený přístup ke všem IP adresy a porty v síti Internet. |Když provoz se šifruje pomocí protokolu IPSec, mohou být zachyceny při přenosu, protože se prochází přes veřejný Internet. |
| Používá vaše stávající připojení k Internetu. |Nepředvídatelné latence vzhledem k tomu, že připojení prochází Internetem. |
| Propustnost až 200 Mb/s na bránu. |Vyžaduje vytváření a Správa samostatného připojení mezi jednotlivými zařízeními ve vaší místní síti nebo každou bránu, kterou každé zařízení potřebuje připojit k. |
| Slouží k připojení ke službám Azure, které se dá připojit Azure Virtual Network (VNet) jako jsou Azure Virtual Machines a Azure Cloud Services. |Vyžaduje minimální průběžné správy služby Azure VPN Gateway. |
| Nelze použít pro připojení k Microsoft Office 365 nebo Dynamics CRM Online. | |
| Nelze použít pro připojení ke službám Azure, které nemůže být připojen k virtuální síti. | |

Další informace o [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) služby, jeho [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)a přenos odchozích dat [ceny](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Připojení pomocí připojení site-to-site
Tento typ připojení poskytuje přístup k některé cloudových služeb Microsoftu prostřednictvím tunelu IPSec přes Internet, jak je znázorněno níže.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "připojení Site-to-site")

Připojení se provádí přes vaše stávající připojení k Internetu, ale vyžaduje použití služby Azure VPN Gateway s jeho přidružené ceny a ceny za odchozí datové přenosy. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Všechna zařízení ve vaší místní síti může komunikovat se službami Azure, které jsou připojené k virtuální síti, takže není potřeba konfigurovat jednotlivá připojení pro každé zařízení. |Když provoz se šifruje pomocí protokolu IPSec, mohou být zachyceny při přenosu, protože se prochází přes veřejný Internet. |
| Používá vaše stávající připojení k Internetu. |Nepředvídatelné latence vzhledem k tomu, že připojení prochází Internetem. |
| Slouží k připojení ke službám Azure, které můžou být připojené k virtuální síti jako jsou virtuální počítače a cloudové služby. |Musíte nakonfigurovat a spravovat ověřené VPN zařízení * místní. |
| Propustnost až 200 Mb/s na bránu. |Vyžaduje minimální průběžné správy služby Azure VPN Gateway. |
| Můžete vynutit odchozího provozu iniciované z cloudu virtuálních počítačů prostřednictvím místní sítě pro účely zkoumání a protokolování pomocí tras definovaných uživatelem nebo protokolu BGP (Border Gateway) **. |Nelze použít pro připojení k Microsoft Office 365 nebo Dynamics CRM Online. |
| Nelze použít pro připojení ke službám Azure, které nemůže být připojen k virtuální síti. | |
| Pokud používáte služby, které zahájí připojení zpět do místních zařízení a zásady zabezpečení vyžaduje, musíte bránu firewall mezi místní sítí a Azure. | |

* * Úplný seznam [ověřených zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Další informace o používání [trasy definované uživatelem](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) nebo [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) přinutit směrování z virtuální sítě Azure do místního zařízení.

## <a name="connecting-with-a-dedicated-private-connection"></a>Připojení pomocí vyhrazeného soukromého připojení
Tento typ připojení poskytuje přístup ke všem cloudovým službám Microsoftu přes vyhrazené soukromé připojení k Microsoftu, která neprocházejí přes Internet, jak je znázorněno níže.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "připojení ExpressRoute")

Připojení vyžaduje použití služby ExpressRoute a připojení k poskytovateli připojení. Následující tabulka uvádí výhody a nevýhody tohoto typu připojení.

| **Výhody** | **Důležité informace** |
| --- | --- |
| Provoz nelze zachytit při přenosu přes veřejný Internet, vzhledem k tomu použít vyhrazené připojení prostřednictvím poskytovatele služeb. |Vyžaduje místní směrovač správy. |
| Šířka pásma až 10 Gb/s na okruh ExpressRoute a propustnost až 2 Gb/s pro každou bránu. |Vyžaduje vyhrazené připojení ke zprostředkovateli připojení. |
| Předvídatelná latence protože se jedná o vyhrazené připojení k Microsoftu, která neprocházejí přes Internet. |Minimální průběžné správy jeden nebo více bran VPN Azure může vyžadovat, (při připojování k virtuálním sítím okruhu). |
| Nevyžaduje, aby šifrovanou komunikaci, i když můžete šifrovat provoz, v případě potřeby. |Pokud používáte cloudové služby, které zahájí připojení zpět na místní zařízení, musíte bránu firewall mezi místní sítí a Azure. |
| Můžou přímo připojit ke všem cloudovým službám Microsoftu, s několika výjimkami *. |Překlad síťových adres (NAT) vyžaduje zadání datových centrech Microsoftu pro služby, které nemůže být připojen k VNet.* * místních IP adres |
| Můžete vynutit odchozího provozu iniciované z cloudu virtuálních počítačů prostřednictvím místní sítě pro účely zkoumání a protokolování pomocí protokolu BGP. | |

* * Zobrazit [seznam služeb](../expressroute/expressroute-faqs.md#supported-services) , který nelze použít s ExpressRoute. Vaše předplatné Azure musí být schválen pro připojení k Office 365.  Zobrazit [Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) , kde najdete podrobnosti.
* ** Další informace o ExpressRoute [NAT](../expressroute/expressroute-nat.md) požadavky.

Další informace o [ExpressRoute](../expressroute/expressroute-introduction.md), jeho přidruženého [ceny](https://azure.microsoft.com/pricing/details/expressroute), a [poskytovatelé připojení](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Další aspekty
* Některé z výše uvedených mají různé maximálními limity, které může podporovat pro připojení virtuální sítě, brána připojení a dalších kritérií. Doporučujeme, abyste si Azure [síťová omezení](../azure-subscription-service-limits.md#networking-limits) pochopit, pokud některý z nich mít vliv na typy připojení, které chcete použít.
* Pokud se chcete připojit bránu připojení VPN typu site-to-site ke stejné virtuální síti jako brána ExpressRoute, můžete byste se seznámit s důležitá omezení nejprve. Zobrazit [používaných připojení typu Site-to-Site a ExpressRoute nakonfigurovat](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) , kde najdete další podrobnosti.

## <a name="next-steps"></a>Další postup
Zdroje informací vysvětlují, jak implementovat typy připojení, které jsou popsané v tomto článku.

* [Implementace připojení point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementace připojení site-to-site](guidance-hybrid-network-vpn.md)
* [Implementujte vyhrazené soukromé připojení](guidance-hybrid-network-expressroute.md)
* [Implementujte vyhrazené soukromé připojení pomocí připojení site-to-site pro zajištění vysoké dostupnosti](guidance-hybrid-network-expressroute-vpn-failover.md)
