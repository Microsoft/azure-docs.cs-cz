---
title: Azure Load Balancer komponenty
description: Přehled komponent Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 532dc313a673d28ffe4fc66060d6dcb491ce866c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691271"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer komponenty

Azure Load Balancer obsahuje několik klíčových komponent pro jeho operaci.  

Tyto komponenty můžete nakonfigurovat v předplatném prostřednictvím Azure Portal, Azure CLI, Azure PowerShell nebo šablon.

## <a name="frontend-ip-configurations"></a>Konfigurace IP adresy front-endu

IP adresa nástroje pro vyrovnávání zatížení. Je to kontaktní bod pro klienty. Tyto adresy můžou být buď:

- **Veřejná IP adresa**
- **Privátní IP adresa**

Výběr IP adresy určuje **typ** vytvořeného nástroje pro vyrovnávání zatížení. Výběr privátních IP adres vytvoří interní nástroj pro vyrovnávání zatížení. Výběr veřejné IP adresy vytvoří veřejný Nástroj pro vyrovnávání zatížení.

## <a name="backend-pool"></a>Back-endový fond

Skupina virtuálních počítačů nebo instancí v sadě škálování virtuálního počítače, která obsluhuje příchozí požadavek. Chcete-li efektivně škálovat náklad na velké objemy příchozích přenosů dat, výpočetní pokyny obecně doporučují přidání dalších instancí do back-endu fondu. 

Nástroj pro vyrovnávání zatížení se při škálování instancí nahoru nebo dolů okamžitě překonfiguruje prostřednictvím automatické změny konfigurace. Přidáním nebo odebráním virtuálních počítačů z back-end fondu překonfigurujete Nástroj pro vyrovnávání zatížení bez dalších operací. Obor back-end fondu je libovolný virtuální počítač ve virtuální síti. 

Při zvažování, jak navrhnout back-end fond, můžete navrhnout minimální počet jednotlivých prostředků fondu back-end pro optimalizaci délky operací správy. Nedochází k žádnému rozdílu ve výkonu a měřítku roviny dat.

## <a name="health-probes"></a>Sondy stavu

Sonda stavu se používá k určení stavu instancí ve fondu back-endu. Pro sondy stavu můžete definovat prahovou hodnotu, která není v pořádku. Když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání nových připojení do instancí, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. Připojení pokračuje do aplikace:

- Ukončí tok.
- Vypršel časový limit nečinnosti
- Virtuální počítač se vypne.

Load Balancer poskytuje pro koncové body různé typy sond stavu:

- TCP
- HTTP
- HTTPS

Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje testy protokolu HTTPS. Nástroj pro vyrovnávání zatížení úrovně Basic uzavře všechna připojení TCP (včetně zavedených připojení).

## <a name="load-balancing-rules"></a>Pravidla vyrovnávání zatížení

Pravidla vyrovnávání zatížení oznamují nástroji pro vyrovnávání zatížení, co dělat. Pravidlo vyrovnávání zatížení mapuje danou konfiguraci IP adresy front-endu a port na více IP adres a portů back-endu.

## <a name="inbound-nat-rules"></a>Příchozí pravidla NAT

Příchozí pravidla překladu adres (NAT) předávají provoz z IP adresy front-endu do instance back-endu v rámci virtuální sítě. Předávání portů se provádí stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení. 

Příkladem použití jsou relace protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) k oddělení instancí virtuálních počítačů v rámci virtuální sítě. Několik vnitřních koncových bodů lze namapovat na porty na stejné front-endové IP adrese. Front-endové IP adresy se dají použít k vzdálené správě virtuálních počítačů bez dalšího pole s odkazem.

## <a name="outbound-rules"></a>Odchozí pravidla

Odchozí pravidlo konfiguruje odchozí překlad síťových adres (NAT) pro všechny virtuální počítače nebo instance identifikované fondem back-end.

Load Balancer úrovně Basic nepodporuje odchozí pravidla.
![Nástroj pro vyrovnávání zatížení Azure](./media/load-balancer-overview/load-balancer-overview.png)

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít s používáním Load Balancer, přečtěte si téma [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) .
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Informace o [veřejné IP adrese](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Informace o [privátní IP adrese](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Přečtěte si o používání [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si o [resetování protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- Přečtěte si o [Standard Load Balancer s pravidly pro vyrovnávání zatížení portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Přečtěte si o použití [Load Balancer s více front-endu](load-balancer-multivip-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Seznamte se s [typy sond](load-balancer-custom-probe-overview.md#types).
- Přečtěte si další informace o [limitech pro vyrovnávání zatížení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Přečtěte si informace o používání [přesměrování portů](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Přečtěte si další informace o [odchozích pravidlech nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
