---
title: Azure Load Balancer komponenty
description: Přehled komponent Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: 6bf090cde7262fdae9c98ef55227bf2925937dbf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739827"
---
# <a name="azure-load-balancer-components"></a>Azure Load Balancer komponenty

Azure Load Balancer obsahuje několik klíčových součástí. Tyto komponenty můžete nakonfigurovat v předplatném prostřednictvím:

* portál Azure
* Azure CLI
* Azure PowerShell
* Šablony Resource Manageru

## <a name="frontend-ip-configuration"></a>Konfigurace IP adresy front-endu <a name = "frontend-ip-configurations"></a>

IP adresa vašeho Azure Load Balancer. Je to kontaktní bod pro klienty. Tyto IP adresy můžou být buď:

- **Veřejná IP adresa**
- **Privátní IP adresa**

Povaha IP adresy určuje **typ** vytvořeného nástroje pro vyrovnávání zatížení. Výběr privátních IP adres vytvoří interní nástroj pro vyrovnávání zatížení. Výběr veřejné IP adresy vytvoří veřejný Nástroj pro vyrovnávání zatížení.

|  | Veřejný nástroj pro vyrovnávání zatížení  | Interní nástroj pro vyrovnávání zatížení |
| ---------- | ---------- | ---------- |
| **Konfigurace IP adresy front-endu**| Veřejná IP adresa | Privátní IP adresa|
| **Popis** | Veřejný Nástroj pro vyrovnávání zatížení mapuje veřejnou IP adresu a port příchozího provozu na privátní IP adresu a port virtuálního počítače. Nástroj pro vyrovnávání zatížení mapuje jiný způsob provozování odezvy z virtuálního počítače. Pomocí pravidel vyrovnávání zatížení můžete distribuovat konkrétní typy provozu napříč několika virtuálními počítači nebo službami. Můžete například rozložit zatížení provozu webových požadavků mezi několik webových serverů.| Interní nástroj pro vyrovnávání zatížení distribuuje provoz do prostředků, které jsou uvnitř virtuální sítě. Azure omezuje přístup k IP adresám front-endu virtuální sítě s vyrovnáváním zatížení. Front-endové IP adresy a virtuální sítě se nikdy přímo nezveřejňují do internetového koncového bodu. Interní obchodní aplikace se spouštějí v Azure a přistupuje se k nim v rámci Azure nebo z místních prostředků. |
| **Podporované SKU** | Basic, Standard | Basic, Standard |

![Příklad vrstveného nástroje pro vyrovnávání zatížení](./media/load-balancer-overview/load-balancer.png)

