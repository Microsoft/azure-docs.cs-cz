---
title: Přiřazení veřejných IP adres po převzetí služeb při selhání pomocí Azure Site Recovery
description: Popisuje, jak nastavit veřejné IP adresy pomocí Azure Site Recovery a Azure Traffic Manager pro zotavení po havárii a migraci
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281948"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Nastavení veřejných IP adres po převzetí služeb při selhání

Veřejné IP adresy umožňují internetovým prostředkům příchozí komunikaci s prostředky Azure. Veřejné IP adresy taky umožňují prostředkům Azure odchozí komunikaci s internetovými službami a veřejně přístupnými službami Azure prostřednictvím IP adresy přiřazené prostředku.
- Příchozí komunikace z Internetu do prostředku, jako jsou virtuální počítače Azure (VM), aplikační brány Azure, nástroje pro vyrovnávání zatížení Azure, brány Azure VPN a další. Stále můžete komunikovat s některými prostředky, jako jsou virtuální společnosti, z Internetu, pokud virtuální hod nemá přiřazenou veřejnou IP adresu, tak dlouho, dokud je virtuální hod součástí back-endového fondu vyrovnávání zatížení a vyvykladaču zatížení je přiřazena veřejná IP adresa.
- Odchozí připojení k Internetu pomocí předvídatelné ip adresy. Virtuální počítač může například komunikovat odchozí do Internetu bez přiřazené veřejné IP adresy, ale jeho adresa je síťová adresa přeložená Azure na nepředvídatelnou veřejnou adresu ve výchozím nastavení. Přiřazení veřejné IP adresy prostředku umožňuje zjistit, která adresa IP se používá pro odchozí připojení. Ačkoli předvídatelné, adresa se může změnit, v závislosti na zvolené metodě přiřazení. Další informace naleznete [v tématu Vytvoření veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Další informace o odchozích připojeních z prostředků Azure najdete v [tématu Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Ve Správci prostředků Azure je veřejná IP adresa prostředek, který má své vlastní vlastnosti. Mezi prostředky, ke kterým můžete přiřadit prostředek s veřejnou IP adresou, patří:

* Síťová rozhraní virtuálního počítače
* Internetové nástroje pro vyrovnávání zatížení
* VPN Gateway
* Application Gateway

Tento článek popisuje, jak můžete použít veřejné IP adresy s site recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Přiřazení veřejné IP adresy pomocí plánu obnovení

Veřejnou IP adresu produkční aplikace **nelze uchovat při převzetí služeb při selhání**. Úlohy svázané jako součást procesu převzetí služeb při selhání musí být přiřazeny prostředek veřejné IP Azure, který je k dispozici v cílové oblasti. Tento krok lze provést ručně nebo je automatizován s plány obnovení. Plán obnovení shromažďuje počítače do skupin obnovení. Pomáhá definovat systematický proces obnovy. Pomocí plánu obnovení můžete vytvořit pořadí a automatizovat akce potřebné v každém kroku pomocí runbooků Azure Automation pro převzetí služeb při selhání do Azure nebo skripty.

Nastavení je následující:
- Vytvořte [plán obnovení](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) a seskupte své úlohy podle potřeby do plánu.
- Přizpůsobte plán přidáním kroku pro připojení veřejné IP adresy pomocí skriptů [runbooků Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) k virtuálnímu počítači, který se nezdařil.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Přepínání veřejných koncových bodů pomocí směrování na úrovni DNS

Azure Traffic Manager umožňuje směrování na úrovni DNS mezi koncovými body a může pomoci s [omezením rtopro](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) pro scénář zotavení po havárii. 

Přečtěte si další informace o scénářích převzetí služeb při selhání pomocí Traffic Manageru:
1. [Místní převzetí služeb při selhání azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) pomocí Traffic Manageru 
2. [Převzetí služeb při selhání Azure s](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) Traffic Managerem 

Nastavení je následující:
- Vytvořte [profil traffic manageru](../traffic-manager/traffic-manager-create-profile.md).
- S využitím metody směrování **Priority** vytvořte dva koncové body – **primární** pro zdroj a **převzetí služeb při selhání** pro Azure. **Primární** je přiřazena priorita 1 a **převzetí služeb při selhání** je přiřazena priorita 2.
- **Primární** koncový bod může být [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) nebo [Externí](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) v závislosti na tom, zda je vaše zdrojové prostředí uvnitř nebo vně Azure.
- Koncový bod **převzetí služeb při selhání** se vytvoří jako koncový bod **Azure.** Použijte **statickou veřejnou IP adresu,** protože to bude externí koncový bod pro Traffic Manager v případě katastrofy.

## <a name="next-steps"></a>Další kroky
- Další informace o [Traffic Manageru pomocí Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Další informace o [metodách směrování](../traffic-manager/traffic-manager-routing-methods.md)traffic manageru .
- Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikací.
