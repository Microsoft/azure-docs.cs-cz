---
title: Co je nového v Azure Load Balancer
description: Přečtěte si, co je nového v Azure Load Balancer, jako je například nejnovější zpráva k vydání verze, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86148048"
---
# <a name="whats-new-in-azure-load-balancer"></a>Co je nového v Azure Load Balancer?

Azure Load Balancer se pravidelně aktualizují. Udržujte si přehled o nejnovějších oznámeních. V tomto článku najdete informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce (Pokud je k dispozici)

Nejnovější Azure Load Balancer aktualizace a přihlásit se k odběru informačního kanálu RSS můžete také najít [zde](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="new-features"></a>Nové funkce

|Funkce  |Popis  |Datum přidání  |
|---------|---------|---------|
| Podpora správy fondů back-endu založených na protokolu IP (verze Preview) | Azure Load Balancer podporuje přidávání a odebírání prostředků z back-endového fondu prostřednictvím adres IPv4 nebo IPv6. To umožňuje snadnou správu kontejnerů, virtuálních počítačů a sady škálování virtuálních počítačů přidružených k Load Balancer. Umožní taky, aby se IP adresy rezervovaly jako součást fondu back-endu před vytvořením přidružených prostředků. | Červenec 2020 |
| Azure Load Balancer přehledy pomocí Azure Monitor | Jako součást Azure Monitor pro sítě mají zákazníci nyní topologické mapy pro všechny své Load Balancer konfigurace a řídicí panely stavu pro své standardní nástroje pro vyrovnávání zatížení, které jsou předem nakonfigurované s metrikami v Azure Portal. [Začněte a získejte další informace](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Červen 2020 |
| Podpora protokolu IPv6 pro Azure Load Balancer (všeobecně dostupná) | Pro vaše front-end služby Vyrovnávání zatížení Azure můžete mít IPv6 adresy. Další informace o tom, jak [vytvořit aplikaci s duálním zásobníkem](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Duben 2020|
| Resetování TCP při nečinnosti časového limitu (všeobecně dostupné)| Použijte resety TCP k vytvoření předvídatelného chování aplikace. [Další informace](load-balancer-tcp-reset.md)| Únor 2020 |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Load Balancer najdete v tématu [co je Azure Load Balancer?](load-balancer-overview.md) a [Nejčastější dotazy](load-balancer-faqs.md).