Nástroj pro vyrovnávání zatížení může mít několik front-endové IP adresy. Další informace o [několika front-endu](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>Back-endový fond

Skupina virtuálních počítačů nebo instancí v sadě škálování virtuálního počítače, která obsluhuje příchozí požadavek. Chcete-li efektivně škálovat náklad na velké objemy příchozích přenosů dat, výpočetní pokyny obecně doporučují přidání dalších instancí do back-endu fondu.

Nástroj pro vyrovnávání zatížení se při škálování instancí nahoru nebo dolů okamžitě překonfiguruje prostřednictvím automatické změny konfigurace. Přidáním nebo odebráním virtuálních počítačů z back-end fondu překonfigurujete Nástroj pro vyrovnávání zatížení bez dalších operací. Obor back-end fondu je libovolný virtuální počítač ve virtuální síti.

Při zvažování, jak navrhnout back-end fond, můžete navrhnout minimální počet jednotlivých prostředků fondu back-end pro optimalizaci délky operací správy. Nedochází k žádnému rozdílu ve výkonu a měřítku roviny dat.

## <a name="health-probes"></a>Sondy stavu

Sonda stavu se používá k určení stavu instancí ve fondu back-endu. Během vytváření nástroje pro vyrovnávání zatížení nakonfigurujte sondu stavu pro použití nástroje pro vyrovnávání zatížení.  Tato sonda stavu určí, jestli je instance v pořádku a může přijímat provoz.

Pro sondy stavu můžete definovat prahovou hodnotu, která není v pořádku. Když sonda přestane reagovat, nástroj pro vyrovnávání zatížení zastaví odesílání nových připojení do instancí, které nejsou v pořádku. Selhání sondy nemá vliv na existující připojení. Připojení pokračuje do aplikace:

- Ukončí tok.
- Vypršel časový limit nečinnosti
- Virtuální počítač se vypne.

Nástroj pro vyrovnávání zatížení poskytuje pro koncové body různé typy sond stavu: TCP, HTTP a HTTPS. [Přečtěte si další informace o Load Balancer sondy stavu](load-balancer-custom-probe-overview.md).

Nástroj pro vyrovnávání zatížení úrovně Basic nepodporuje testy protokolu HTTPS. Nástroj pro vyrovnávání zatížení úrovně Basic uzavře všechna připojení TCP (včetně zavedených připojení).

## <a name="load-balancing-rules"></a>Pravidla vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce příchozího provozu do **všech** instancí v rámci fondu back-endu. Pravidlo vyrovnávání zatížení mapuje danou konfiguraci IP adresy front-endu a port na více IP adres a portů back-endu.

Použijte například pravidlo vyrovnávání zatížení pro port 80 ke směrování provozu z vaší front-endu IP na port 80 back-end instancí.

:::image type="content" source="./media/load-balancer-components/lbrules.png" alt-text="Referenční diagram pravidla nástroje pro vyrovnávání zatížení" border="false":::

*Obrázek: Load-Balancing pravidla*

## <a name="high-availability-ports"></a>Porty s vysokou dostupností

Pravidlo nástroje pro vyrovnávání zatížení, které je nakonfigurováno pomocí **Protocol-All a port-0**. 

Toto pravidlo aktivuje jedno pravidlo pro vyrovnávání zatížení všech toků TCP a UDP, které přicházejí na všechny porty interního Standard Load Balancer. 

Rozhodnutí o vyrovnávání zatížení se provádí na jeden tok. Tato akce je založena na následujících pěti připojeních řazené kolekce členů: 

1. zdrojová IP adresa
2. zdrojový port
3. cílová IP adresa
4. cílový port
5. protokol

Pravidla vyrovnávání zatížení portů HA vám pomůžou s kritickými scénáři, jako je vysoká dostupnost a škálování síťových virtuálních zařízení (síťová virtuální zařízení) uvnitř virtuálních sítí. Tato funkce může pomáhat při vyrovnávání zatížení velkého počtu portů.

<p align="center">
  <img src="./media/load-balancer-components/harules.svg" alt="Figure depicts how Azure Load Balancer directs all frontend ports to three instances of all backend ports" width="512" title="Pravidla portů HA">
</p>

*Obrázek: pravidla portů HA*

Další informace o [portech ha](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Příchozí pravidla NAT

Příchozí pravidlo překladu adres (NAT) přepošle příchozí provoz odeslaný do kombinace IP adresy a portu front-endu. Přenosy se odesílají do **konkrétního** virtuálního počítače nebo instance ve fondu back-endu. Předávání portů se provádí stejnou distribucí založenou na hodnotě hash jako vyrovnávání zatížení.

:::image type="content" source="./media/load-balancer-components/inboundnatrules.png" alt-text="Referenční diagram pravidla příchozího překladu adres (NAT)" border="false":::

*Obrázek: příchozí pravidla NAT*

Příchozí pravidla NAT v kontextu Virtual Machine Scale Sets jsou příchozí fondy NAT. Přečtěte si další informace o [komponentách Load Balancer a sadě škálování virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Pravidla odchozích přenosů

Odchozí pravidlo konfiguruje odchozí překlad síťových adres (NAT) pro všechny virtuální počítače nebo instance identifikované fondem back-end. Toto pravidlo umožňuje, aby instance v back-endu komunikovaly (odchozí) na Internet nebo jiné koncové body.

Přečtěte si další informace o [odchozích připojeních a pravidlech](load-balancer-outbound-connections.md).

Load Balancer úrovně Basic nepodporuje odchozí pravidla.

:::image type="content" source="./media/load-balancer-components/outbound-rules.png" alt-text="Referenční diagram odchozího pravidla" border="false":::

*Obrázek: odchozí pravidla*

## <a name="limitations"></a>Omezení

- Další informace o [limitech](../azure-resource-manager/management/azure-subscription-service-limits.md) pro vyrovnávání zatížení 
- Nástroj pro vyrovnávání zatížení poskytuje vyrovnávání zatížení a přesměrování portů pro konkrétní protokoly TCP a UDP. Pravidla vyrovnávání zatížení a pravidla příchozího překladu adres (NAT) podporují protokoly TCP a UDP, ale ne jiné protokoly IP, včetně protokolu ICMP.
- Odchozí tok z back-endu virtuálního počítače do front-endu interního Load Balancer se nezdaří.
- Pravidlo nástroje pro vyrovnávání zatížení nemůže zahrnovat dvě virtuální sítě.  Front-endové a jejich instance back-endu se musí nacházet ve stejné virtuální síti.  
- Předávání fragmentů IP adres není u pravidel vyrovnávání zatížení podporováno. Pro pravidla vyrovnávání zatížení se nepodporují fragmentace paketů UDP a TCP. Pravidla pro vyrovnávání zatížení portů HA lze použít k přeposílání stávajících fragmentů IP adres. Další informace najdete v tématu [Přehled portů vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Na jednu skupinu dostupnosti můžete mít jenom 1 veřejné Load Balancer a 1 interní Load Balancer

## <a name="next-steps"></a>Další kroky

- Pokud chcete začít s používáním Load Balancer, přečtěte si téma [vytvoření veřejného nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-portal.md) .
- Přečtěte si další informace o [Azure Load Balancer](load-balancer-overview.md).
- Informace o [veřejné IP adrese](../virtual-network/virtual-network-public-ip-address.md)
- Informace o [privátní IP adrese](../virtual-network/private-ip-addresses.md)
- Přečtěte si o používání [nástroje Load Balancer úrovně Standard a zóny dostupnosti](load-balancer-standard-availability-zones.md).
- Seznamte se s [diagnostikou standardního nástroje pro vyrovnávání zatížení](load-balancer-standard-diagnostics.md).
- Přečtěte si o [resetování protokolu TCP při nečinnosti](load-balancer-tcp-reset.md).
- Seznamte [se s nástrojem Load Balancer úrovně Standard s porty vysoké dostupnosti](load-balancer-ha-ports-overview.md).
- Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
- Přečtěte si další informace o [limitech pro vyrovnávání zatížení](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer).
- Přečtěte si informace o používání [přesměrování portů](./tutorial-load-balancer-port-forwarding-portal.md).
