---
title: Nejčastější dotazy – Azure Load Balancer
description: Odpovědi na nejčastější dotazy týkající se Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005157"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="what-types-of-load-balancer-exist"></a>Jaké typy Load Balancer existují?
Interní nástroje pro vyrovnávání zatížení, které vyrovnávají provoz v rámci virtuální sítě a externích nástrojů pro vyrovnávání zatížení, které vyrovnávají provoz z koncového bodu připojeného k Další informace najdete v tématu [typy Load Balancer](components.md#frontend-ip-configurations). 

Pro oba tyto typy nabízí Azure základní SKU a standardní SKU, které mají různé funkce pro funkční, výkon, zabezpečení a sledování stavu. Tyto rozdíly jsou vysvětleny v tomto článku o [porovnání SKU](skus.md) .

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
