---
title: Sítě skupiny zabezpečení s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak používat skupiny zabezpečení sítě s Azure Site Recovery pro zotavení po havárii a migrace
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/21/2018
ms.author: manayar
ms.openlocfilehash: 2b35578e2dc49cd47689f62fc47c5341ea8767a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655002"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Skupiny zabezpečení sítě s Azure Site Recovery

Omezit síťový provoz na prostředky ve virtuální síti se používají skupiny zabezpečení sítě. A [skupina zabezpečení sítě (NSG)](../virtual-network/security-overview.md#network-security-groups) obsahuje seznam pravidel zabezpečení, která povolují nebo odepírají příchozí nebo odchozí síťový provoz na základě zdrojové nebo cílové IP adresy, portu a protokolu.

V rámci modelu nasazení Resource Manager skupiny Nsg můžou být přidružena k podsítě nebo jednotlivých síťových rozhraní. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz se dá dále omezit tím, že také přidružíte skupinu NSG k jednotlivých síťových rozhraní v rámci jedné podsítě, který už má přidružené skupiny NSG.

Tento článek popisuje, jak můžete používat skupiny zabezpečení sítě s Azure Site Recovery.

## <a name="using-network-security-groups"></a>Pomocí skupin zabezpečení sítě

Jednotlivé podsítě může mít nula nebo jedna, přidružené NSG. Jednotlivá síťová rozhraní může mít nula nebo jedna, přidružené NSG. Ano můžete efektivně mít omezení duální provozu pro virtuální počítač tím, že přidružíte skupinu NSG nejprve k podsíti a pak další skupiny NSG k síťové rozhraní Virtuálního počítače. Použití pravidla NSG v tomto případě závisí na provoz a priority pravidel použité zabezpečení.

Zvažte jednoduchý příklad s jeden virtuální počítač následujícím způsobem:
-   Virtuální počítač je umístěn uvnitř **Contoso podsítě**.
-   **Podsíť contoso** přidružen **podsíť NSG**.
-   Síťové rozhraní virtuálního počítače je kromě přidružen **NSG virtuálních počítačů**.

![Skupina NSG službou Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

V tomto příkladu se pro příchozí provoz NSG pro podsíť se vyhodnocují jako první. Přenosy dat přes podsíť NSG povolená je pak vyhodnocován NSG virtuálních počítačů. Naopak je použít pro odchozí přenosy, s první vyhodnocovaný NSG virtuálních počítačů. Přenosy dat přes virtuální počítač NSG povolená je vyhodnocován pak NSG na podsítě.

To umožňuje granulární zabezpečení pravidla aplikace. Například můžete chtít povolit příchozí přístup k Internetu na pár aplikací virtuálních počítačů (například front-endu virtuálních počítačů) v rámci podsítě však omezit příchozí přístup k Internetu na ostatních virtuálních počítačů (například databáze a jiných back-end virtuálních počítačů). V takovém případě můžete mít mírnější pravidlo na skupinu NSG podsíť, povolení přenosy z Internetu a omezit přístup pro konkrétní virtuální počítače tak, že odepření přístupu na NSG virtuálních počítačů. Stejné můžete použít pro odchozí provoz.

Při nastavování konfigurace taková skupina NSG, zajistíte, že jsou správná priority použije k [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules). Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.

Ne vždy musíte mít přehled o použití skupin zabezpečení sítě na síťové rozhraní i podsíť. Můžete ověřit agregační pravidla použije k síťovému rozhraní zobrazením [pravidla efektivní zabezpečení](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Můžete také [IP tok ověření](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funkci [sledovací proces sítě Azure](../network-watcher/network-watcher-monitoring-overview.md) k určení, zda je komunikace povolena do nebo z rozhraní sítě. Tento nástroj vám řekne, jestli je povolená komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Místním nasazením a Azure replikace s NSG

Azure Site Recovery umožňuje zotavení po havárii a migrace do Azure pro místní [virtuální počítače Hyper-V](hyper-v-azure-architecture.md), [virtuálních počítačů VMware](vmware-azure-architecture.md), a [fyzických serverů](physical-azure-architecture.md). Data replikace je pro všechny místní Azure scénářů, odesílají a ukládají v účtu Azure Storage. Během replikace nemusíte platit žádné poplatky za virtuální počítače. Při spuštění převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Po vytvoření virtuálních počítačů po převzetí služeb při selhání do Azure skupiny Nsg slouží k omezení síťového provozu pro virtuální síť a virtuální počítače. Site Recovery nevytváří skupin Nsg v rámci operace převzetí služeb při selhání. Doporučujeme vytvořit požadované NGSs Azure před zahájením převzetí služeb při selhání. Potom můžete spojit skupiny Nsg k převzetí služeb při selhání virtuální počítače automaticky během převzetí služeb při selhání, skripty pro automatizaci pomocí Site Recovery pomocí výkonné [plány obnovení](site-recovery-create-recovery-plans.md).

Například, pokud je podobná konfigurace post-převzetí služeb při selhání virtuálního počítače [ukázkový scénář](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) podrobné výše:
-   Můžete vytvořit **virtuální síť společnosti Contoso** a **Contoso podsíť** v rámci zotavení po Havárii plánování v cílové oblasti Azure.
-   Můžete také vytvořit a nakonfigurovat i **podsíť NSG** a také **virtuálních počítačů NSG** jako část stejné DR plánování.
-   **Podsíť NSG** pak dají okamžitě přidružit **Contoso podsítě**, jak NSG a podsítě jsou již k dispozici.
-   **Virtuální počítač NSG** během převzetí služeb při selhání pomocí plánů obnovení může být spojen s virtuálními počítači.

Jakmile skupin Nsg vytvoříte a nakonfigurujete, doporučujeme spuštěná [testovací převzetí služeb při selhání](site-recovery-test-failover-to-azure.md) ověření skriptování přidružení skupiny NSG a post-převzetí služeb při selhání virtuálního počítače připojení.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikace Azure do Azure s NSG

Azure Site Recovery umožňuje zotavení po havárii [virtuální počítače Azure](azure-to-azure-architecture.md). Při povolení replikace pro virtuální počítače Azure, můžete Site Recovery vytvořte repliku virtuálních sítí (včetně podsítě a podsítě brány) v oblasti cíl a vytvořit požadované mapování mezi zdroji a cílové virtuální sítě. Můžete také předem vytvořit cíl straně sítě a podsítě a použít stejné při povolení replikace. Obnovení lokality nevytvoří žádné virtuální počítače v oblasti Azure před cíl [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md).

Pro replikaci virtuálního počítače Azure, zkontrolujte, jestli tato pravidla NSG na zdroji oblast Azure umožňují [odchozí připojení](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) pro přenosy replikace. Můžete také testovat a ověřovat přes tento tato požadovaná pravidla [příklad NSG konfigurace](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery nevytváří ani replikovat skupin Nsg v rámci operace převzetí služeb při selhání. Doporučujeme vytvořit požadované NGSs na cíli oblast Azure před zahájením převzetí služeb při selhání. Potom můžete spojit skupiny Nsg k převzetí služeb při selhání virtuální počítače automaticky během převzetí služeb při selhání, skripty pro automatizaci pomocí Site Recovery pomocí výkonné [plány obnovení](site-recovery-create-recovery-plans.md).

Vzhledem k tomu [ukázkový scénář](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) popsané výše:
-   Site Recovery můžete vytvořit repliky **virtuální síť společnosti Contoso** a **Contoso podsíť** na cíli oblast Azure, když je povolená replikace pro virtuální počítač.
-   Můžete vytvořit požadované repliky **podsíť NSG** a **NSG virtuálních počítačů** (s názvem, například **cílové podsíti NSG** a **cílový počítač NSG**, v uvedeném pořadí) na cílovém oblast Azure, aby vám umožnil žádná další pravidla na cílová oblast vyžaduje.
-   **Cílové skupiny NSG pro podsíť** pak může být okamžitě spojenou s podsítí oblast cíl, jako skupina NSG a podsíť jsou již k dispozici.
-   **Cílové skupiny NSG virtuálních počítačů** během převzetí služeb při selhání pomocí plánů obnovení může být spojen s virtuálními počítači.

Jakmile skupin Nsg vytvoříte a nakonfigurujete, doporučujeme spuštěná [testovací převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md) ověření skriptování přidružení skupiny NSG a post-převzetí služeb při selhání virtuálního počítače připojení.

## <a name="next-steps"></a>Další postup
-   Další informace o [skupin zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups).
-   Další informace o NSG [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules).
-   Další informace o [pravidla efektivní zabezpečení](../virtual-network/diagnose-network-traffic-filter-problem.md) pro skupinu NSG.
-   Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
