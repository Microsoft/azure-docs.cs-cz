---
title: 'Azure ExpressRoute: požadavky'
description: Tato stránka obsahuje seznam požadavků, které musí být splněné, než můžete objednat okruh Azure ExpressRoute. Obsahuje kontrolní seznam.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 87c8a7523222afd3ae4daeb8fb5824025e693cd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569624"
---
# <a name="expressroute-prerequisites--checklist"></a>Požadavky ExpressRoute a kontrolní seznam
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, musíte ověřit, že jsou splněné požadavky uvedené v následujících oddílech.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Účet Azure
* Platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení okruhu ExpressRoute. Okruhy ExpressRoute jsou prostředky v rámci předplatných Azure. Předplatné Azure je požadavkem i v případě, že je připojení omezené na cloudové služby Microsoftu mimo Azure, jako je například Microsoft 365.
* Aktivní předplatné Microsoft 365 (Pokud používáte služby Microsoft 365). Další informace najdete v části Microsoft 365 specifické požadavky tohoto článku.

## <a name="connectivity-provider"></a>Poskytovatel připojení

* Abyste se připojili ke cloudu Microsoftu, můžete spolupracovat s [partnerem připojení ExpressRoute](expressroute-locations.md#partners). Připojení mezi místní sítí a Microsoftem můžete vytvořit [třemi způsoby](expressroute-introduction.md)
* Pokud váš poskytovatel není partnerem připojení ExpressRoute, můžete se pořád připojit ke cloudu Microsoftu prostřednictvím [poskytovatele cloudové výměny](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Požadavky sítě
* **Redundance v každém umístění partnerského vztahu**: Microsoft vyžaduje, aby byly v každém okruhu ExpressRoute nastaveny redundantní relace protokolu BGP mezi směrovači Microsoftu a směrovači partnerského vztahu na každém okruhu (i když máte jenom [jedno fyzické připojení ke cloudu Exchange](expressroute-faqs.md#onep2plink)).
* **Redundance při zotavení po havárii**: Microsoft důrazně doporučuje nastavit alespoň dva okruhy ExpressRoute v různých umístěních partnerských vztahů, aby nedocházelo k jedinému bodu selhání.
* **Směrování**: v závislosti na tom, jak se připojíte k Microsoft Cloud, budete vy nebo váš poskytovatel potřebovat nastavit a spravovat relace protokolu BGP pro [domény směrování](expressroute-circuit-peerings.md). Někteří poskytovatelé připojení k síti Ethernet nebo poskytovatelé cloudového serveru můžou nabízet správu protokolu BGP jako službu pro přidání hodnoty.
* **NAT:** Microsoft prostřednictvím partnerského vztahu Microsoftu přijímá jenom veřejné IP adresy. Pokud používáte privátní IP adresy v místní síti, vy nebo váš poskytovatel potřebuje překlad privátních IP adres na veřejné IP adresy [pomocí překladu adres (NAT)](expressroute-nat.md).
* **QoS:** Skype pro firmy má různé služby (třeba hlasové, textové nebo videoslužby), které vyžadují diferencovaný přístup. Vy a váš poskytovatel byste měli postupovat podle [požadavků QoS](expressroute-qos.md).
* **Zabezpečení sítě:** Při připojení ke cloudu Microsoftu pomocí ExpressRoute byste měli zvážit [zabezpečení sítě](../best-practices-network-security.md).

## <a name="microsoft-365"></a>Microsoft 365
Pokud máte v plánu povolit Microsoft 365 v ExpressRoute, přečtěte si následující dokumenty, kde najdete další informace o Microsoft 365 požadavcích.

* [Azure ExpressRoute pro Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Směrování s ExpressRoute pro Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Vysoká dostupnost a převzetí služeb při selhání u ExpressRoute](https://aka.ms/erhighavailability)
* [Microsoft 365 adresy URL a rozsahy IP adres](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Plánování sítě a optimalizace výkonu pro Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Plánování sítě a migrace pro Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Integrace Microsoft 365 s místními prostředími](/microsoft-365/enterprise/microsoft-365-integration)
* [Školicí videa ExpressRoute v Office 365 pro pokročilé](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Další kroky
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
* Vyhledejte poskytovatele připojení ExpressRoute. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
