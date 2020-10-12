---
title: Skupiny zabezpečení sítě s Azure Site Recovery | Microsoft Docs
description: Popisuje, jak používat skupiny zabezpečení sítě s Azure Site Recovery pro zotavení po havárii a pro migraci.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 904bc63ed2a135cdcadad75e96acd6fe3ca39039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069675"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Skupiny zabezpečení sítě se službou Azure Site Recovery

Skupiny zabezpečení sítě se používají k omezení síťového provozu do prostředků ve virtuální síti. [Skupina zabezpečení sítě (NSG)](../virtual-network/security-overview.md#network-security-groups) obsahuje seznam pravidel zabezpečení, která povolují nebo zakazují příchozí nebo odchozí síťový provoz na základě zdrojové nebo cílové IP adresy, portu a protokolu.

V rámci modelu nasazení Správce prostředků lze skupin zabezpečení sítě přidružit k podsítím nebo jednotlivým síťovým rozhraním. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz se může dál omezit taky tím, že přidruží NSG k jednotlivým síťovým rozhraním v rámci podsítě, která už má přidružené NSG.

Tento článek popisuje, jak můžete používat skupiny zabezpečení sítě s Azure Site Recovery.

## <a name="using-network-security-groups"></a>Použití skupin zabezpečení sítě

Jednotlivé podsítě mohou mít nula nebo jednu přidruženou NSG. Každé síťové rozhraní může mít také nula nebo jednu přidruženou NSG. To znamená, že pro virtuální počítač můžete efektivně přidružit duální omezení přenosů NSG a pak další NSG k síťovému rozhraní virtuálního počítače. Použití pravidel NSG v tomto případě závisí na směru přenosů a prioritě použitých pravidel zabezpečení.

Jedním z nich je jednoduchý příklad s jedním virtuálním počítačem, a to takto:
-    Virtuální počítač se umístí do **podsítě contoso**.
-    **Podsíť contoso** je přidružená k **podsíti NSG**.
-    Síťové rozhraní virtuálního počítače je navíc přidruženo k **NSG virtuálních počítačů**.

![NSG s Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

V tomto příkladu je pro příchozí provoz vyhodnocena podsíť NSG jako první. Veškerý provoz povolený prostřednictvím podsítě NSG se pak vyhodnocuje pomocí NSG virtuálního počítače. Tato změna se týká odchozího provozu, přičemž NSG virtuálního počítače se vyhodnocuje jako první. Veškerý provoz povolený prostřednictvím NSG virtuálního počítače se pak vyhodnotí v rámci podsítě NSG.

To umožňuje aplikaci pro podrobné pravidlo zabezpečení. Například můžete chtít povolit příchozí internetový přístup k několika virtuálním počítačům aplikace (jako jsou front-endové virtuální počítače) v podsíti, ale omezit příchozí internetový přístup k jiným virtuálním počítačům (například k databázím a dalším back-end virtuálním počítačům). V takovém případě můžete mít další mírné pravidlo pro NSG, povolení internetového provozu a omezení přístupu ke konkrétním virtuálním počítačům tím, že odepřete přístup k virtuálnímu počítači NSG. Totéž lze použít pro odchozí provoz.

Při nastavování takových konfigurací NSG zajistěte, aby byly na [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules)aplikovány správné priority. Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.

Ne vždy musíte mít přehled o použití skupin zabezpečení sítě na síťové rozhraní i podsíť. Můžete ověřit agregovaná pravidla použitá pro síťové rozhraní zobrazením [platných pravidel zabezpečení](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Funkci [ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) v [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) taky můžete použít k určení, jestli je komunikace povolená pro nebo ze síťového rozhraní. Tento nástroj vám řekne, jestli je povolená komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Replikace z místního prostředí do Azure pomocí NSG

Azure Site Recovery umožňuje zotavení po havárii a migraci do Azure pro místní [virtuální počítače Hyper-V](hyper-v-azure-architecture.md), [virtuální počítače VMware](vmware-azure-architecture.md)a [fyzické servery](physical-azure-architecture.md). Pro všechny místní scénáře do Azure se data replikace odesílají do účtu Azure Storage a ukládají se do něj. Během replikace neplatíte žádné poplatky za virtuální počítače. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure s IaaS.

Po vytvoření virtuálních počítačů po převzetí služeb při selhání do Azure se dá skupin zabezpečení sítě použít k omezení síťového provozu do virtuální sítě a virtuálních počítačů. Site Recovery nevytváří skupin zabezpečení sítě jako součást operace převzetí služeb při selhání. Před zahájením převzetí služeb při selhání doporučujeme vytvořit skupin zabezpečení sítě Azure. Pak můžete přidružit skupin zabezpečení sítě k virtuálním počítačům při převzetí služeb při selhání automaticky během převzetí služeb při selhání pomocí skriptů pro automatizaci Site Recovery výkonných [plánů obnovení](site-recovery-create-recovery-plans.md).

Například pokud je konfigurace virtuálního počítače po převzetí služeb při selhání podobná [ukázkovému scénáři](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) popsanému výše:
-    V rámci plánování zotavení po havárii v cílové oblasti Azure můžete vytvořit **virtuální síť contoso** a **podsíť contoso** .
-    V rámci stejného plánování DR můžete také vytvořit a nakonfigurovat obě **NSG podsítě** i **NSG virtuálního počítače** .
-    **Podsíť NSG** se pak dá hned přidružit k **podsíti contoso**, protože NSG i podsíť jsou už dostupné.
-    **NSG virtuálních počítačů** je možné přidružit k virtuálním počítačům během převzetí služeb při selhání pomocí plánů obnovení.

Po vytvoření a konfiguraci skupin zabezpečení sítě doporučujeme spuštění [testovacího převzetí služeb při selhání](site-recovery-test-failover-to-azure.md) za účelem ověření provedených NSG přidružení a připojení virtuálního počítače po převzetí služeb při selhání.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikace z Azure do Azure pomocí NSG

Azure Site Recovery umožňuje zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). Když povolíte replikaci pro virtuální počítače Azure, Site Recovery může vytvořit virtuální sítě repliky (včetně podsítí a podsítí brány) v cílové oblasti a vytvořit požadovaná mapování mezi zdrojovou a cílovou virtuální sítí. Můžete také předem vytvořit cílové sítě a podsítě a při povolování replikace použít stejné. Site Recovery nevytváří žádné virtuální počítače v cílové oblasti Azure před [převzetím služeb při selhání](azure-to-azure-tutorial-failover-failback.md).

U replikace virtuálních počítačů Azure zajistěte, aby pravidla NSG ve zdrojové oblasti Azure umožňovala [odchozí připojení](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) pro provoz replikace. Pomocí této [ukázkové NSG konfigurace](azure-to-azure-about-networking.md#example-nsg-configuration)můžete také otestovat a ověřit tato požadovaná pravidla.

Site Recovery nevytváří ani nereplikuje skupin zabezpečení sítě jako součást operace převzetí služeb při selhání. Před spuštěním převzetí služeb při selhání doporučujeme vytvořit požadovanou skupin zabezpečení sítě v cílové oblasti Azure. Pak můžete přidružit skupin zabezpečení sítě k virtuálním počítačům při převzetí služeb při selhání automaticky během převzetí služeb při selhání pomocí skriptů pro automatizaci Site Recovery výkonných [plánů obnovení](site-recovery-create-recovery-plans.md).

Zvážení [ukázkového scénáře](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) popsaného výše:
-    Site Recovery může vytvořit repliky **virtuální** **sítě a podsítě contoso** v cílové oblasti Azure, pokud je pro tento virtuální počítač povolená replikace.
-    V cílové oblasti Azure můžete vytvořit požadované repliky **podsítě NSG** a **VM NSG** (s názvem, například **cílová podsíť NSG** a **cílový virtuální počítač NSG**, v uvedeném pořadí) a povolit tak všechna další pravidla požadovaná v cílové oblasti.
-    **Cílová podsíť NSG** se pak dá hned přidružit k podsíti cílové oblasti, protože NSG i podsíť jsou už dostupné.
-    **Cílový virtuální počítač NSG** může být přidružen k virtuálním počítačům během převzetí služeb při selhání pomocí plánů obnovení.

Po vytvoření a konfiguraci skupin zabezpečení sítě doporučujeme spuštění [testovacího převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md) za účelem ověření provedených NSG přidružení a připojení virtuálního počítače po převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
-    Přečtěte si další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups).
-    Přečtěte si další informace o [pravidlech zabezpečení](../virtual-network/security-overview.md#security-rules)NSG.
-    Přečtěte si další informace o [platných pravidlech zabezpečení](../virtual-network/diagnose-network-traffic-filter-problem.md) pro NSG.
-    Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikace.
