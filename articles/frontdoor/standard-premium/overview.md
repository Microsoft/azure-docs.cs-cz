---
title: Přední dvířka Azure (Standard/Premium) | Microsoft Docs
description: Tento článek poskytuje přehled služby Azure front dveří Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 32654f743301f9f2f6c010947d73d957c96dceac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100830"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Co je Azure front dveří Standard/Premium (Preview)?

> [!IMPORTANT]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Prohlédněte si [dokumentaci ke službě Azure front dveří](../front-door-overview.md).

Přední dvířka Azure (Standard/Premium) je rychlý, spolehlivý a zabezpečený moderní Cloud CDN, který využívá síť Microsoft Global Edge a integruje s inteligentní ochranou hrozeb. Kombinuje funkce Azure CDN Standard od Microsoftu, front Azure a firewallu webových aplikací (WAF) ve službě Azure Web Application firewall () do jediné zabezpečené platformy CDN cloudu.

Pomocí služby Azure front bran Standard/Premium můžete transformovat své globální aplikace a podnikové aplikace na zabezpečené a vysoce výkonné přizpůsobené moderní aplikace s obsahem, který je přístupný pro globální cílovou skupinu s nízkou latencí.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Architektura front-Premium pro Azure na úrovni Standard/Premium" lightbox="../media/overview/front-door-overview-expanded.png":::

Přední dveře Azure (Standard/Premium) fungují ve vrstvě 7 (vrstva HTTP/HTTPS) pomocí protokolu libovolného vysílání s rozdělenou sítí TCP a Microsoftem, aby se zlepšilo globální připojení. Na základě vaší metody směrování můžete zajistit, aby přední dveře Azure směrovaly požadavky klientů na nejrychlejší a nejvíce dostupný počátek. Back-end aplikace je jakákoli internetová služba hostovaná v rámci služby Azure nebo mimo ni. AzureFront dveří úrovně Standard/Premium poskytují řadu metod směrování provozu a možnosti monitorování stavu původu, které vyhovují různým požadavkům aplikací a automatickým scénářům pro převzetí služeb při selhání. Podobně jako u Traffic Manager je přední dveře odolná vůči chybám, včetně selhání do celé oblasti Azure.

Přední dvířka Azure také chrání svoji aplikaci na hranách pomocí brány firewall webových aplikací, ochrany bot a integrovaného rozložení 3/vrstvy 4 DDoS Protection. Zabezpečuje taky vaše soukromé back-endy pomocí služby privátního propojení. Přední dveře Azure poskytují nejlepší zabezpečení Microsoftu v globálním měřítku.  

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení.
>
> * Pokud chcete provést globální směrování na základě DNS **a nemáte požadavky** na ukončení protokolu TLS (Transport Layer Security) ("snižování zátěže SSL"), požadavky na protokol HTTP/HTTPS nebo zpracování aplikační vrstvy, přečtěte si [Traffic Manager](../../traffic-manager/traffic-manager-overview.md).
> * Pokud chcete vyrovnávat zatížení mezi servery v oblasti aplikační vrstvy, přečtěte si [Application Gateway](../../application-gateway/overview.md)
> * Pro vyrovnávání zatížení síťové vrstvy si přečtěte [Load Balancer](../../load-balancer/load-balancer-overview.md).
>
> Vaše ucelené scénáře můžou v případě potřeby těžit z kombinace těchto řešení.
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Přední dveře Azure úrovně Standard/Premium jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Proč používat Azure front-end Standard/Premium (Preview)?

Přední dveře Azure (Standard/Premium) poskytují jednu sjednocenou platformu pro statický obsah a dynamickou akceleraci aplikací s rozšířenými možnostmi zabezpečení. Přední dvířka také umožňují definovat, spravovat a monitorovat globální směrování pro vaši aplikaci.

Klíčové funkce, které jsou součástí služby Azure front dveří Standard/Premium (Preview):

- Urychlený výkon aplikace pomocí rozděleného protokolu libovolného vysílání založeného na protokolu TCP.

- Inteligentní monitorování **[stavu](concept-health-probes.md)** a vyrovnávání zatížení mezi **[zdroji](concept-origin.md)**.

- Definujte vlastní doménu s flexibilním ověřováním domén.

- Zabezpečení aplikace pomocí integrovaného [firewallu webových aplikací (WAF)](../../web-application-firewall/afds/afds-overview.md).

- Přesměrování zpracování SSL a integrovaná správa certifikátů.

- Zabezpečte své zdroje pomocí **[privátního odkazu](concept-private-link.md)**.  

- Přizpůsobitelná směrování a optimalizace provozu prostřednictvím **[sady pravidel](concept-rule-set.md)**.

- **[Předdefinované sestavy](how-to-reports.md)** s řídicím panelem All-in-One pro přední dveře a vzory zabezpečení.

- **[Monitorování a výstrahy v reálném čase](how-to-monitor-metrics.md)** , které se integrují s monitorováním Azure.

- **[Protokolování](how-to-logs.md)** požadavků na přední dveře a sondy stavu, které selhaly.

- Nativní podpora připojení mezi koncovým protokolem IPv6 a protokolu HTTP/2.

## <a name="pricing"></a>Ceny

Přední dveře Azure úrovně Standard/Premium mají dvě SKU, Standard a Premium. Viz [porovnání vrstev](tier-comparison.md). Informace o cenách najdete v tématu, které se věnuje [cenám služby Front Door](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Co je nového

Přihlaste se k odběru informačního kanálu RSS a na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) si prohlédněte nejnovější aktualizace funkcí služby Azure front-dveří.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit Front Door](create-front-door-portal.md).
