---
title: Převzetí služeb při selhání virtuálních počítačích VMware do Azure s obnovením webu
description: Zjistěte, jak si přijet přes microsoft-up virtuálních monů VMware do Azure v Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498083"
---
# <a name="fail-over--vmware-vms"></a>Virtuální ho dispozice virtuálních měn VMware při selhání

Tento článek popisuje, jak převzetí služeb při selhání místní virtuální počítač VMware (VM) do Azure s [Azure Site Recovery](site-recovery-overview.md).

Toto je pátý kurz v řadě, který ukazuje, jak nastavit zotavení po havárii do Azure pro místní počítače.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte, zda vlastnosti virtuálního počítače VMware splňují požadavky Azure.
> * Převzetí služeb při selhání konkrétních virtuálních počítačích do Azure.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. Používají výchozí možnosti tam, kde je to možné, a nezobrazují všechna možná nastavení a cesty. Pokud se chcete podrobně dozvědět o převzetí služeb při selhání, přečtěte si informace o [převzetí služeb při selhání a fyzických serverech](site-recovery-failover.md).

[Další informace o](failover-failback-overview.md#types-of-failover) různých typech převzetí služeb při selhání. Pokud chcete převzetí služeb při selhání více virtuálních počítačů v plánu obnovení, přečtěte si [tento článek](site-recovery-failover.md).

## <a name="before-you-start"></a>Než začnete

Dokončete předchozí kurzy:

1. Ujistěte se, že jste [nastavili Azure](tutorial-prepare-azure.md) pro místní zotavení po havárii virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických počítačů do Azure.
2. Připravte místní prostředí [VMware](vmware-azure-tutorial-prepare-on-premises.md) na zotavení po havárii. 
3. Nastavte zotavení po havárii pro [virtuální virtuální měna VMware](vmware-azure-tutorial.md).
4. Spusťte [cvičení zotavení po havárii,](tutorial-dr-drill-azure.md) abyste se ujistili, že vše funguje podle očekávání.

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před spuštěním převzetí služeb při selhání zkontrolujte vlastnosti virtuálního počítače a ujistěte se, že virtuální počítače splňují [požadavky Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Ověřte vlastnosti následujícím způsobem:

1. V **části Chráněné položky**vyberte **Replikované položky**a vyberte virtuální počítač, který chcete ověřit.

2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Vyberte **Vlastnosti,** chcete-li zobrazit další podrobnosti.

3. V **počítačích Compute and Network**můžete podle potřeby upravit tyto vlastnosti:
    * Název Azure
    * Skupina prostředků
    * Velikost cíle
    * [Sada dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)
    * Nastavení spravovaného disku

4. Můžete zobrazit a upravit nastavení sítě, včetně:

    * Síť a podsíť, ve které bude virtuální počítač Azure umístěn po převzetí služeb při selhání.
    * Adresa IP, která k ní bude přiřazena.

5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="run-a-failover-to-azure"></a>Spuštění převzetí služeb při selhání do Azure

1. V **nastavení** > **Replikované položky**vyberte virtuální ms, který chcete přepojit převzetíslužeb služeb při selhání, a pak vyberte **převzetí služeb při selhání**.
2. V **převzetí služeb při selhání**vyberte bod **obnovení,** na který chcete přepojit. Můžete použít jednu z následujících možností:
   * **Nejnovější:** Tato možnost nejprve zpracuje veškerá data odeslaná do Site Recovery. Poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač Azure, který je vytvořen po převzetí služeb při selhání má všechna data, která byla replikována do site recovery při aktivaci převzetí služeb při selhání.
   * **Poslední zpracované**: Tato možnost selže virtuální ho virtuálního zařízení k nejnovějšímu bodu obnovení zpracovaného site recovery. Tato možnost poskytuje nízké RTO (Cíl doby obnovení), protože netráví čas zpracováním nezpracovaných dat.
   * **Nejnovější konzistentní aplikace**: Tato možnost selže virtuální ho virtuálního zařízení se k nejnovějším bodem obnovení konzistentní aplikace zpracované site recovery.
   * **Vlastní**: Tato možnost umožňuje určit bod obnovení.

3. Vyberte **Vypnout počítač před zahájením převzetí služeb při selhání** a pokuste se vypnout zdrojové virtuální počítače před aktivací převzetí služeb při selhání. Převzetí služeb při selhání pokračuje i v případě, že se vypnutí nezdaří. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, jehož dokončení trvá přibližně 8 až 10 minut. Můžete si všimnout delší zkušební doby převzetí služeb při selhání pro:

* Virtuální počítačvsystému VMware s verzí služby Mobility starší než 9.8.
* Fyzické servery.
* Virtuální počítače VMware Linux.
* Virtuální počítače Hyper-V chráněné jako fyzické servery.
* Virtuální many VMware, které nemají povolenou službu DHCP.
* Virtuální zařízení VMware, která nemají následující spouštěcí ovladače: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Neznevažuj probíhající převzetí služeb při selhání. Před zahájením převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.

## <a name="connect-to-failed-over-vm"></a>Připojení k virtuálnímu virtuálnímu zařízení s připojením k selhání

1. Pokud se chcete po převzetí služeb při selhání připojit k virtuálním počítačům Azure pomocí protokolu RDP (RDP) a zabezpečeného prostředí (SSH), [ověřte, zda byly splněny požadavky]((ailover-failback-overview.md#connect-to-azure-after-failover).
2. Po převzetí služeb při selhání přejděte na virtuální hod a ověřte [připojením](../virtual-machines/windows/connect-logon.md) k němu.
3. Pokud chcete po převzetí služeb při selhání použít **bod obnovení** změnit. Po potvrzení převzetí služeb při selhání v dalším kroku již nebude tato možnost k dispozici.
4. Po ověření vyberte **Potvrdit,** chcete-li dokončit bod obnovení virtuálního virtuálního soudu po převzetí služeb při selhání.
5. Po potvrzení budou odstraněny všechny ostatní dostupné body obnovení. Tento krok dokončí převzetí služeb při selhání.

>[!TIP]
> Pokud po převzetí služeb při selhání narazíte na potíže s připojením, postupujte podle [pokynů pro řešení potíží](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Další kroky

Po převzetí služeb při selhání znovu připevněte virtuální počítače Azure do místního prostředí. Potom po virtuálnípočítače jsou znovu chráněné a replikovat do místního webu, navrácení služeb po obnovení z Azure, když jste připraveni.

> [!div class="nextstepaction"]
> [Opětovné zabezpečení virtuálních počítačů Azure,](vmware-azure-reprotect.md)
> [které se z Azure vrátily,](vmware-azure-failback.md)
