---
title: 'Požadavky technologie QoS – ExpressRoute: Azure | Microsoft Docs'
description: Tato stránka obsahuje podrobné požadavky pro konfiguraci a správu technologie QoS. Skype pro firmy/hlasové služby jsou popsány.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: eed6113442b4080341ff08b3983880f3afe66c00
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385120"
---
# <a name="expressroute-qos-requirements"></a>Požadavky na technologii QoS služby ExpressRoute
Skype pro firmy má úlohy, které vyžadují odlišné zacházení podle QoS. Pokud budete chtít využívat hlasové služby prostřednictvím ExpressRoute, měli byste dodržovat požadavky popsané dál.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Požadavky na technologii QoS se týkají jenom partnerského vztahu Microsoftu. Hodnoty DSCP v provozu vaší sítě přijaté v rámci veřejného partnerského vztahu Azure a soukromého partnerského vztahu Azure budou vynulovány. 
> 
> 

Následující tabulka uvádí seznam označení DSCP používaných Microsoft teams a Skype pro firmy. Další informace najdete v tématu [Správa technologie QoS pro Skype pro firmy](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS).

| **Třída provozu** | **Zpracování (označení DSCP)** | **Úlohy Microsoft teams a Skype pro firmy** |
| --- | --- | --- |
| **Hlas** |EF (46) |Skype/Microsoft Teams/Lync – hlas |
| **Interaktivní** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Sdílení aplikací | 
| **Výchozí** |AF11 (10) |Přenos souborů |
| |CS0 (0) |Cokoliv jiného |

* Měli byste klasifikovat úlohy a označit správné hodnoty DSCP. Postupujte podle pokynů uvedených [zde](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10), kde se popisuje, jak ve vaší síti nastavit označení DSCP.
* Měli byste ve vaší síti nakonfigurovat a podporovat více front QoS. Hlasový vstup musí být samostatnou třídou a může přijmout zpracování EF specifikované v [dokumentu RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Můžete rozhodnout o mechanismu řízení front, zásadách detekce zahlcení a přidělení šířky pásma pro jednotlivé třídy provozu. Označení DSCP pro Skype pro firmy ale musí být zachováno. Pokud používáte označení DSCP, které není uvedené výš, například AF31 (26), musíte před odesláním paketu Microsoftu tuto hodnotu DSCP přepsat na 0. Microsoft odesílá jenom pakety označené hodnotami DSCP uvedenými v tabulce výš. 

## <a name="next-steps"></a>Další postup
* Přečtěte si požadavky pro [směrování](expressroute-routing.md) a [překlad adres (NAT)](expressroute-nat.md).
* Následující odkazy popisují konfiguraci připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Konfigurace směrování](expressroute-howto-routing-classic.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-classic.md)

