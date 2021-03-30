---
title: Přiřazení veřejných IP adres po převzetí služeb při selhání pomocí Azure Site Recovery
description: Popisuje, jak nastavit veřejné IP adresy pomocí Azure Site Recovery a Azure Traffic Manager pro zotavení po havárii a migraci.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86130285"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Nastavení veřejných IP adres po převzetí služeb při selhání

Veřejné IP adresy umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure odchozí komunikaci s internetovými službami a veřejně přístupnými službami Azure prostřednictvím IP adresy přiřazené prostředku.
- Příchozí komunikace z Internetu k prostředku, jako je například Azure Virtual Machines (VM), služby Azure Application Gateway, služby Azure Load Balancer, brány Azure VPN a další. I nadále můžete komunikovat s některými prostředky, jako jsou virtuální počítače, z Internetu, pokud k virtuálnímu počítači není přiřazená veřejná IP adresa, pokud je tento virtuální počítač součástí fondu back-end nástroje pro vyrovnávání zatížení a nástroj pro vyrovnávání zatížení má přiřazenou veřejnou IP adresu.
- Odchozí připojení k Internetu pomocí předvídatelné IP adresy. Virtuální počítač může například komunikovat odchozí na Internet bez přiřazené veřejné IP adresy, ale jeho adresa je síťová adresa přeložená službou Azure na nepředvídatelné veřejné adresy, ve výchozím nastavení. Přiřazení veřejné IP adresy prostředku vám umožní zjistit, která IP adresa se používá pro odchozí připojení. I když je předvídatelné, adresa se může změnit v závislosti na zvolené metodě přiřazení. Další informace najdete v tématu [Vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Další informace o odchozích připojeních z prostředků Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

V Azure Resource Manager je veřejná IP adresa prostředkem, který má své vlastní vlastnosti. Mezi prostředky, ke kterým můžete přiřadit prostředek s veřejnou IP adresou, patří:

* síťová rozhraní virtuálních počítačů,
* Internetové nástroje pro vyrovnávání zatížení
* VPN Gateway
* brány Application Gateway.

Tento článek popisuje, jak můžete použít veřejné IP adresy s Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Přiřazení veřejné IP adresy pomocí plánu obnovení

Veřejnou IP adresu produkční aplikace **nejde zachovat při převzetí služeb při selhání**. Úlohy, které se zadají jako součást procesu převzetí služeb při selhání, musí mít přiřazený prostředek veřejné IP adresy Azure dostupný v cílové oblasti. Tento krok lze provést buď ručně, nebo automaticky pomocí plánů obnovení. Plán obnovení shromažďuje počítače do skupin pro obnovení. Pomáhá definovat proces systematického obnovení. Plán obnovení můžete použít k vytvoření objednávky a automatizaci akcí potřebných v každém kroku, a to pomocí Azure Automation Runbooky pro převzetí služeb při selhání do Azure nebo skriptů.

Nastavení je následující:
- Vytvořte [plán obnovení](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) a podle potřeby seskupte své úlohy do plánu.
- Přizpůsobte si plán přidáním kroku a připojením veřejné IP adresy pomocí [Azure Automationch runbooků](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) skripty k virtuálnímu počítači, u kterého došlo k převzetí služeb při selhání.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Přepínání veřejného koncového bodu s směrováním na úrovni DNS

Azure Traffic Manager umožňuje směrování na úrovni DNS mezi koncovými body a může pomoct s tím, že se [RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) pro případ zotavení po havárii. 

Přečtěte si víc o scénářích převzetí služeb při selhání pomocí Traffic Manager:
1. [Převzetí služeb při selhání z místního prostředí do Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) pomocí Traffic Manager 
2. [Převzetí služeb při selhání Azure do Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) pomocí Traffic Manager 

Nastavení je následující:
- Vytvořte [profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- S využitím metody směrování **priority** vytvořte dva koncové body – **primární** pro zdroj a **převzetí služeb při selhání** pro Azure. K **primární** je přiřazena priorita 1 a pro **převzetí služeb při selhání** je přiřazena Priorita 2.
- **Primární** koncový bod může být [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) nebo [externě](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) v závislosti na tom, jestli je vaše zdrojové prostředí v Azure nebo mimo něj.
- Koncový bod **převzetí služeb při selhání** se vytvoří jako koncový bod **Azure** . Použijte **statickou veřejnou IP adresu** , protože se jedná o vnější koncový bod pro Traffic Manager v události po havárii.

## <a name="next-steps"></a>Další kroky
- Další informace o [Traffic Manager s Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Přečtěte si další informace o Traffic Manager [metod směrování](../traffic-manager/traffic-manager-routing-methods.md).
- Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikace.
