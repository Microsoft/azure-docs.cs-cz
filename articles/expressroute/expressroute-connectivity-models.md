---
title: 'Modely připojení ExpressRoute: Připojení k Microsoft Azure prostřednictvím poskytovatelů síťových služeb, výměn a poskytovatelů ethernetových připojení | Dokumentace Microsoftu'
description: Tento článek popisuje různé režimy připojení mezi sítí zákazníka a službami Microsoft Azure, Office 365 a Dynamics 365. Zákazníci mohou využívat poskytovatele MPLS, cloudové výměny a poskytovatele ethernetových připojení.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: cherylmc
ms.openlocfilehash: 00f97da2189491103c461b49ac82feb92d8f4b9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22709891"
---
# <a name="expressroute-connectivity-models"></a>Modely připojení ExpressRoute
Připojení mezi místní sítí a cloudem Microsoftu můžete vytvořit třemi různými způsoby: [společné umístění CloudExchange](#CloudExchange), [ethernetové připojení typu point-to-point](#Ethernet) a [připojení typu any-to-any (IPVPN)](#IPVPN). Poskytovatelé připojení můžou nabízet jeden nebo víc modelů připojení. Můžete ve spolupráci s poskytovatelem připojení vybrat model, který vám bude nejlépe vyhovovat.
<br><br>

![Diagram modelů připojení ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Společně umístěné v cloudové výměně
Pokud jste ve společném umístění s výměnou cloudu, můžete si objednat virtuální křížové připojení ke cloudu Microsoftu prostřednictvím ethernetové výměny poskytovatele ve společném umístění. Poskytovatelé ve společném umístění můžou nabízet křížová připojení vrstvy 2 nebo spravovaná křížová připojení vrstvy 3 mezi vaší infrastrukturou ve společném umístění a cloudem Microsoftu.

## <a name="Ethernet"></a>Ethernetová připojení typu point-to-point
Místní datová centra nebo pobočky můžete připojit ke cloudu Microsoftu prostřednictvím ethernetových propojení typu point-to-point. Poskytovatelé ethernetových pripojení typu point-to-point můžou nabízet připojení vrstvy 2 nebo spravovaná připojení vrstvy 3 mezi vaší lokalitou a cloudem Microsoftu.

## <a name="IPVPN"></a>Sítě typu any-to-any (IPVPN)
S cloudem Microsoftu můžete integrovat vaši síť WAN. Poskytovatelé IPVPN (obvykle MPLS VPN) nabízejí připojení typu any-to-any mezi pobočkami a datovými centry. Cloud Microsoftu může být připojen do vaší sítě WAN, aby vypadal stejně jako jiné pobočky. Poskytovatelé sítě WAN obvykle nabízejí spravované připojení vrstvy 3. Funkce a možnosti ExpressRoute jsou u všech výše uvedených modelů připojení identické. 

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o připojeních ExpressRoute a doménách směrování. Viz [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Seznamte se s funkcemi ExpressRoute. [Technický přehled ExpressRoute](expressroute-introduction.md)
* Vyhledejte poskytovatele služeb. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Zkontrolujte, že jsou splněné všechny požadavky. Viz [Požadavky služby ExpressRoute](expressroute-prerequisites.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurace směrování](expressroute-howto-routing-portal-resource-manager.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)