---
title: Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů VMware a fyzických serverů během zotavení po havárii do Azure pomocí Site Recovery | Microsoft Docs
description: Zjistěte, jak během zotavení po havárii do Azure pomocí Azure Site Recovery provést převzetí služeb při selhání virtuálních počítačů VMware a fyzických serverů do Azure a navrácení služeb po obnovení do místní lokality.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797661"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů VMware

Tento článek popisuje, jak převzít služby při selhání VMware v místním prostředí virtuálního počítače do Azure [Azure Site Recovery](site-recovery-overview.md) služby. 

Toto je pátý dílem série, která ukazuje, jak nastavit zotavení po havárii do Azure pro místní počítače.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření vlastností virtuálního počítače VMware a kontrola, že odpovídá požadavkům Azure
> * Spuštění převzetí služeb při selhání do Azure


> [!NOTE]
> Kurzy vám ukážou, nejjednodušší způsob nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Pokud chcete další informace o převzetí služeb při selhání podrobně [k tomuto článku](site-recovery-failover.md).

## <a name="before-you-start"></a>Než začnete
Proveďte z předchozích kurzů:

1. Ujistěte se, že jste [nastavení Azure](tutorial-prepare-azure.md) zotavení po havárii místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických počítačů do Azure.
2. Příprava na místní [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) prostředí pro zotavení po havárii. Pokud při nastavování zotavení po havárii pro fyzické servery, přečtěte si [systém podpory replikace z](vmware-physical-secondary-support-matrix.md).
3. Nastavení zotavení po havárii pro [virtuálních počítačů VMware](vmware-azure-tutorial.md), [virtuálních počítačů Hyper-V](hyper-v-azure-tutorial.md), nebo [fyzické počítače](physical-azure-disaster-recovery.md).
4. Spuštění [zotavení po havárii](tutorial-dr-drill-azure.md) abyste měli jistotu, že vše funguje podle očekávání.


## <a name="failover-and-failback"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Převzetí služeb při selhání a navrácení služeb po obnovení probíhá ve čtyřech fázích:

1. **Převzetí služeb při selhání do Azure**: Pokud vaše místní primární lokalita ocitne mimo provoz, selhání počítačů do Azure. Po převzetí služeb při selhání se z replikovaných dat vytvoří virtuální počítače Azure.
2. **Znovunastavení ochrany virtuálních počítačů Azure**: Znovunastavení ochrany virtuálních počítačů Azure tak, aby se začaly replikovat zpět do místních virtuálních počítačů VMware v Azure. Během opětovného nastavování ochrany pro zajištění konzistence dat je místní virtuální počítač vypnutý.
3. **Převzetí služeb při selhání do místního**: Když běží v místní lokalitě a spuštěná, spusťte převzetí služeb při selhání obnovení z Azure.
4. **Znovunastavení ochrany místních virtuálních počítačů**: Po zpět navrácení dat znovunastavení ochrany místních virtuálních počítačů, na které můžete při navrácení služeb obnoví, tak, aby se začaly replikovat do Azure.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před spuštěním převzetí služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že virtuální počítače v souladu s [požadavky služby Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Ověřte vlastnosti následujícím způsobem:

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.

3. V **výpočty a síť**, můžete upravit název Azure, skupinu prostředků, cílovou velikost, [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)a nastavení spravovaného disku

4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.

5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   - **Nejnovější**: Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože se virtuální počítač Azure vytvoří teprve tehdy, až převzetí služeb při selhání bude mít veškerá data, která se do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
   - **Nejnovější zpracovaný**: Tato možnost převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
   - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání virtuálního počítače do bodu nejnovější konzistentní vzhledem k obnovení zpracovanému službou Site Recovery.
   - **Vlastní**: Zadejte bod obnovení.

3. Vyberte **před spuštěním převzetí služeb při selhání vypnout počítač** chcete pokusit před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Můžete si všimnout už testovacího převzetí služeb při selhání pro:
- Virtuální počítače VMware spuštěné verze služby Mobility, která je starší než 9.8
- Fyzické servery
- Virtuální počítače VMware s Linuxem
- Virtuální počítače Hyper-V ochranu, protože fyzické servery
- Virtuální počítače VMware, které nemají povolené služba DHCP
- Virtuální počítače VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Nepřerušujte převzetí služeb při selhání v průběhu**: Před zahájením převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="connect-to-failed-over-vm"></a>Připojte se k převzetí služeb virtuálního počítače při selhání

1. Pokud se chcete připojit k virtuálním počítačům Azure pomocí RDP/SSH po převzetí služeb při selhání, [Zkontrolujte tyto požadavky](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Po převzetí služeb při selhání, přejděte na virtuální počítač a ověřte tak, že [připojení](../virtual-machines/windows/connect-logon.md) k němu.
3. Použít **změnit bod obnovení** Pokud chcete použít jiný bod obnovení po převzetí služeb při selhání. Po potvrzení převzetí služeb při selhání v dalším kroku, tato možnost bude nadále již nebudou dostupné.
4. Po ověření klikněte na **potvrzení** pro dokončení bodu obnovení virtuálního počítače po převzetí služeb při selhání.
5. Po potvrzení jsou odstraněny všechny další dostupné body obnovení. Dokončení tohoto postupu převzetí služeb při selhání.

>[!TIP]
> Pokud narazíte na jakékoli problémy s připojením po převzetí služeb při selhání, použijte toto [Průvodce odstraňováním potíží](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Další postup

Po převzetí služeb při selhání znovunastavení ochrany virtuálních počítačů Azure do místní. Potom až se virtuální počítače znovu zapnout ochranu a replikaci do místní lokality, selhání obnovení z Azure až budete připravení.

> [!div class="nextstepaction"]
> [Znovunastavení ochrany virtuálních počítačů Azure](vmware-azure-reprotect.md)
> [selhat obnovení z Azure](vmware-azure-failback.md) 
