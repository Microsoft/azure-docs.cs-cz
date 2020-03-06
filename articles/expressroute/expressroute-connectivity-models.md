---
title: 'Azure ExpressRoute: modely připojení'
description: Tento článek popisuje různé režimy připojení mezi sítí zákazníka a Microsoft Azure a službami Office 365. Zákazníci mohou využívat poskytovatele MPLS, cloudové výměny a poskytovatele ethernetových připojení.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: 375d2f9d3b455c0495c69f2b23d62b1ab6522710
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382355"
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