---
title: Spuštění cvičení pro zotavení po havárii do Azure pomocí Azure Site Recovery
description: Zjistěte, jak spustit cvičení pro zotavení po havárii z místního prostředí do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238899"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Spuštění postupu zotavení po havárii do Azure

Tento článek popisuje, jak spustit cvičení pro zotavení po havárii pro místní počítač do Azure pomocí služby [Azure Site Recovery.](site-recovery-overview.md) Postup ověří vaši strategii replikace bez ztráty dat.


Toto je čtvrtý kurz v řadě, který ukazuje, jak nastavit zotavení po havárii do Azure pro místní počítače.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavení izolované sítě pro testovací převzetí služeb při selhání
> * Příprava připojení k virtuálnímu počítači Azure po převzetí služeb při selhání
> * Spusťte test převzetí služeb při selhání pro jeden počítač.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu nasazení pro scénář. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Pokud se chcete dozvědět o postupech cvičení zotavení po havárii podrobněji, [přečtěte si tento článek](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Než začnete

Dokončete předchozí kurzy:

1. Ujistěte se, že jste [nastavili Azure](tutorial-prepare-azure.md) pro místní zotavení po havárii virtuálních počítačů VMware, virtuálních počítačů Hyper-V a fyzických počítačů do Azure.
2. Připravte si místní prostředí [VMware](vmware-azure-tutorial-prepare-on-premises.md) nebo [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) na zotavení po havárii. Pokud nastavujete zotavení po havárii pro fyzické servery, přečtěte si [matici podpory](vmware-physical-secondary-support-matrix.md).
3. Nastavte zotavení po havárii pro [virtuální počítače VMware](vmware-azure-tutorial.md), [virtuální počítače Hyper-V](hyper-v-azure-tutorial.md)nebo [fyzické počítače](physical-azure-disaster-recovery.md).
 

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že [virtuální počítač Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) nebo [virtuální počítač VMware](vmware-physical-azure-support-matrix.md#replicated-machines) splňuje požadavky Azure.

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V **aplikaci Compute and Network**můžete upravit název Azure, skupinu prostředků, velikost cíle, sadu dostupnosti a nastavení spravovaného disku.
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="create-a-network-for-test-failover"></a>Vytvoření sítě pro testovací převzetí služeb při selhání

Pro testovací převzetí služeb při selhání doporučujeme zvolit síť, která je izolovaná od produkční sítě lokality pro obnovení zadané v nastavení **Výpočty a síť** jednotlivých virtuálních počítačů. Když vytvoříte virtuální síť Azure, ve výchozím nastavení je izolovaná od ostatních sítí. Testovací síť by měla napodobovat produkční síť:

- Testovací síť by měla mít stejný počet podsítí jako produkční síť. Podsítě by měly mít stejné názvy.
- Testovací síť by měla používat stejný rozsah IP adres.
- Aktualizujte DNS testovací sítě s použitím IP adresy zadané pro virtuální počítač DNS v nastavení **Výpočty a síť**. Další podrobnosti najdete v tématu věnovaném [aspektům, které je třeba zvážit při testování převzetí služeb při selhání pro Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Spuštění testovacího převzetí služeb při selhání pro jeden virtuální počítač

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
3. Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

Spusťte testovací převzetí služeb při selhání následujícím způsobem:

1. V **nastavení** > **replikované položky**klepněte na položku > **+Test Failover**.
2. Pro účely tohoto kurzu vyberte **Nejnovější zpracovaný** bod obnovení. Tím se převezmou služby při selhání virtuálního počítače k nejnovějšímu dostupnému bodu v čase. Zobrazí se časové razítko. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
3. V části **Testovací převzetí služeb při selhání** vyberte cílovou síť Azure, ke které se virtuální počítače Azure po převzetí služeb při selhání připojí.
4. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat kliknutím na virtuální počítač, které otevře jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** >
   **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, že má virtuální počítač odpovídající velikost, je připojený ke správné síti a běží.
6. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit.
7. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V **poznámkách**zaznamenejte a uložte všechna pozorování spojená s převzetím služeb při selhání testu.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího trvání testovacího převzetí služeb při selhání u počítačů VMware s Linuxem, virtuálních počítačů VMware, které nemají povolenou službu DHCP, a virtuálních počítačů VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Připojit po převzetí služeb při selhání

Pokud se chcete připojit k virtuálním počítačům Azure pomocí RDP/SSH po převzetí služeb při selhání, [připravte se na připojení](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Pokud po převzetí služeb při selhání narazíte na problémy s připojením, postupujte podle pokynů [pro řešení potíží.](site-recovery-failover-to-azure-troubleshoot.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění převzetí služeb při selhání a navrácení služeb po selhání pro virtuální počítače](vmware-azure-tutorial-failover-failback.md)
> VMware[Spuštění převzetí služeb při selhání a navrácení služeb po selhání pro virtuální počítače](hyper-v-azure-failover-failback-tutorial.md)
> Hyper-V[spustit převzetí služeb při selhání a navrácení služeb po selhání pro fyzické počítače](physical-to-azure-failover-failback.md)
