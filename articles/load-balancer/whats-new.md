---
title: Co je nového v Azure Load Balancer
description: Přečtěte si, co je nového v Azure Load Balancer, jako je například nejnovější zpráva k vydání verze, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a30a42e8a8c4049b53274da512089dd29965e775
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573149"
---
# <a name="whats-new-in-azure-load-balancer"></a>Co je nového v Azure Load Balancer?

Azure Load Balancer se pravidelně aktualizují. Udržujte si přehled o nejnovějších oznámeních. V tomto článku najdete informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce (Pokud je k dispozici)

Nejnovější Azure Load Balancer aktualizace a přihlásit se k odběru informačního kanálu RSS můžete také najít [zde](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Poslední verze

| Typ |Název |Popis  |Datum přidání  |
| ------ |---------|---------|---------|
| Příznak | Podpora přesunů mezi skupinami prostředků | Přesun Standard Load Balancer a standardní podpora veřejných IP adres pro [skupinu prostředků](https://azure.microsoft.com/updates/standard-resource-group-move/). | Říjen 2020 |
| Příznak | Podpora správy fondů back-endu založených na protokolu IP (verze Preview) | Azure Load Balancer podporuje přidávání a odebírání prostředků z back-endového fondu prostřednictvím adres IPv4 nebo IPv6. To umožňuje snadnou správu kontejnerů, virtuálních počítačů a sady škálování virtuálních počítačů přidružených k Load Balancer. Umožní taky, aby se IP adresy rezervovaly jako součást fondu back-endu před vytvořením přidružených prostředků. Další informace najdete [tady](backend-pool-management.md).|Červenec 2020 |
| Příznak| Azure Load Balancer přehledy pomocí Azure Monitor | Jako součást Azure Monitor pro sítě mají zákazníci nyní topologické mapy pro všechny své Load Balancer konfigurace a řídicí panely stavu pro své standardní nástroje pro vyrovnávání zatížení, které jsou předem nakonfigurované s metrikami v Azure Portal. [Začněte a získejte další informace](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Červen 2020 |
| Ověřování | Přidání ověření pro porty HA | Bylo přidáno ověření, které zajistí, že pravidla portů HA a pravidla portů bez vysoké dostupnosti jsou konfigurovatelná pouze v případě, že je povolena plovoucí IP adresa. Dříve tato konfigurace procházela, ale nefunguje tak, jak je zamýšlená. Nebyla provedena žádná změna funkčnosti. Další informace najdete [tady](load-balancer-ha-ports-overview.md#limitations) .| Červen 2020 |
| Příznak| Podpora protokolu IPv6 pro Azure Load Balancer (všeobecně dostupná) | Pro vaše front-end služby Vyrovnávání zatížení Azure můžete mít IPv6 adresy. Další informace o tom, jak [vytvořit aplikaci s duálním zásobníkem](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Duben 2020|
| Příznak| Resetování TCP při nečinnosti časového limitu (všeobecně dostupné)| Použijte resety TCP k vytvoření předvídatelného chování aplikace. [Další informace](load-balancer-tcp-reset.md)| Únor 2020 |

## <a name="known-issues"></a>Známé problémy

Produktová skupina aktivně pracuje na řešeních pro následující známé problémy:

|Problém |Popis  |Omezení rizik  |
| ---------- |---------|---------|
| Load Balancer protokoly událostí výstrah a stavu sondy stavu | Protokolování nefunguje pro Load Balancer události výstrah pro základní a Standard Load Balancer nebo protokoly stavu sondy stavu pro základní Load Balancer  | [Využijte Azure monitor pro multidimenzionální metriky pro vaši Standard Load Balancer](load-balancer-standard-diagnostics.md). Azure Monitor poskytuje vizualizaci pro bohatou sadu multidimenzionálních metrik, která se dá také exportovat jako protokoly. Řídicí panel předem konfigurovaných metrik můžete využít v podokně přehledy Load Balancer. Pokud používáte základní Load Balancer [upgradujte na standard](upgrade-basic-standard.md) pro monitorování metrik na úrovni produkčního prostředí.

  

## <a name="next-steps"></a>Další kroky

Další informace o Azure Load Balancer najdete v tématu [co je Azure Load Balancer?](load-balancer-overview.md) a [Nejčastější dotazy](load-balancer-faqs.md).
