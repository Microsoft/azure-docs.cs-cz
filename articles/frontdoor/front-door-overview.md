---
title: Přední dvířka Azure | Microsoft Docs
description: Tento článek obsahuje přehled služby Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2020
ms.author: duau
ms.openlocfilehash: e9d719a29bc9a4b3e0428913e5bc1a91bec16623
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183161"
---
# <a name="what-is-azure-front-door"></a>Co je Azure Front Door?

Přední dvířka Azure jsou globální, škálovatelný vstupní bod, který využívá síť Microsoft Global Edge k vytváření rychlých, zabezpečených a široce škálovatelných webových aplikací. S předními dvířky můžete své globální aplikace a podnikové aplikace transformovat na robustní a vysoce výkonné přizpůsobené moderní aplikace s obsahem, který je přístupný pro globální cílovou skupinu prostřednictvím Azure.

<p align="center">
  <img src="./media/front-door-overview/front-door-visual-diagram.png" alt="Front Door architecture" width="600" title="Azure Front Door">
</p>

Přední dveře pracují ve vrstvě 7 (vrstva HTTP/HTTPS) pomocí protokolu libovolného vysílání s rozdělenou sítí TCP a Microsoft globální sítě pro zlepšení globálního připojení. Na základě metody směrování můžete zajistit, aby přední dvířka směrovala požadavky klientů na nejrychlejší a nejvíce dostupnou back-end aplikace. Back-end aplikace je jakákoli internetová služba hostovaná v rámci služby Azure nebo mimo ni. Přední dvířka poskytují řadu [metod směrování provozu](front-door-routing-methods.md) a [Možnosti sledování stavu back-endu](front-door-health-probes.md) , které vyhovují různým požadavkům aplikací a automatickým scénářům pro převzetí služeb při selhání. Podobně jako u [Traffic Manager](../traffic-manager/traffic-manager-overview.md)je přední dveře odolná vůči chybám, včetně selhání do celé oblasti Azure.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. 
> * Pokud chcete provést globální směrování na základě DNS **a nemáte požadavky** na ukončení protokolu TLS (Transport Layer Security) ("snižování zátěže SSL"), požadavky na protokol HTTP/HTTPS nebo zpracování aplikační vrstvy, přečtěte si [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Pokud chcete vyrovnávat zatížení mezi servery v oblasti aplikační vrstvy, přečtěte si [Application Gateway](../application-gateway/overview.md).
> * Pro vyrovnávání zatížení síťové vrstvy si přečtěte [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Vaše ucelené scénáře můžou v případě potřeby těžit z kombinace těchto řešení.
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-front-door"></a>Proč používat přední dvířka Azure?

S předními dvířky můžete vytvářet, provozovat a škálovat dynamickou webovou aplikaci a statický obsah. Přední dvířka umožňují definovat, spravovat a monitorovat globální směrování pro webový provoz optimalizací vysokého výkonu a spolehlivosti koncového uživatele na nejvyšší úrovni, a to díky rychlému globálnímu převzetí služeb při selhání.

Klíčové funkce, které jsou součástí front-dveří:

* Urychlený výkon aplikace pomocí rozděleného **[protokolu libovolného vysílání](front-door-routing-architecture.md#anycast)** založeného na **[protokolu TCP](front-door-routing-architecture.md#splittcp)**.

* Monitorování inteligentní **[sondy stavu](front-door-health-probes.md)** pro back-endové prostředky

*  **[Adresa URL – směrování na základě cesty](front-door-route-matching.md)** pro žádosti.

* Umožňuje hostování více webů pro efektivní aplikační infrastrukturu. 

* **[Spřažení relace](front-door-routing-methods.md#affinity)** na základě souborů cookie.

* **[Snižování zátěže SSL](front-door-custom-domain-https.md)** a Správa certifikátů.

* Definujte vlastní **[doménu](front-door-custom-domain.md)**. 

* Zabezpečení aplikace pomocí integrovaného  **[firewallu webových aplikací (WAF)](../web-application-firewall/overview.md)**.

* Přesměrujte provoz HTTP na HTTPS pomocí **[přesměrování adresy URL](front-door-url-redirect.md)**.

* Vlastní cesta přesměrování s **[přepisem adresy URL](front-door-url-rewrite.md)**

* Nativní podpora připojení mezi koncovým protokolem IPv6 a **[protokolu HTTP/2](front-door-http2.md)**.

## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu, které se věnuje [cenám služby Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). Přečtěte si [smlouvu SLA pro přední dveře Azure](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Co je nového

Přihlaste se k odběru informačního kanálu RSS a na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) si prohlédněte nejnovější aktualizace funkcí služby Azure front-dveří.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).