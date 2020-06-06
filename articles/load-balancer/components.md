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
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: b696cdf2d54c42d3967041c5d10b1bd9bb5a3065
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84448678"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer komponenty

Azure Load Balancer se skládá z několika klíčových součástí. Ty můžete nakonfigurovat v předplatném prostřednictvím Azure Portal, Azure CLI, Azure PowerShell nebo šablon.

## <a name="frontend-ip-configuration"></a>Konfigurace IP adresy front-endu<a name = "frontend-ip-configurations"></a>

IP adresa vašeho Azure Load Balancer. Je kontaktním bodem pro klienty. Tyto IP adresy můžou být buď:

- **Veřejná IP adresa**
- **Privátní IP adresa**

Povaha IP adresy určuje **typ** vytvořeného nástroje pro vyrovnávání zatížení. Výběr privátních IP adres vytvoří interní nástroj pro vyrovnávání zatížení. Výběr veřejné IP adresy vytvoří veřejný Nástroj pro vyrovnávání zatížení.

|  | Veřejný Load Balancer  | Interní nástroj pro vyrovnávání zatížení |
| ---------- | ---------- | ---------- |
| Konfigurace IP adresy front-endu| Veřejná IP adresa | Privátní IP adresa|
| Description | Veřejný Nástroj pro vyrovnávání zatížení mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Nástroj pro vyrovnávání zatížení mapuje jiný způsob provozování odezvy z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.| Interní nástroj pro vyrovnávání zatížení distribuuje provoz do prostředků, které jsou uvnitř virtuální sítě. Azure omezuje přístup k IP adresám front-endu virtuální sítě s vyrovnáváním zatížení. Front-endové IP adresy a virtuální sítě se nikdy přímo nezveřejňují do internetového koncového bodu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků. |
| Podporované SKU | Basic, Standard | Basic, Standard |

![Příklad vrstveného nástroje pro vyrovnávání zatížení](./media/load-balancer-overview/load-balancer.png)

Load Balancer může mít několik front-endové IP adresy. Další informace o [několika front-endu](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>Back-endový fond

Skupina virtuálních počítačů nebo instancí v sadě škálování virtuálního počítače, která obsluhuje příchozí požadavek. Chcete-li efektivně škálovat náklad na velké objemy příchozích přenosů dat, výpočetní pokyny obecně doporučují přidání dalších instancí do back-endu fondu.

Nástroj pro vyrovnávání zatížení se při škálování instancí nahoru nebo dolů okamžitě překonfiguruje prostřednictvím automatické změny konfigurace. Přidáním nebo odebráním virtuálních počítačů z back-end fondu překonfigurujete Nástroj pro vyrovnávání zatížení bez dalších operací. Obor back-end fondu je libovolný virtuální počítač ve virtuální síti.

Při zvažování, jak navrhnout back-end fond, můžete navrhnout minimální počet jednotlivých prostředků fondu back-end pro optimalizaci délky operací správy. Nedochází k žádnému rozdílu ve výkonu a měřítku roviny dat.

## <a name="health-probes"></a>Sondy stavu

Sonda stavu se používá k určení stavu instancí ve fondu back-endu. Při vytváření Load Balancer musíte nakonfigurovat sondu stavu, kterou může Load Balancer použít k určení, jestli je instance v pořádku, a jak do ní směrovat provoz.

Pro sondy stavu můžete definovat prahovou hodnotu, která není v pořádku. Když sonda přestane reagovat, Load Balancer zastaví odesílání nových připojení k instancím, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. Připojení pokračuje do aplikace:

- Ukončí tok.
- Vypršel časový limit nečinnosti
- Virtuální počítač se vypne.

Load Balancer poskytuje pro koncové body různé typy sond stavu: TCP, HTTP a HTTPS. [Přečtěte si další informace o Load Balancer sondy stavu](load-balancer-custom-probe-overview.md).

Základní Load Balancer nepodporují testy HTTPS. Základní Load Balancer ukončí všechna připojení TCP (včetně zavedených připojení).

## <a name="load-balancing-rules"></a>Pravidla vyrovnávání zatížení

Pravidlo Load Balancer slouží k definování způsobu distribuce příchozího provozu do **všech** instancí v rámci fondu back-endu. Pravidlo vyrovnávání zatížení mapuje danou konfiguraci IP adresy front-endu a port na více IP adres a portů back-endu.

Například pokud chcete, aby byl provoz na portu 80 (nebo jiný port) vaší front-endu směrován na port 80 všech instancí back-endu, použili byste pravidlo vyrovnávání zatížení k dosažení tohoto.

### <a name="high-availability-ports"></a>Porty s vysokou dostupností

Load Balancer pravidlo s konfigurací Protocol-All a port-0. Díky tomu je možné zajistit vyrovnávání zatížení všech toků TCP a UDP, které přicházejí na všechny porty interního Standard Load Balancer, pomocí jediného pravidla. Rozhodnutí o vyrovnávání zatížení se provádí na jeden tok. Tato akce je založena na následujících pěti připojeních řazené kolekce členů: 
1. zdrojová IP adresa
2. zdrojový port
3. cílová IP adresa
4. cílový port
5. protokol

Pravidla vyrovnávání zatížení portů HA vám pomůžou s kritickými scénáři, jako je vysoká dostupnost a škálování síťových virtuálních zařízení (síťová virtuální zařízení) uvnitř virtuálních sítí. Tato funkce může také pomáhat při vyrovnávání zatížení velkého počtu portů.

Další informace o [portech ha](load-balancer-ha-ports-overview.md)najdete v.

## <a name="inbound-nat-rules"></a>Příchozí pravidla NAT

Příchozí pravidlo překladu adres (NAT) přepošle příchozí provoz odeslaný na vybranou kombinaci IP adresy a portu front-endu na **konkrétní** virtuální počítač nebo instanci back-end fondu. Předávání portů se provádí stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení.

Například pokud chcete, aby relace protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) oddělují instance virtuálních počítačů ve fondu back-endu. Do portů na stejné IP adrese front-endu lze namapovat více interních koncových bodů. IP adresy front-endu lze použít k vzdálené správě virtuálních počítačů bez dalšího pole s odkazem.

Příchozí pravidla NAT v kontextu Virtual Machine Scale Sets (VMSS) jsou příchozí fondy NAT. Přečtěte si další informace o [komponentách Load Balancer a VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Odchozí pravidla

Odchozí pravidlo konfiguruje odchozí překlad síťových adres (NAT) pro všechny virtuální počítače nebo instance identifikované fondem back-end. To umožňuje, aby instance v back-endu komunikovaly (odchozí) na Internet nebo jiné koncové body.

Přečtěte si další informace o [odchozích připojeních a pravidlech](load-balancer-outbound-connections.md).

Load Balancer úrovně Basic nepodporuje odchozí pravidla.

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít s používáním Load Balancer, přečtěte si téma [Vytvoření veřejné Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) .
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Informace o [veřejné IP adrese](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Informace o [privátní IP adrese](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Přečtěte si o používání [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md).
- Další informace o [diagnostice Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Přečtěte si o [resetování protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- Přečtěte si o [Standard Load Balancer s pravidly pro vyrovnávání zatížení portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md).
- Přečtěte si další informace o [limitech pro vyrovnávání zatížení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Přečtěte si informace o používání [přesměrování portů](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
