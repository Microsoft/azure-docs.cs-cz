---
title: Použít veřejné IP adresy po převzetí služeb při selhání pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak nastavit veřejné IP adresy s Azure Site Recovery a Azure Traffic Manager pro zotavení po havárii a migrace
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: mayg
ms.openlocfilehash: 80c38ecc766d60fba578e877998aeb216ea66012
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215272"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Nastavení veřejné IP adresy po převzetí služeb při selhání

Veřejné IP adresy umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure odchozí komunikaci s internetovými službami a veřejně přístupnými službami Azure prostřednictvím IP adresy přiřazené prostředku.
- Příchozí komunikace ze sítě Internet k prostředku, jako jsou Azure Virtual Machines (VM), služby Azure Application Gateway, nástroje pro vyrovnávání zatížení Azure, Azure VPN Gateway a dalších. Můžete dál komunikovat s některými prostředky, jako jsou virtuální počítače z Internetu, pokud virtuální počítač nemá veřejnou IP adresu přiřazenou jako virtuální počítač je součástí fondu back-end nástroje pro vyrovnávání zatížení a nástroje pro vyrovnávání zatížení je přiřazena veřejná IP adresa.
- Odchozí připojení k Internetu pomocí předvídatelné IP adresy. Virtuální počítač může například komunikovat odchozí k Internetu bez veřejné IP adresy přiřazené k ní, ale její adresa je adresa sítě přeložit pomocí Azure na veřejnou adresu nepředvídatelné ve výchozím nastavení. Veřejnou IP adresu přiřadit prostředku umožňuje vědět, které IP adresa se používá pro odchozí připojení. I když je předvídatelné, můžete změnit na adresu, v závislosti na zvolené metodě přiřazení. Další informace najdete v tématu [vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Další informace o odchozích připojení z prostředků Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

V Azure Resource Manageru, veřejná IP adresa je prostředek, který má své vlastní vlastnosti. Mezi prostředky, ke kterým můžete přiřadit prostředek s veřejnou IP adresou, patří:

* Síťová rozhraní virtuálního počítače
* Internetové nástroje pro vyrovnávání zatížení
* VPN Gateway
* Application Gateway

Tento článek popisuje, jak můžete pomocí veřejné IP adresy pomocí Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Veřejné přiřazení IP adresy pomocí plán obnovení

Veřejnou IP adresu produkční aplikace **nelze uchovávat v převzetí služeb při selhání**. Úlohy, které aktivují jako součást procesu převzetí služeb při selhání musí být přiřazena veřejná IP adresa Azure prostředek k dispozici v cílové oblasti. Tento krok lze provést buď ručně, nebo je automatické s plány obnovení. Plán obnovení shromažďuje počítače do skupin pro obnovení. Pomůže vám k definování procesu systematické obnovení. Můžete použít plán obnovení a stanovit pořadí a automatizaci akcí, třeba v každém kroku pomocí runbooků Azure Automation pro převzetí služeb při selhání do Azure nebo skriptů.

Instalační program je následujícím způsobem:
- Vytvoření [plánu obnovení](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) a seskupte úlohy podle potřeby do plánu.
- Upravit plán tak, že přidáte krok připojit a veřejné IP adresy s použitím [runbooků Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) skripty pro převzetí virtuálního počítače.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Veřejný koncový bod přímé přepnutí s směrování na úrovni DNS

Azure Traffic Manager umožňuje DNS na úrovni směrování mezi koncovými body a vám můžou pomoct s [poklesne vaše RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) pro scénář zotavení po Havárii. 

Další informace o scénáře převzetí služeb při selhání s využitím Traffic Manageru:
1. [Místní převzetí služeb při selhání Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) pomocí Traffic Manageru 
2. [Převzetí služeb při selhání Azure do Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) pomocí Traffic Manageru 

Instalační program je následujícím způsobem:
- Vytvoření [profil služby Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Využívají **Priority** směrování metodu, vytvořte dva koncové body – **primární** pro zdroj a **převzetí služeb při selhání** pro Azure. **Primární** je přiřazené Priority 1 a **převzetí služeb při selhání** je přiřazené Priority 2.
- **Primární** koncový bod může být [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) nebo [externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) v závislosti na tom, jestli je vaše zdrojové prostředí uvnitř nebo mimo Azure.
- **Převzetí služeb při selhání** jako je vytvořen koncový bod **Azure** koncového bodu. Použití **statickou veřejnou IP adresu** jak to bude externí směřující pro Traffic Manager koncový bod v události po havárii.

## <a name="next-steps"></a>Další postup
- Další informace o [Traffic Manager se službou Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Další informace o Traffic Manageru [metod směrování](../traffic-manager/traffic-manager-routing-methods.md).
- Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
