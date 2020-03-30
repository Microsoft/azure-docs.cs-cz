---
title: Partnerský vztah v internetu vs. služba partnerského vztahu
titleSuffix: Azure
description: Partnerský vztah v internetu vs. služba partnerského vztahu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6c1205fcacd3d7228f1aecf1e603b66d9e1fcee5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75775702"
---
# <a name="internet-peering-vs-peering-service"></a>Partnerský vztah v internetu vs. služba partnerského vztahu

Partnerský vztah v internetu označuje jakékoli propojení mezi globální sítí společnosti Microsoft (AS8075) a sítí dopravců nebo poskytovatelů služeb. Poskytovatel služeb se může stát partnerem služby Partner partnera partnera partnerského programu partnerského vztahu partnerských společností, které jsou vysvětleny níže, aby poskytoval spolehlivé a vysoce výkonné veřejné připojení s optimálním směrováním od zákazníka k síti společnosti Microsoft.

## <a name="about-peering-service"></a>Informace o službě Peering Service
Peering Service je partnerský program s klíčovými poskytovateli služeb, který poskytuje nejlepší veřejné připojení k Internetu ve své třídě svým podnikovým uživatelům. Partneři, kteří jsou součástí programu, budou mít přímé, vysoce dostupné, geograficky redundantní připojení a optimalizované směrování do společnosti Microsoft. Služba peeringu je doplňkem portfolia připojení microsoftu:
*   ExpressRoute pro privátní připojení k prostředkům IaaS nebo PaaS (podpora privátního IP prostoru)
    *   Připojení založené na partnerech
    *   Přímé připojení 100G k Microsoftu
*   Protokol IPSEC přes Internet pro připojení VPN ke cloudu
*   Připojení SD-WAN k Azure prostřednictvím virtuální sítě WAN

Cílovým segmentem služby Peering Service je připojení SaaS, zákazníci SD-WAN, kteří jsou ochotni udělat internet breakout na pobočce a všichni zákazníci, kteří mají dvojí strategii MPLS a internet na podnikové úrovni.

Primárním cílem při připojování ke službě Microsoft Cloud by mělo být minimalizace latence zkrácením doby odezvy (RTT) z webu uživatele do sítě Microsoft Global Network, což je páteřní síť veřejné sítě společnosti Microsoft, která propojuje všechna datacentra společnosti Microsoft a více vstupních bodů cloudových aplikací. Viz [Získání nejlepšího připojení a výkonu v Office 365](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Obrázek distribuovaného přístupu](./media/distributed-access.png)

Na obrázku nad každou pobočkou globálního podniku se připojí k nejbližšímu možnému umístění microsoft edge prostřednictvím sítě partnera.

**Výhody zákazníků služby partnerského vztahu:**
* Nejlepší veřejné směrování přes internet do cloudových služeb Společnosti Microsoft pro optimální výkon a spolehlivost.
* Možnost vybrat preferovanou SP pro připojení k Microsoft Cloudu.
* Přehledy provozu, jako je hlášení latence a monitorování předpony.
* Optimální síťový chmel (AS Chmel) od společnosti Microsoft Cloud.
* Analýza a statistika tras – události pro hraniční bránu[(BGP)](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)trasy anomálie (detekce nevracení/únosu) a neoptimální směrování.

## <a name="peering-service-partnership-requirements"></a>Požadavky na partnerství služby peeringu
* Připojení ke službě Microsoft Cloud v umístění, které je zákazníkovi nejblíže. Partnerský poskytovatel služeb bude směrovat provoz uživatelů na okraj společnosti Microsoft nejblíže uživateli. Podobně na provoz směrem k uživateli, Microsoft bude směrovat provoz (pomocí značky BGP) do umístění hraniční ho nejblíže k uživateli a SP bude dodávat provoz uživateli.
* Partner bude udržovat vysokou dostupnou, vysokou propustnost a geograficky redundantní připojení se službou Microsoft Global Network.
* Partner může využít svůj stávající partnerský vztah k podpoře služby peeringu, pokud splňuje požadavky

## <a name="faq"></a>Nejčastější dotazy
Nejčastější dotazy najdete [v tématu Peering Service – FAQ](service-faqs.md).

## <a name="next-steps"></a>Další kroky

* Další informace o výhodách pro zákazníky se [službou Peering Service](https://docs.microsoft.com/azure/peering-service/).
* Přečtěte si o postupech povolení přímého partnerského vztahu pro službu partnerského vztahu [partnerského vztahu](walkthrough-peering-service-all.md).
