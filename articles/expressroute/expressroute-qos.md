---
title: 'Azure ExpressRoute: požadavky QoS'
description: Tato stránka poskytuje podrobné požadavky pro konfiguraci a správu technologie QoS. Projednávají se Skype pro firmy/hlasové služby.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: duau
ms.openlocfilehash: 871af5b767e8cfe10db3a0a1c15c47340e25581f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395022"
---
# <a name="expressroute-qos-requirements"></a>Požadavky na technologii QoS služby ExpressRoute
Skype pro firmy má úlohy, které vyžadují odlišné zacházení podle QoS. Pokud budete chtít využívat hlasové služby prostřednictvím ExpressRoute, měli byste dodržovat požadavky popsané dál.

![Diagram, který zobrazuje hlasové služby, které procházejí přes ExpressRoute.](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Požadavky na technologii QoS se týkají jenom partnerského vztahu Microsoftu. Hodnoty DSCP v provozu vaší sítě přijaté v rámci veřejného partnerského vztahu Azure a soukromého partnerského vztahu Azure budou vynulovány. 
> 
> 

Následující tabulka uvádí seznam označení DSCP používaných Microsoft teams a Skype pro firmy. Další informace najdete v tématu [Správa technologie QoS pro Skype pro firmy](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS).

| **Třída provozu** | **Zpracování (označení DSCP)** | **Úlohy Microsoft teams a Skype pro firmy** |
| --- | --- | --- |
| **Hlas** |EF (46) |Skype/Microsoft Teams/Lync – hlas |
| **Interaktivit** |AF41 (34) |Video, VBSS |
| |AF21 (18) |Sdílení aplikací | 
| **Výchozí** |AF11 (10) |Přenos souborů |
| |CS0 (0) |Cokoliv jiného |

* Měli byste klasifikovat úlohy a označit správné hodnoty DSCP. Postupujte podle pokynů uvedených [zde](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10), kde se popisuje, jak ve vaší síti nastavit označení DSCP.
* Měli byste ve vaší síti nakonfigurovat a podporovat více front QoS. Hlasový vstup musí být samostatnou třídou a může přijmout zpracování EF specifikované v [dokumentu RFC 3246](https://www.ietf.org/rfc/rfc3246.txt). 
* Můžete rozhodnout o mechanismu řízení front, zásadách detekce zahlcení a přidělení šířky pásma pro jednotlivé třídy provozu. Označení DSCP pro Skype pro firmy ale musí být zachováno. Pokud používáte označení DSCP, které není uvedené výš, například AF31 (26), musíte před odesláním paketu Microsoftu tuto hodnotu DSCP přepsat na 0. Microsoft odesílá jenom pakety označené hodnotami DSCP uvedenými v tabulce výš. 

## <a name="next-steps"></a>Další kroky
* Přečtěte si požadavky pro [směrování](expressroute-routing.md) a [překlad adres (NAT)](expressroute-nat.md).
* Následující odkazy popisují konfiguraci připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Konfigurace směrování](expressroute-howto-routing-classic.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-classic.md)

