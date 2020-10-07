---
title: Spuštění postupu zotavení po havárii z řešení Azure VMware do Azure pomocí Azure Site Recovery
description: Přečtěte si, jak spustit postup zotavení po havárii z privátního cloudu řešení Azure VMware do Azure s Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814555"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Spuštění postupu zotavení po havárii z řešení Azure VMware do Azure

Tento článek popisuje, jak spustit postup zotavení po havárii pro virtuální počítač řešení Azure VMware do Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) . Postup ověří vaši strategii replikace bez ztráty dat.


Toto je čtvrtý kurz v řadě, ve kterém se dozvíte, jak nastavit zotavení po havárii do Azure pro počítače řešení Azure VMware.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]

> * Nastavení izolované sítě pro testovací převzetí služeb při selhání
> * Příprava připojení k virtuálnímu počítači Azure po převzetí služeb při selhání
> * Spusťte testovací převzetí služeb při selhání pro jeden počítač.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Pokud se chcete dozvědět víc o krocích v postupu zotavení po havárii podrobněji, [Přečtěte si tento článek](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Než začnete

Dokončete předchozí kurzy:

1. Ujistěte se, že jste [nastavili Azure](avs-tutorial-prepare-azure.md) na zotavení po havárii do Azure.
2. Pomocí [těchto kroků](avs-tutorial-prepare-avs.md) připravte nasazení řešení Azure VMware pro zotavení po havárii do Azure.
3. [Nastavte](avs-tutorial-replication.md) zotavení po havárii pro virtuální počítače řešení Azure VMware.
 

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače a ujistěte se, že  [virtuální počítač VMware](vmware-physical-azure-support-matrix.md#replicated-machines) splňuje požadavky Azure.

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V části **výpočty a síť**můžete upravit název Azure, skupinu prostředků, cílovou velikost, skupinu dostupnosti a nastavení spravovaného disku.
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="create-a-network-for-test-failover"></a>Vytvoření sítě pro testovací převzetí služeb při selhání

Pro testovací převzetí služeb při selhání doporučujeme zvolit síť, která je izolovaná od produkční sítě lokality pro obnovení zadané v nastavení **Výpočty a síť** jednotlivých virtuálních počítačů. Když vytvoříte virtuální síť Azure, ve výchozím nastavení je izolovaná od ostatních sítí. Testovací síť by měla napodobovat produkční síť:

- Testovací síť by měla mít stejný počet podsítí jako produkční síť. Podsítě by měly mít stejné názvy.
- Testovací síť by měla používat stejnou třídu IP adres a rozsah podsítě.
- Aktualizujte DNS testovací sítě s použitím IP adresy zadané pro virtuální počítač DNS v nastavení **Výpočty a síť**. Další podrobnosti najdete v tématu věnovaném [aspektům, které je třeba zvážit při testování převzetí služeb při selhání pro Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Spuštění testovacího převzetí služeb při selhání pro jeden virtuální počítač

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
3. Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

Spusťte testovací převzetí služeb při selhání následujícím způsobem:

1. V **Nastavení**  >  **replikované položky**klikněte na virtuální počítač > **+ testovací převzetí služeb při selhání**.
2. Pro účely tohoto kurzu vyberte **Nejnovější zpracovaný** bod obnovení. Tím se převezmou služby při selhání virtuálního počítače k nejnovějšímu dostupnému bodu v čase. Zobrazí se časové razítko. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
3. V části **Testovací převzetí služeb při selhání** vyberte cílovou síť Azure, ke které se virtuální počítače Azure po převzetí služeb při selhání připojí.
4. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat kliknutím na virtuální počítač, které otevře jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** >
   **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, že má virtuální počítač odpovídající velikost, je připojený ke správné síti a běží.
6. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit.
7. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V části **poznámky**si zaznamenejte a uložte všechny poznámky spojené s testovacím převzetím služeb při selhání.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího trvání testovacího převzetí služeb při selhání u počítačů VMware s Linuxem, virtuálních počítačů VMware, které nemají povolenou službu DHCP, a virtuálních počítačů VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Připojit po převzetí služeb při selhání

Pokud se chcete po převzetí služeb při selhání připojit k virtuálním počítačům Azure pomocí protokolu RDP/SSH, [Připravte se na připojení](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Pokud narazíte na problémy s připojením po převzetí služeb při selhání, postupujte podle pokynů průvodce [řešením potíží](site-recovery-failover-to-azure-troubleshoot.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění převzetí služeb při selhání](avs-tutorial-failover.md)

