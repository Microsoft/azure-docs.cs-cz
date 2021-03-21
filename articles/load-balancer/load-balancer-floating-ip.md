---
title: Azure Load Balancer konfigurace plovoucí IP adresy
description: Přehled Azure Load Balancer plovoucí IP adresy
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94699402"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer konfigurace plovoucí IP adresy

Nástroj pro vyrovnávání zatížení poskytuje několik možností pro aplikace UDP i TCP.

## <a name="floating-ip"></a>Plovoucí IP adresa

Některé scénáře aplikací dávají přednost nebo vyžadují, aby se stejný port používal ve více instancích aplikace na jednom virtuálním počítači ve fondu back-endu. Mezi běžné příklady opakovaného použití portů patří clustering pro vysokou dostupnost, síťová virtuální zařízení a vystavování více koncových bodů TLS bez nutnosti opětovného šifrování. Pokud chcete znovu použít port back-end v rámci více pravidel, musíte v definici pravidla povolit plovoucí IP adresu.

**Plovoucí IP adresa** je terminologie Azure, která je součástí toho, co je známo jako přímé vrácení serveru (DSR). DSR se skládá ze dvou částí:

- Topologie toků
- Schéma mapování IP adres

Na úrovni platformy Azure Load Balancer vždy pracuje s topologií toku DSR bez ohledu na to, zda je povolena plovoucí IP adresa. To znamená, že výstupní část toku je vždy správně přepsána do toku přímo zpět k původnímu zdroji.
Bez plovoucí IP adresy poskytuje Azure tradiční schéma mapování IP adres pro vyrovnávání zatížení, které umožňuje snadné použití (IP instance virtuálních počítačů). Povolením plovoucí IP adresy změní mapování IP adres na front-end IP adresu nástroje pro vyrovnávání zatížení, aby byla umožněna další flexibilita. Další informace najdete [tady](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Omezení

- Plovoucí IP adresa se v současné době nepodporuje u sekundárních konfigurací IP pro scénáře vyrovnávání zatížení.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít s používáním Load Balancer, přečtěte si téma [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) .
- Přečtěte si o [Azure Load Balancer odchozích připojeních](load-balancer-outbound-connections.md).
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Seznamte se s [sondami stavu](load-balancer-custom-probe-overview.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/network-security-groups-overview.md).