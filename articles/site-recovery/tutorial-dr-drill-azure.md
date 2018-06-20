---
title: Spuštění postupu zotavení po havárii do Azure pro místní počítače pomocí Azure Site Recovery | Microsoft Docs
description: Informace o spuštění postupu zotavení po havárii z místního prostředí do Azure pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: d1b6dec122672e4f6260105f7b50af2cd7369947
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737101"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Spuštění postupu zotavení po havárii do Azure

[Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

- Toto je čtvrtý kurz řady, která ukazuje, jak nastavit zotavení po havárii do Azure pro místní virtuální počítače VMware. Předpokládá se, že jste dokončili předchozí kurzy:
    - V [prvním kurzu](tutorial-prepare-azure.md) jste nastavili komponenty Azure potřebné pro zotavení po havárii VMware.
    - Ve [druhém kurzu](vmware-azure-tutorial-prepare-on-premises.md) jste připravili místní komponenty pro zotavení po havárii a zkontrolovali požadované součásti.
    - Ve [třetím kurzu](vmware-azure-tutorial.md) jste nastavili a povolili replikaci vašich místních virtuálních počítačů VMware.
- Tyto kurzy demonstrují ten nejjednodušší způsob nasazení určitého scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. 


V tomto článku se dozvíte, jak pro místní počítač spustit postup zotavení po havárii do Azure s využitím testovacího převzetí služeb při selhání. Postup ověří vaši strategii replikace bez ztráty dat. Naučte se:

> [!div class="checklist"]
> * Nastavení izolované sítě pro testovací převzetí služeb při selhání
> * Příprava připojení k virtuálnímu počítači Azure po převzetí služeb při selhání
> * Spuštění testovacího převzetí služeb při selhání pro jeden počítač

Tento kurz demonstruje nejjednodušší způsob, jak nastavit zotavení po havárii VMware do Azure. Podrobnější informace ke krokům testovacího převzetí služeb při selhání najdete v [tomto průvodci](site-recovery-test-failover-to-azure.md).

## <a name="verify-vm-properties"></a>Ověření vlastností virtuálního počítače

Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti virtuálního počítače VMware a ujistěte se, že virtuální počítač Hyper-V[hyper-v-azure-support-matrix.md#replicated-vms], [fyzický server nebo virtuální počítač VMware](vmware-physical-azure-support-matrix.md#replicated-machines) splňuje požadavky Azure.

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V části **Výpočty a síť** můžete upravit název Azure, skupinu prostředků, cílovou velikost, skupinu dostupnosti a nastavení spravovaného disku.
4. Můžete zobrazit a upravit nastavení sítě, včetně sítě a podsítě, do které se virtuální počítače Azure umístí po převzetí služeb při selhání, a IP adresy, která se jim přiřadí.
5. V části **Disky** se zobrazí informace o operačním systému a datových discích ve virtuálním počítači.

## <a name="run-a-test-failover-for-a-single-vm"></a>Spuštění testovacího převzetí služeb při selhání pro jeden virtuální počítač

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
3. Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

Spusťte testovací převzetí služeb při selhání následujícím způsobem:

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **+ Testovací převzetí služeb při selhání**.
2. Pro účely tohoto kurzu vyberte **Nejnovější zpracovaný** bod obnovení. Tím se převezmou služby při selhání virtuálního počítače k nejnovějšímu dostupnému bodu v čase. Časové razítko je vidět. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
3. V části **Testovací převzetí služeb při selhání** vyberte cílovou síť Azure, ke které se virtuální počítače Azure po převzetí služeb při selhání připojí.
4. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat kliknutím na virtuální počítač, které otevře jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** >
   **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, že má virtuální počítač odpovídající velikost, je připojený ke správné síti a běží.
6. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit.
7. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut. Možná si všimnete delšího trvání testovacího převzetí služeb při selhání u počítačů VMware s Linuxem, virtuálních počítačů VMware, které nemají povolenou službu DHCP, a virtuálních počítačů VMware, které nemají následující ovladače spuštění: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spuštění převzetí služeb při selhání a navrácení služeb po obnovení pro místní virtuální počítače VMware](vmware-azure-tutorial-failover-failback.md)
