---
title: Převzetí služeb při selhání virtuálních počítačů VMware do Azure pomocí Site Recovery
description: Přečtěte si, jak převzít služby při selhání virtuálních počítačů VMware do Azure v Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: bf47f08ac555cf60f59ba2b1a84750b6a9e2e0a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86132002"
---
# <a name="fail-over--vmware-vms"></a>Převzetí služeb při selhání pro virtuální počítače VMware

Tento článek popisuje, jak provést převzetí služeb při selhání z místního virtuálního počítače VMware do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Toto je pátý kurz v řadě, ve kterém se dozvíte, jak nastavit zotavení po havárii do Azure pro místní počítače.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, že vlastnosti virtuálního počítače VMware splňují požadavky Azure.
> * Převzetí služeb při selhání konkrétními virtuálními počítači do Azure

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. Používají výchozí možnosti, pokud je to možné, a nezobrazují všechna možná nastavení a cesty. Pokud se chcete dozvědět více o převzetí služeb při selhání, přečtěte si téma [selhání virtuálních počítačů a fyzických serverů](site-recovery-failover.md).

[Přečtěte si o](failover-failback-overview.md#types-of-failover) různých typech převzetí služeb při selhání. Pokud chcete převzít služby při selhání více virtuálních počítačů v plánu obnovení, přečtěte si [Tento článek](site-recovery-failover.md).

## <a name="before-you-start"></a>Než začnete

Dokončete předchozí kurzy:

1. Ujistěte se, že jste [nastavili Azure](tutorial-prepare-azure.md) pro místní zotavení po havárii virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických počítačů do Azure.
2. Připravte místní prostředí [VMware](vmware-azure-tutorial-prepare-on-premises.md) pro zotavení po havárii. 
3. Nastavte zotavení po havárii pro [virtuální počítače VMware](vmware-azure-tutorial.md).
4. Spusťte postup [zotavení po havárii](tutorial-dr-drill-azure.md) , abyste měli jistotu, že všechno funguje podle očekávání.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před spuštěním převzetí služeb při selhání zkontrolujte vlastnosti virtuálního počítače a ujistěte se, že virtuální počítače splňují [požadavky Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Ověřte vlastnosti následujícím způsobem:

1. V části **chráněné položky** vyberte **replikované položky** a potom vyberte virtuální počítač, který chcete ověřit.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **vlastnosti** zobrazíte další podrobnosti.

3. V **COMPUTE a síti** můžete tyto vlastnosti upravit podle potřeby:
    * Název Azure
    * Skupina prostředků
    * Cílová velikost
    * [Skupina dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)
    * Nastavení spravovaného disku

4. Můžete zobrazit a upravit nastavení sítě, včetně:

    * Síť a podsíť, ve kterých se virtuální počítač Azure bude nacházet po převzetí služeb při selhání.
    * IP adresa, která se mu přiřadí.

5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V **Nastavení**  >  **replikované položky** vyberte virtuální počítač, u kterého chcete převzít služby při selhání, a pak vyberte **převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Můžete použít jednu z následujících možností:
   * **Nejnovější:** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač Azure, který se vytvořil po převzetí služeb při selhání, má všechna data, která se replikují do Site Recovery při aktivaci převzetí služeb při selhání.
   * **Poslední zpracování**: Tato možnost nefunguje pro virtuální počítač do nejnovějšího bodu obnovení zpracovaného Site Recovery. Tato možnost poskytuje nízkou RTO (cíl doby obnovení), protože nestráví zpracováním nezpracovaných dat žádného času.
   * **Nejnovější konzistentní vzhledem k aplikacím**: pomocí této možnosti se virtuální počítač nezdařil do posledního bodu obnovení konzistentního vzhledem k aplikacím zpracovaného pomocí Site Recovery.
   * **Vlastní**: Tato možnost umožňuje zadat bod obnovení.

3. Před spuštěním **převzetí služeb při selhání vyberte vypnout počítač** , aby se před aktivací převzetí služeb při selhání ukončily zdrojové virtuální počítače. Převzetí služeb při selhání pokračuje i v případě, že vypnutí selhalo. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .

V některých scénářích převzetí služeb při selhání vyžaduje další zpracování, které trvá přibližně 8 až 10 minut. Můžete si všimnout delší doby testovacího převzetí služeb při selhání pro:

* Virtuální počítače VMware s verzí služby mobility, která je starší než 9,8.
* Fyzické servery.
* Virtuální počítače VMware Linux.
* Virtuální počítače Hyper-V chráněné jako fyzické servery.
* Virtuální počítače VMware, které nemají povolenou službu DHCP.
* Virtuální počítače VMware, které nemají následující ovladače pro spouštění: storvsc, VMBus, storflt, Intelide, ATAPI.

> [!WARNING]
> Nemůžete zrušit probíhající převzetí služeb při selhání. Před zahájením převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="connect-to-failed-over-vm"></a>Připojení k virtuálnímu počítači s podporou převzetí služeb při selhání

1. Pokud se chcete připojit k virtuálním počítačům Azure po převzetí služeb při selhání pomocí protokol RDP (Remote Desktop Protocol) (RDP) a Secure Shell (SSH), [Ověřte, že jsou splněné požadavky](failover-failback-overview.md#connect-to-azure-after-failover).
2. Po převzetí služeb při selhání přejdete na virtuální počítač a ověříte ho [připojením](../virtual-machines/windows/connect-logon.md) .
3. Použijte **změnu bodu obnovení** , pokud chcete po převzetí služeb při selhání použít jiný bod obnovení. Po potvrzení převzetí služeb při selhání v dalším kroku už tato možnost nebude dostupná.
4. Po ověření vyberte **Potvrdit** a dokončete bod obnovení virtuálního počítače po převzetí služeb při selhání.
5. Po potvrzení dojde k odstranění všech dalších dostupných bodů obnovení. Tento krok dokončí převzetí služeb při selhání.

>[!TIP]
> Pokud narazíte na problémy s připojením po převzetí služeb při selhání, postupujte podle pokynů [Průvodce řešením potíží](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Další kroky

Po převzetí služeb při selhání znovu nastavte ochranu virtuálních počítačů Azure do místního prostředí. Až budou virtuální počítače znovu chráněny a replikovány na místní lokalitu, po dokončení navrácení služeb po obnovení z Azure.

> [!div class="nextstepaction"]
> Znovu [zapnout ochranu virtuálních počítačů Azure](vmware-azure-reprotect.md) 
>  [Navrácení služeb po obnovení z Azure](vmware-azure-failback.md)
