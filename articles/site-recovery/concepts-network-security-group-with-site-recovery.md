---
title: Skupiny zabezpečení sítě s obnovením webu Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak používat skupiny zabezpečení sítě s Azure Site Recovery pro zotavení po havárii a migraci
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: eb5ba99133f5726c44164b0ba45b7ab5d94e292f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292363"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Skupiny zabezpečení sítě s obnovením webu Azure

Skupiny zabezpečení sítě se používají k omezení síťového provozu na prostředky ve virtuální síti. [Skupina zabezpečení sítě (NSG)](../virtual-network/security-overview.md#network-security-groups) obsahuje seznam pravidel zabezpečení, která povolují nebo zapírají příchozí nebo odchozí síťový provoz na základě zdrojové nebo cílové adresy IP, portu a protokolu.

V rámci modelu nasazení Správce prostředků mohou být skupiny sítě nsg přidruženy k podsítím nebo jednotlivým síťovým rozhraním. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz lze dále omezit také přidružením skupiny zabezpečení sítě k jednotlivým síťovým rozhraním v rámci podsítě, která již má přidruženou skupinu zabezpečení sítě.

Tento článek popisuje, jak můžete použít skupiny zabezpečení sítě s Azure Site Recovery.

## <a name="using-network-security-groups"></a>Použití skupin zabezpečení sítě

Jednotlivé podsítě může mít nula nebo jeden, přidružené skupiny sítě sítě. Jednotlivé síťové rozhraní může mít také nula nebo jeden, přidružené nSG. Takže můžete efektivně mít duální omezení provozu pro virtuální počítač tím, že přisuzuje nsg nejprve k podsíti a pak další nsg na síťové rozhraní virtuálního počítače. Použití pravidel skupiny zabezpečení sítě v tomto případě závisí na směru provozu a prioritě použitých bezpečnostních pravidel.

Zvažte jednoduchý příklad s jedním virtuálním počítačem takto:
-    Virtuální počítač je umístěn uvnitř **podsítě Contoso**.
-    **Podsíť Contoso** je přidružena k **souboru NSG podsítě**.
-    Síťové rozhraní virtuálního zařízení je navíc přidruženo k síti **VM NSG**.

![NSG s obnovením webu](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

V tomto příkladu pro příchozí přenosy je nejprve vyhodnocena skupina nsg podsítě. Veškerý provoz povolený prostřednictvím sítě NSG podsítě je pak vyhodnocován skupinou VM NSG. Naopak je použitelná pro odchozí provoz, přičemž služba NSG virtuálních montovna je vyhodnocována jako první. Veškerý provoz povolený prostřednictvím sítě sítě VM NSG je pak vyhodnocován skupinou NSG podsítě.

To umožňuje použití granulárního pravidla zabezpečení. Můžete například povolit příchozí přístup k internetu několika virtuálním virtuálním sítím aplikace (například front-endovým virtuálním sítím) v podsíti, ale omezit příchozí přístup k internetu na jiné virtuální aplikace (například databázi a další back-endové virtuální aplikace). V takovém případě můžete mít mírnější pravidlo pro podsíť nsg, povolení internetového provozu a omezit přístup ke konkrétním virtuálním počítačům odepřením přístupu na NSG virtuálních sítí. Totéž lze použít pro odchozí provoz.

Při nastavování těchto konfigurací skupiny zabezpečení zabezpečení se ujistěte, že jsou pro [pravidla zabezpečení](../virtual-network/security-overview.md#security-rules)použity správné priority . Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.

Ne vždy musíte mít přehled o použití skupin zabezpečení sítě na síťové rozhraní i podsíť. Agregační pravidla použitá v síťovém rozhraní můžete ověřit zobrazením [účinných pravidel zabezpečení](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Můžete také použít funkci [ověření toku IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) v [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) k určení, zda je komunikace povolena do nebo ze síťového rozhraní. Tento nástroj vám řekne, jestli je povolená komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Místní replikace do Azure pomocí služby NSG

Azure Site Recovery umožňuje zotavení po havárii a migraci do Azure pro místní [virtuální počítače Hyper-V](hyper-v-azure-architecture.md), [virtuální počítače VMware](vmware-azure-architecture.md)a [fyzické servery](physical-azure-architecture.md). Pro všechny místní scénáře Azure se data replikace odesílají a ukládají v účtu Azure Storage. Během replikace neplatíte žádné poplatky za virtuální počítače. Když spustíte převzetí služeb při selhání do Azure, Site Recovery automaticky vytvoří virtuální počítače Azure IaaS.

Po vytvoření virtuálních počítačů po převzetí služeb při selhání do Azure se soubory zabezpečení sítě můžou použít k omezení síťového provozu na virtuální síť a virtuální počítače. Obnovení sítě nevytvoří sítě sítě jako součást operace převzetí služeb při selhání. Doporučujeme vytvořit požadované soubory zabezpečení Azure před zahájením převzetí služeb při selhání. Potom můžete přidružit nsgs selhání přes virtuální chod automaticky během převzetí služeb při selhání pomocí skriptů automatizace s plány obnovení webu výkonné [plány obnovení](site-recovery-create-recovery-plans.md).

Například pokud konfigurace virtuálního počítače po převzetí služeb při selhání je podobná [příkladu scénáře](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) popsaného výše:
-    Můžete vytvořit **Contoso Virtuální síť** a **Contoso podsíť** jako součást plánování zotavení po havárii v cílové oblasti Azure.
-    Můžete také vytvořit a nakonfigurovat **skupiny zabezpečení sítě Podsítě** i **skupiny NSG virtuálního** počítače jako součást stejného plánování zotavení po havárii.
-    **Skupina nsg podsítě** pak může být okamžitě přidružena k **podsíti Contoso**, protože skupina nsg i podsíť jsou již k dispozici.
-    **NSG virtuálních** montovny lze přidružit k virtuálním mandům během převzetí služeb při selhání pomocí plánů obnovení.

Po vytvoření a konfiguraci sítí nsg doporučujeme spustit [zkušební převzetí služeb při selhání](site-recovery-test-failover-to-azure.md) k ověření skriptovaných přidružení nsg a připojení virtuálního počítače po převzetí služeb při selhání.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikace Azure do Azure se sítí zabezpečení sítí a zabezpečení

Azure Site Recovery umožňuje zotavení po havárii [virtuálních počítačů Azure](azure-to-azure-architecture.md). Při povolení replikace pro virtuální počítače Azure, Site Recovery můžete vytvořit virtuální sítě repliky (včetně podsítí a podsítí brány) v cílové oblasti a vytvořit požadovaná mapování mezi zdrojové a cílové virtuální sítě. Můžete také předem vytvořit cílové boční sítě a podsítě a použít stejné při povolení replikace. Site Recovery nevytvoří žádné virtuální počítače v cílové oblasti Azure před [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md).

Pro replikaci virtuálních počítačů Azure se ujistěte, že pravidla nsg ve zdrojové oblasti Azure umožňují [odchozí připojení](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) pro provoz replikace. Tato požadovaná pravidla můžete také otestovat a ověřit prostřednictvím této [ukázkové konfigurace souboru NSG](azure-to-azure-about-networking.md#example-nsg-configuration).

Obnovení sítě nevytváří ani replikuje sítě sítě jako součást operace převzetí služeb při selhání. Doporučujeme vytvořit požadované skupiny zabezpečení zabezpečení v cílové oblasti Azure před zahájením převzetí služeb při selhání. Potom můžete přidružit nsgs selhání přes virtuální chod automaticky během převzetí služeb při selhání pomocí skriptů automatizace s plány obnovení webu výkonné [plány obnovení](site-recovery-create-recovery-plans.md).

Vzhledem k [příkladu scénář](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) popsaného dříve:
-    Site Recovery můžete vytvořit repliky **Contoso Virtuální síť** a **Contoso podsítě** v cílové oblasti Azure, když je povolena replikace pro virtuální počítač.
-    Můžete vytvořit požadované repliky **Podsítě NSG** a **NSG VM** (pojmenované například **Cílová podsíť NSG** a **Cílový soubor zabezpečení sítě VSG**) v cílové oblasti Azure, což umožňuje další pravidla požadovaná v cílové oblasti.
-    **Cílová skupina sítě nsg** pak může být okamžitě přidružena k podsíti cílové oblasti, protože skupina nsg i podsíť jsou již k dispozici.
-    **Cílová skupina nsg virtuálních** montovny může být přidružena k virtuálním mům během převzetí služeb při selhání pomocí plánů obnovení.

Po vytvoření a konfiguraci sítí nsg doporučujeme spustit [zkušební převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md) k ověření skriptovaných přidružení nsg a připojení virtuálního počítače po převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky
-    Další informace o [skupinách zabezpečení sítě](../virtual-network/security-overview.md#network-security-groups).
-    Další informace o [pravidlech zabezpečení](../virtual-network/security-overview.md#security-rules)skupiny zabezpečení skupiny zabezpečení .
-    Přečtěte si další informace o [účinných pravidlech zabezpečení](../virtual-network/diagnose-network-traffic-filter-problem.md) pro skupinu zabezpečení zabezpečení.
-    Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md) pro automatizaci převzetí služeb při selhání aplikací.
