---
title: Požadavky pro přijetí Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje seznam požadavků, které musí být splněné, než můžete objednat okruh Azure ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: cherylmc
ms.openlocfilehash: b4358d1ccffd31d1f347f1872237620bee081251
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989355"
---
# <a name="expressroute-prerequisites--checklist"></a>Požadavky ExpressRoute a kontrolní seznam
Pokud se chcete připojit ke cloudovým službám Microsoftu pomocí služby ExpressRoute, musíte ověřit, že jsou splněné požadavky uvedené v následujících oddílech.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Účet Azure
* Platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení okruhu ExpressRoute. Okruhy ExpressRoute jsou prostředky v rámci předplatných Azure. Předplatné Azure je požadavkem i v případě, že připojení je omezené na cloudové služby Microsoftu mimo Azure, jako jsou například služby Office 365 a Dynamics 365.
* Aktivní předplatné Office 365 (pokud používáte služby Office 365). Další informace najdete v tomto článku v části [Specifické požadavky Office 365](#office-365-specific-requirements).

## <a name="connectivity-provider"></a>Poskytovatel připojení

* Abyste se připojili ke cloudu Microsoftu, můžete spolupracovat s [partnerem připojení ExpressRoute](expressroute-locations.md#partners). Připojení mezi místní sítí a Microsoftem můžete vytvořit [třemi způsoby](expressroute-introduction.md)
* Pokud váš poskytovatel není partnerem připojení ExpressRoute, můžete se pořád připojit ke cloudu Microsoftu prostřednictvím [poskytovatele cloudové výměny](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Síťové požadavky
* **Redundantní připojení:** Neexistuje požadavek na redundanci fyzického připojení mezi vámi a poskytovatelem. Microsoft nevyžaduje navázání redundantních relací protokolu BGP mezi směrovači Microsoftu a směrovači partnerského vztahu, ani když máte jen [jedno fyzické připojení ke cloudové výměně](expressroute-faqs.md#onep2plink).
* **Směrování:** V závislosti na způsobu připojení ke cloudu Microsoftu potřebujete vy nebo váš poskytovatel nastavit a spravovat relace BGP pro [domény směrování](expressroute-circuit-peerings.md). Někteří poskytovatelé ethernetového připojení nebo poskytovatelé cloudové výměny můžou nabízet správu protokolu BGP jako službu s přidanou hodnotou.
* **NAT:** Microsoft prostřednictvím partnerského vztahu Microsoftu přijímá jenom veřejné IP adresy. Pokud v místní síti používáte soukromé IP adresy, je nutné, abyste vy nebo váš poskytovatel překládali soukromé IP adresy na veřejné IP adresy [pomocí překladu síťových adres (NAT)](expressroute-nat.md).
* **QoS:** Skype pro firmy má různé služby (třeba hlasové, textové nebo videoslužby), které vyžadují diferencovaný přístup. Vy a váš poskytovatel byste měli postupovat podle [požadavků QoS](expressroute-qos.md).
* **Zabezpečení sítě:** Při připojení ke cloudu Microsoftu pomocí ExpressRoute byste měli zvážit [zabezpečení sítě](../best-practices-network-security.md).

## <a name="office-365"></a>Office 365
Pokud chcete povolit Office 365 v ExpressRoute, přečtěte si následující dokumenty, kde najdete další informace o požadavcích Office 365.

* [Přehled ExpressRoute pro Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Směrování s ExpressRoute pro Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Vysoká dostupnosti a převzetí služeb při selhání s využitím ExpressRoute](https://aka.ms/erhighavailability)
* [Adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Plánování sítě a optimalizace výkonu pro Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Nástroje a kalkulačky šířky pásma sítě](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integrace Office 365 s místními prostředími](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Školicí videa ExpressRoute v Office 365 pro pokročilé](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Pokud chcete povolit Dynamics 365 v ExpressRoute, přečtěte si následující dokumenty, kde najdete další informace o Dynamics 365.

* [Dokument white paper pro Dynamics 365 a ExpressRoute](http://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [Adresy URL](https://support.microsoft.com/kb/2655102) a [rozsahy IP adres](https://support.microsoft.com/kb/2728473) pro Dynamics 365

## <a name="next-steps"></a>Další kroky
* Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
* Vyhledejte poskytovatele připojení ExpressRoute. Viz [Partneři ExpressRoute a umístění partnerského vztahu](expressroute-locations.md).
* Přečtěte si požadavky pro [směrování](expressroute-routing.md), [překlad adres (NAT)](expressroute-nat.md) a [technologii QoS](expressroute-qos.md).
* Nakonfigurujte připojení ExpressRoute.
  * [Vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Konfigurace směrování](expressroute-howto-routing-arm.md)
  * [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
