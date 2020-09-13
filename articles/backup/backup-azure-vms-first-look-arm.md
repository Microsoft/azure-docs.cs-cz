---
title: Zálohování virtuálního počítače Azure z nastavení virtuálního počítače
description: V tomto článku se dozvíte, jak zálohovat jeden virtuální počítač Azure nebo několik virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667518"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Zálohování virtuálního počítače Azure z nastavení virtuálního počítače

Tento článek vysvětluje, jak zálohovat virtuální počítače Azure pomocí služby [Azure Backup](backup-overview.md) . Virtuální počítače Azure můžete zálohovat pomocí několika metod:

- Jeden virtuální počítač Azure: pokyny v tomto článku popisují, jak zálohovat virtuální počítač Azure přímo z nastavení virtuálního počítače.
- Několik virtuálních počítačů Azure: můžete nastavit trezor Recovery Services a nakonfigurovat zálohování pro několik virtuálních počítačů Azure. Postupujte podle pokynů v [tomto článku](backup-azure-arm-vms-prepare.md) v tomto scénáři.

## <a name="before-you-start"></a>Než začnete

1. [Přečtěte si](backup-architecture.md#how-does-azure-backup-work) , jak zálohování funguje, a [Ověřte](backup-support-matrix.md#azure-vm-backup-support) požadavky na podporu.
2. [Získejte přehled](backup-azure-vms-introduction.md) zálohování virtuálních počítačů Azure.

### <a name="azure-vm-agent-installation"></a>Instalace agenta virtuálního počítače Azure

K zálohování virtuálních počítačů Azure Azure Backup nainstaluje na agentovi virtuálního počítače na počítači rozšíření. Pokud byl váš virtuální počítač vytvořen z bitové kopie Azure Marketplace, Agent bude spuštěn. V některých případech, například pokud vytvoříte vlastní virtuální počítač nebo migrujete počítač z místního prostředí, možná budete muset agenta nainstalovat ručně.

- Pokud potřebujete agenta virtuálního počítače nainstalovat ručně, postupujte podle pokynů pro virtuální počítače se [systémem Windows](../virtual-machines/extensions/agent-windows.md) nebo [Linux](../virtual-machines/extensions/agent-linux.md) .
- Po instalaci agenta se při povolení zálohování Azure Backup nainstaluje rozšíření zálohování agenta. Aktualizuje a opraví rozšíření bez zásahu uživatele.

## <a name="back-up-from-azure-vm-settings"></a>Zálohování z nastavení virtuálního počítače Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **všechny služby** a ve filtru zadejte **virtuální počítače**a pak vyberte **virtuální počítače**.
3. V seznamu virtuálních počítačů vyberte virtuální počítač, který chcete zálohovat.
4. V nabídce virtuální počítač vyberte **zálohování**.
5. V **Recovery Services trezoru**postupujte takto:
   - Pokud už máte trezor, vyberte **Vybrat existující**a vyberte trezor.
   - Pokud nemáte trezor, vyberte **vytvořit novou**. Zadejte název trezoru. Vytvoří se ve stejné oblasti a skupině prostředků jako virtuální počítač. Tato nastavení nemůžete změnit, když povolíte zálohování přímo z nastavení virtuálního počítače.

        ![Průvodce povolením zálohování](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. V části **zvolit zásadu zálohování**proveďte jednu z následujících akcí:

   - Ponechte výchozí zásadu. Tím se virtuální počítač zálohuje jednou denně v zadaném čase a zachovává zálohy v trezoru po dobu 30 dnů.
   - Vyberte existující zásadu zálohování, pokud ji máte.
   - Vytvořte novou zásadu a definujte nastavení zásad.  

       ![Výběr zásady zálohování](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Vyberte **Povolit zálohování**. Tím se přidruží zásady zálohování k virtuálnímu počítači.

    ![Tlačítko Povolit zálohování](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Průběh konfigurace můžete sledovat v oznámeních na portálu.
9. Po dokončení úlohy vyberte v nabídce VM (virtuální počítač) možnost **zálohovat**. Stránka zobrazuje stav zálohování pro virtuální počítač, informace o vydaných bodech obnovení, spuštěných úlohách a výstrahách.

   ![Stav zálohy](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po povolení zálohování se spustí prvotní zálohování. Počáteční zálohu můžete spustit okamžitě nebo počkat, až začne v souladu s plánem zálohování.
    - Až do dokončení počáteční zálohy se **Stav poslední zálohy** zobrazí jako **varování (čeká se na prvotní zálohování)**.
    - Pokud chcete zjistit, kdy se spustí další naplánované zálohování, vyberte název zásady zálohování.

## <a name="run-a-backup-immediately"></a>Okamžitě spustit zálohování

1. Pokud chcete spustit zálohování okamžitě, v nabídce VM (virtuální počítač) vyberte **zálohovat**  >  **zálohu nyní**.

    ![Spustit zálohování](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. V části **zálohovat**pomocí ovládacího prvku kalendáře vyberte, do kdy se bude bod obnovení uchovávat > a **OK**.

    ![Den uchování zálohy](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Oznámení na portálu vám pomůžou zjistit, že se spustila úloha zálohování. Chcete-li monitorovat průběh zálohování, vyberte možnost **Zobrazit všechny úlohy**.

## <a name="back-up-from-the-recovery-services-vault"></a>Zálohování z trezoru Recovery Services

Postupujte podle pokynů v [tomto článku](backup-azure-arm-vms-prepare.md) a povolte zálohování virtuálních počítačů Azure nastavením Recovery Services trezoru Azure Backup a povolením zálohování v trezoru.

## <a name="next-steps"></a>Další kroky

- Pokud máte problémy s některým z postupů v tomto článku, přečtěte si [příručku k odstraňování potíží](backup-azure-vms-troubleshoot.md).
- [Přečtěte si o](backup-azure-manage-vms.md) správě záloh.
