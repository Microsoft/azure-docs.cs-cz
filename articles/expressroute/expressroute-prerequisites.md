---
title: 'Azure ExpressRoute: požadavky'
description: Tato stránka obsahuje seznam požadavků, které musí být splněné, než můžete objednat okruh Azure ExpressRoute. Obsahuje kontrolní seznam.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74083356"
---
# <a name="expressroute-prerequisites--checklist"></a>Požadavky ExpressRoute a kontrolní seznam
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, musíte ověřit, že jsou splněné požadavky uvedené v následujících oddílech.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Účet Azure
* Platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení okruhu ExpressRoute. Okruhy ExpressRoute jsou prostředky v rámci předplatných Azure. Předplatné Azure je požadavkem i v případě, že je připojení omezené na cloudové služby Microsoftu mimo Azure, jako je například Office 365.
* Aktivní předplatné Office 365 (pokud používáte služby Office 365). Další informace najdete v tomto článku v části Specifické požadavky Office 365.

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

## <a name="office-365"></a>Office 365
Pokud chcete povolit Office 365 v ExpressRoute, přečtěte si následující dokumenty, kde najdete další informace o požadavcích Office 365.

* [Přehled ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Směrování s ExpressRoute pro Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Vysoká dostupnosti a převzetí služeb při selhání s využitím ExpressRoute](https://aka.ms/erhighavailability)
* [Adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Plánování sítě a optimalizace výkonu pro Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Nástroje a kalkulačky šířky pásma sítě](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integrace Office 365 s místními prostředími](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Školicí videa ExpressRoute v Office 365 pro pokročilé](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Další kroky
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
* Vyhledejte poskytovatele připojení ExpressRoute. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
