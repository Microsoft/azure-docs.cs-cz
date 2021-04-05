---
title: Internetový peering vs. Peering Service
titleSuffix: Azure
description: Internetový peering vs. Peering Service
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 717a8c87c6a1c22d3f75cd4e3054bfdf0c4b5f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023441"
---
# <a name="internet-peering-vs-peering-service"></a>Internetový peering vs. Peering Service

Partnerský vztah mezi Internetem odkazuje na jakékoli propojení mezi globální sítí (AS8075) a dopravci nebo poskytovateli služeb společnosti Microsoft. Poskytovatel služeb se může stát partnerem služby peering Service tím, že implementuje požadavky na partnerství partnerských služeb popsané níže, aby poskytoval spolehlivé a vysoce výkonné veřejné připojení s optimálním směrováním od zákazníka k síti Microsoftu.

## <a name="about-peering-service"></a>Informace o službě Peering Service
Peering Service je program pro partnerství s poskytovateli služby Key Service, který poskytuje svým podnikovým uživatelům nejlepší veřejné připojení k Internetu. Partneři, kteří jsou součástí programu, budou mít přímá, vysoce dostupná, geograficky redundantní připojení a optimalizované směrování do Microsoftu. Služba peering Service je doplňují portfolio pro připojení Microsoftu:
*   ExpressRoute pro privátní připojení k prostředkům IaaS nebo PaaS (podpora pro privátní adresní prostor IP adres)
    *   Připojení na základě partnerů
    *   Přímé připojení 100G k Microsoftu
*   Protokol IPSEC přes Internet pro připojení VPN ke cloudu
*   Připojení SD-WAN k Azure prostřednictvím virtuální sítě WAN

Cílový segment služby partnerského vztahu je SaaS konektivita, zákazníci SD-WAN, kteří ochotni udělat internetovou užitečných na pobočce a všechny zákazníky, kteří mají duální strategii MPLS a internetovou na podnikové úrovni.

Primárním cílem při připojování k Microsoft Cloud by měla být minimalizace latence tím, že se zkrátí doba odezvy (RTT) z uživatelského webu na globální síť Microsoftu, což je páteřní síť Microsoftu, která spojuje všechna datacentra Microsoftu a více vstupních bodů cloudových aplikací. Podívejte [se na téma získání nejlepšího připojení a výkonu v sadě Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Obrázek distribuovaného přístupu](./media/distributed-access.png)

Na obrázku, který se nachází na každé pobočce globálního podniku, se připojuje k nejbližšímu možnému umístění Microsoft Edge přes síť partnera.

**Výhody pro zákazníky služby peering Service:**
* Nejlepší veřejné směrování přes Internet na Microsoft Cloud služby za účelem optimálního výkonu a spolehlivosti.
* Možnost výběru preferovaného poskytovatele služeb pro připojení k Microsoft Cloud.
* Přehledy provozu, jako je generování sestav latence, a monitorování předpon.
* Optimální směrování sítě (jako směrování) od Microsoft Cloud.
* Směrování a statistika – události týkající se anomálií tras Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (detekce vrácených a napadení) a neoptimální směrování.

## <a name="peering-service-partnership-requirements"></a>Požadavky na partnerství služby peering Service
* Připojení k Microsoft Cloud v místě nejbližším zákazníkovi. Poskytovatel partnerské služby bude směrovat uživatelský provoz na Microsoft Edge co nejblíže k uživateli. Podobně platí, že při přenosu směrem k uživateli bude Microsoft směrovat přenosy (pomocí značky BGP) do hraničního umístění, které je nejblíže poskytovateli uživatelů a služeb, aby tento provoz poskytoval uživateli.
* Partner bude udržovat vysokou dostupnou, vysokou propustnost a geograficky redundantní připojení pomocí globální sítě Microsoft.
* Partner může využít stávající partnerský vztah pro podporu služby partnerského vztahu, pokud splňuje požadavky.

## <a name="faq"></a>Časté otázky
Nejčastější dotazy najdete v tématu [Služba peering Service – Nejčastější dotazy](service-faqs.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o výhodách zákazníků pomocí [služby peering Service](../peering-service/index.yml).
* Přečtěte si o krocích pro povolení přímého partnerského vztahu pro službu partnerského vztahu v rámci [partnera služby peering Service](walkthrough-peering-service-all.md).