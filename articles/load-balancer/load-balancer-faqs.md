---
title: Nejčastější dotazy – Azure Load Balancer
description: Odpovědi na nejčastější dotazy týkající se Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3648bb102d539156067cdc259c1cf9b6e73d6a49
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884485"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="what-types-of-load-balancer-exist"></a>Jaké typy Load Balancer existují?
Interní nástroje pro vyrovnávání zatížení, které vyrovnávají provoz v rámci virtuální sítě a externích nástrojů pro vyrovnávání zatížení, které vyrovnávají provoz z koncového bodu připojeného k Další informace naleznete v tématu [Load Balancer Types]https://docs.microsoft.com/azure/load-balancer/concepts-limitations#load-balancer-types)(. 

Pro oba tyto typy nabízí Azure základní SKU a standardní SKU, které mají různé funkce pro funkční, výkon, zabezpečení a sledování stavu. Tyto rozdíly jsou vysvětleny v našem [porovnání SKU]https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) (článek.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Jak můžu upgradovat z úrovně Basic na Standard Load Balancer?
Další informace o automatizovaném skriptu a pokynech k upgradu Load Balancer SKU najdete v článku věnovaném [upgradu ze základního na standardní](upgrade-basic-standard.md) .

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Jaké jsou různé možnosti vyrovnávání zatížení v Azure?
V tématu [Průvodce technologií pro vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) najdete dostupné služby Vyrovnávání zatížení a doporučená použití pro každý z nich.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Kde najdu Load Balancer šablony ARM?
Přečtěte si [seznam šablon pro rychlý start Azure Load Balancer](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) pro šablony ARM běžných nasazení.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Jak se liší pravidla příchozího překladu adres (NAT) od pravidel vyrovnávání zatížení?
Pravidla překladu adres (NAT) slouží k určení prostředku back-end pro směrování provozu do. Například konfigurace konkrétního portu nástroje pro vyrovnávání zatížení pro odesílání provozu RDP na konkrétní virtuální počítač. Pravidla vyrovnávání zatížení se používají k určení fondu back-end prostředků ke směrování provozu, vyrovnání zatížení napříč jednotlivými instancemi. Například pravidlo nástroje pro vyrovnávání zatížení může směrovat pakety TCP na portu 80 nástroje pro vyrovnávání zatížení napříč fondem webových serverů.

## <a name="next-steps"></a>Další kroky
Pokud Váš dotaz není uvedený výše, pošlete nám prosím svůj názor na tuto stránku s vaším dotazem. Tím se vytvoří problém GitHubu pro produktový tým, aby se zajistila odpověď na všechny naše oceněné dotazy zákazníků.
