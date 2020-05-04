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
ms.openlocfilehash: 4a84c43b57ec4f632a2bfabb10d112e4975249bf
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733103"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer komponenty

Azure Load Balancer obsahuje několik klíčových komponent pro jeho operaci. Tyto komponenty můžete nakonfigurovat v předplatném prostřednictvím Azure Portal, Azure CLI, Azure PowerShell nebo šablon.

## <a name="frontend-ip-configurations"></a>Konfigurace IP adresy front-endu

IP adresa nástroje pro vyrovnávání zatížení. Je to kontaktní bod pro klienty. Tyto adresy můžou být buď:

- **Veřejná IP adresa**
- **Privátní IP adresa**

Výběr IP adresy určuje **typ** vytvořeného nástroje pro vyrovnávání zatížení. Výběr privátních IP adres vytvoří interní nástroj pro vyrovnávání zatížení. Výběr veřejné IP adresy vytvoří veřejný Nástroj pro vyrovnávání zatížení.

|  | Veřejný Load Balancer  | Interní nástroj pro vyrovnávání zatížení |
| ---------- | ---------- | ---------- |
| Konfigurace IP adresy front-endu| Veřejná IP adresa | Privátní IP adresa|
| Popis | Veřejný Nástroj pro vyrovnávání zatížení mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Nástroj pro vyrovnávání zatížení mapuje jiný způsob provozování odezvy z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.| Interní nástroj pro vyrovnávání zatížení distribuuje provoz do prostředků, které jsou uvnitř virtuální sítě. Azure omezuje přístup k IP adresám front-endu virtuální sítě s vyrovnáváním zatížení. Front-endové IP adresy a virtuální sítě se nikdy přímo nezveřejňují do internetového koncového bodu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků. |
| Podporované SKU | Basic, Standard | Basic, Standard |

![Příklad vrstveného nástroje pro vyrovnávání zatížení](./media/load-balancer-overview/load-balancer.png)

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
