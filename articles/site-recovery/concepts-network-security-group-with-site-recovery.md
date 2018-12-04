---
title: Skupiny zabezpečení s využitím Azure Site Recovery sítě | Dokumentace Microsoftu
description: Popisuje, jak pomocí skupin zabezpečení sítě pomocí Azure Site Recovery pro zotavení po havárii a migrace
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 4885be7547d404505b50c563036f260166cbc2cc
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833818"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Skupiny zabezpečení sítě pomocí Azure Site Recovery

Skupiny zabezpečení sítě umožňují omezení síťového provozu směřujícího do prostředků ve virtuální síti. A [skupina zabezpečení sítě (NSG)](../virtual-network/security-overview.md#network-security-groups) obsahuje seznam pravidel zabezpečení, která povolují nebo odpírají příchozí nebo odchozí síťový provoz podle zdrojové nebo cílové IP adresy, portu a protokolu.

V rámci modelu nasazení Resource Manageru skupiny Nsg můžou být přidružené k podsítím nebo jednotlivým síťovým rozhraním. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz se dá dále omezit přidružením skupiny NSG k jednotlivým síťovým rozhraním v rámci podsítě, který už má přidruženou skupinu NSG.

Tento článek popisuje, jak můžete pomocí skupin zabezpečení sítě pomocí Azure Site Recovery.

## <a name="using-network-security-groups"></a>Pomocí skupin zabezpečení sítě

Jednotlivé podsíť může mít jednu, nebo žádnou přidružené skupiny zabezpečení sítě. Jednotlivých síťových rozhraní může mít také jednu, nebo žádnou přidružené skupiny zabezpečení sítě. Ano můžete efektivně máte omezení duální provozu pro virtuální počítač tím, že přidružíte skupinu NSG k podsíti první a pak další skupiny zabezpečení sítě k síťovému rozhraní Virtuálního počítače. Použití pravidel NSG v tomto případě závisí na směr provozu a priority pravidel zabezpečení.

Vezměte v úvahu jednoduchý příklad s jeden virtuální počítač následujícím způsobem:
-   Virtuální počítač je umístěn uvnitř **Contoso podsítě**.
-   **Podsíť contoso** je přidružený **skupina NSG podsítě**.
-   Síťové rozhraní virtuálního počítače je také přidružen **skupiny zabezpečení sítě virtuálních počítačů**.

![Skupina zabezpečení sítě pomocí služby Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

V tomto příkladu, pro příchozí provoz skupina NSG podsítě je vyhodnocen jako první. Veškerý provoz povolený průchod přes skupina NSG podsítě se pak vyhodnocuje podle skupiny zabezpečení sítě virtuálních počítačů. Naopak platí pro odchozí provoz pomocí skupiny zabezpečení sítě virtuálního počítače je vyhodnocen jako první. Veškerý provoz povolený průchod přes skupiny zabezpečení sítě virtuálního počítače se pak vyhodnocuje podle NSG podsítě.

To umožňuje aplikaci pravidlo detailní zabezpečení. Například můžete chtít povolit příchozí internetový přístup k několika virtuálních počítačů aplikace (jako je například front-endové virtuální počítače) v rámci podsítě však omezit příchozí přístup k Internetu pro ostatní virtuální počítače (například databáze a jiných back-endový virtuální počítače). V tomto případě můžete mít mírnější pravidla NSG podsítě, což přenosy z Internetu a omezit přístup na konkrétní virtuální počítače tak, že odepření přístupu na skupiny zabezpečení sítě virtuálních počítačů. Stejné lze použít pro odchozí provoz.

Při nastavování takové konfigurace skupiny zabezpečení sítě se ujistit, že správné priority jsou [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules). Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.

Ne vždy musíte mít přehled o použití skupin zabezpečení sítě na síťové rozhraní i podsíť. Agregovaná pravidla použitá na síťové rozhraní zobrazením můžete ověřit, [platná pravidla zabezpečení](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Můžete také použít [ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) možnosti [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) k určení, jestli je povolená komunikace do nebo z síťové rozhraní. Tento nástroj vám řekne, jestli je povolená komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Místní replikace se skupina zabezpečení sítě Azure

Azure Site Recovery nabízí zotavení po havárii a migraci do Azure pro místní [virtuálních počítačů Hyper-V](hyper-v-azure-architecture.md), [virtuálních počítačů VMware](vmware-azure-architecture.md), a [fyzických serverů](physical-azure-architecture.md). Data replikace pro všechny místní do scénáře služby Azure, se odesílají a ukládají v účtu služby Azure Storage. Během replikace neplatíte žádné poplatky za virtuální počítače. Při spuštění převzetí služeb při selhání do Azure Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Po vytvoření virtuálních počítačů po převzetí služeb při selhání do Azure skupin zabezpečení sítě je možné omezit síťový provoz do virtuální sítě a virtuální počítače. Site Recovery nevytváří skupiny zabezpečení sítě jako součást operace převzetí služeb při selhání. Doporučujeme vytvořit požadované NGSs Azure před inicializací převzetí služeb při selhání. Potom můžete spojit skupiny Nsg k převzetí služeb při selhání virtuálních počítačů automaticky během převzetí služeb při selhání, skripty pro automatizaci pomocí Site Recovery pomocí výkonných [plány obnovení](site-recovery-create-recovery-plans.md).

Například, pokud je podobný konfigurace post-převzetí služeb při selhání virtuálního počítače [ukázkový scénář](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) podrobné výše:
-   Můžete vytvořit **virtuální síti Contoso** a **Contoso podsítě** jako součást plánování v cílové oblasti Azure zotavení po Havárii.
-   Můžete také vytvořit a nakonfigurovat i **skupina NSG podsítě** stejně jako **skupiny zabezpečení sítě virtuálních počítačů** jako plánování části stejné zotavení po Havárii.
-   **Skupina NSG podsítě** je pak možné okamžitě přidružit **Contoso podsítě**, jak skupina zabezpečení sítě a podsítě jsou už k dispozici.
-   **Skupina zabezpečení sítě virtuálních počítačů** můžou být spojené s virtuálními počítači během převzetí služeb při selhání pomocí plánů obnovení.

Po vytvoření skupiny zabezpečení sítě a konfiguraci, doporučujeme vám běží [testovací převzetí služeb při selhání](site-recovery-test-failover-to-azure.md) ověření skripty přidružení skupiny zabezpečení sítě a připojení post-převzetí služeb při selhání virtuálních počítačů.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikace z Azure do Azure pomocí skupiny zabezpečení sítě

Azure Site Recovery nabízí zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). Při povolení replikace pro virtuální počítače Azure, Site Recovery vytvořit repliku virtuálních sítí (včetně podsítí a podsítě brány) v cílové oblasti a požadovaná mapování mezi zdroji a cílové virtuální sítě. Také můžete předem vytvořit cíl na straně sítě a podsítě a použít stejné při povolení replikace. Site Recovery nevytvoří všechny virtuální počítače před cílové oblasti Azure [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md).

Pro replikaci virtuálních počítačů Azure, ujistěte se, že pravidla NSG na zdrojovou oblast Azure umožňují [odchozí připojení](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) pro přenosy replikace. Můžete také otestovat a ověřit tato požadovaná pravidla v něm [Ukázková konfigurace NSG](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery nepodporuje ani replikovat skupin zabezpečení sítě jako součást operace převzetí služeb při selhání. Doporučujeme vytvořit požadované NGSs na cílovou oblastí Azure před inicializací převzetí služeb při selhání. Potom můžete spojit skupiny Nsg k převzetí služeb při selhání virtuálních počítačů automaticky během převzetí služeb při selhání, skripty pro automatizaci pomocí Site Recovery pomocí výkonných [plány obnovení](site-recovery-create-recovery-plans.md).

Vzhledem k tomu, [ukázkový scénář](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) popsané výše:
-   Site Recovery můžete vytvořit repliky **virtuální síti Contoso** a **Contoso podsítě** na cílovou oblastí Azure po povolení replikace pro virtuální počítač.
-   Můžete vytvořit požadovanou repliky **skupina NSG podsítě** a **skupiny zabezpečení sítě virtuálních počítačů** (s názvem, například **cílové podsítě NSG** a **NSG cílový virtuální počítač**, v tomto pořadí) v cílové oblasti Azure, umožňuje veškerá další pravidla, která vyžaduje v cílové oblasti.
-   **Cílová skupina NSG podsítě** je pak možné okamžitě spojené s cílovou podsíť oblasti, skupiny zabezpečení sítě a podsítě jsou už k dispozici.
-   **Cílová skupina zabezpečení sítě virtuálních počítačů** můžou být spojené s virtuálními počítači během převzetí služeb při selhání pomocí plánů obnovení.

Po vytvoření skupiny zabezpečení sítě a konfiguraci, doporučujeme vám běží [testovací převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md) ověření skripty přidružení skupiny zabezpečení sítě a připojení post-převzetí služeb při selhání virtuálních počítačů.

## <a name="next-steps"></a>Další postup
-   Další informace o [skupiny zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups).
-   Další informace o NSG [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules).
-   Další informace o [platná pravidla zabezpečení](../virtual-network/diagnose-network-traffic-filter-problem.md) pro skupinu zabezpečení sítě.
-   Další informace o [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
