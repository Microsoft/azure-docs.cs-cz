---
title: Internet peering vs. peering Service
titleSuffix: Azure
description: Internet peering vs. peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75775702"
---
# <a name="internet-peering-vs-peering-service"></a>Internet peering vs. peering Service

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
* Možnost výběru upřednostňované aktualizace SP pro připojení k Microsoft Cloud.
* Přehledy provozu, jako je generování sestav latence, a monitorování předpon.
* Optimální směrování sítě (jako směrování) od Microsoft Cloud.
* Směrování a statistika – události týkající se anomálií tras Border Gateway Protocol ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) (detekce vrácených a napadení) a neoptimální směrování.

## <a name="peering-service-partnership-requirements"></a>Požadavky na partnerství služby peering Service
* Připojení k Microsoft Cloud v místě nejbližším zákazníkovi. Poskytovatel partnerské služby bude směrovat uživatelský provoz na Microsoft Edge co nejblíže k uživateli. Podobně platí, že při přenosu směrem k uživateli Microsoft bude směrovat provoz (pomocí značky BGP) do hraničního umístění, které je nejblíže uživateli a aktualizace SP bude uživatelům poskytovat data.
* Partner bude udržovat vysokou dostupnou, vysokou propustnost a geograficky redundantní připojení pomocí globální sítě Microsoft.
* Partner může využít stávající partnerský vztah pro podporu služby partnerského vztahu, pokud splňuje požadavky.

## <a name="faq"></a>Nejčastější dotazy
Nejčastější dotazy najdete v tématu [Služba peering Service – Nejčastější dotazy](service-faqs.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o výhodách zákazníků pomocí [služby peering Service](https://docs.microsoft.com/azure/peering-service/).
* Přečtěte si o krocích pro povolení přímého partnerského vztahu pro službu partnerského vztahu v rámci [partnera služby peering Service](walkthrough-peering-service-all.md).
