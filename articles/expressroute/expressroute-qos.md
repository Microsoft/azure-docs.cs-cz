---
title: Požadavky technologie QoS pro ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje podrobné požadavky pro konfiguraci a správu technologie QoS pro okruhy ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: db1c1447-0283-4a09-907b-ae481adc40c7
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: cherylmc
ms.openlocfilehash: c097a9ccba91f59b323215d42d37e6d85e0981ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22709908"
---
# <a name="expressroute-qos-requirements"></a>Požadavky na technologii QoS služby ExpressRoute
Skype pro firmy má úlohy, které vyžadují odlišné zacházení podle QoS. Pokud budete chtít využívat hlasové služby prostřednictvím ExpressRoute, měli byste dodržovat požadavky popsané dál.

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> Požadavky na technologii QoS se týkají jenom partnerského vztahu Microsoftu. Hodnoty DSCP v provozu vaší sítě přijaté v rámci veřejného partnerského vztahu Azure a soukromého partnerského vztahu Azure budou vynulovány. 
> 
> 

V následující tabulce je seznam označení DSCP používaných Skypem pro firmy. Další informace najdete v tématu [Správa technologie QoS pro Skype pro firmy](https://technet.microsoft.com/library/gg405409.aspx).

| **Třída provozu** | **Zpracování (označení DSCP)** | **Úlohy Skypu pro firmy** |
| --- | --- | --- |
| **Hlas** |EF (46) |Skype/Lync – hlas |
| **Interaktivní** |AF41 (34) |Video, VBSS |
| AF21 (18) |Sdílení aplikací | |
| **Výchozí** |AF11 (10) |Přenos souborů |
| CS0 (0) |Cokoliv jiného | |

* Měli byste klasifikovat úlohy a označit správné hodnoty DSCP. Postupujte podle pokynů uvedených [zde](https://technet.microsoft.com/library/gg405409.aspx), kde se popisuje, jak ve vaší síti nastavit označení DSCP.
* Měli byste ve vaší síti nakonfigurovat a podporovat více front QoS. Hlas musí být samostatná třída a přijímat zpracování EF uvedené v dokumentu RFC 3246. 
* Můžete rozhodnout o mechanismu řízení front, zásadách detekce zahlcení a přidělení šířky pásma pro jednotlivé třídy provozu. Označení DSCP pro Skype pro firmy ale musí být zachováno. Pokud používáte označení DSCP, které není uvedené výš, například AF31 (26), musíte před odesláním paketu Microsoftu tuto hodnotu DSCP přepsat na 0. Microsoft odesílá jenom pakety označené hodnotami DSCP uvedenými v tabulce výš. 

## <a name="next-steps"></a>Další kroky
* Přečtěte si požadavky pro [směrování](expressroute-routing.md) a [překlad adres (NAT)](expressroute-nat.md).
* Následující odkazy popisují konfiguraci připojení ExpressRoute.
  
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Konfigurace směrování](expressroute-howto-routing-classic.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-classic.md)

