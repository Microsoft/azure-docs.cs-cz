---
title: Zálohujte virtuální počítač Azure z nastavení virtuálního počítače pomocí Azure Backup
description: Zjistěte, jak zálohovat virtuální počítač Azure pomocí služby Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: cfbec94a2922995eed546d526c1f469e2ea54118
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969050"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Zálohování virtuálního počítače Azure z nastavení virtuálního počítače

Tento článek vysvětluje, jak zálohovat virtuální počítače Azure pomocí služby [Azure Backup](backup-overview.md) . Virtuální počítače Azure můžete zálohovat pomocí několika metod:

- Jeden virtuální počítač Azure: pokyny v tomto článku popisují, jak zálohovat virtuální počítač Azure přímo z nastavení virtuálního počítače.
- Několik virtuálních počítačů Azure: můžete nastavit trezor Recovery Services a nakonfigurovat zálohování pro několik virtuálních počítačů Azure. Postupujte podle pokynů v [tomto článku](backup-azure-arm-vms-prepare.md) v tomto scénáři.

## <a name="before-you-start"></a>Než začnete

1. [Přečtěte si](backup-architecture.md#how-does-azure-backup-work) , jak zálohování funguje, a [Ověřte](backup-support-matrix.md#azure-vm-backup-support) požadavky na podporu.
2. [Získejte přehled](backup-azure-vms-introduction.md) zálohování virtuálních počítačů Azure.

### <a name="azure-vm-agent-installation"></a>Instalace agenta virtuálního počítače Azure

Aby bylo možné zálohovat virtuální počítače Azure, Azure Backup nainstaluje rozšíření na agenta virtuálního počítače spuštěného v počítači. Pokud byl váš virtuální počítač vytvořený z image Azure Marketplace, Agent se spustí. V některých případech například vytvoříte vlastní virtuální počítač nebo migrujete počítač z místního prostředí. je možné, že budete muset agenta nainstalovat ručně.

- Pokud potřebujete agenta virtuálního počítače nainstalovat ručně, postupujte podle pokynů pro virtuální počítače se [systémem Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) .
- Po instalaci agenta se při povolení zálohování Azure Backup nainstaluje rozšíření zálohování agenta. Aktualizuje a opraví rozšíření bez zásahu uživatele.

## <a name="back-up-from-azure-vm-settings"></a>Zálohování z nastavení virtuálního počítače Azure

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. Klikněte na **všechny služby** a ve filtru zadejte **virtuální počítače**a potom klikněte na **virtuální počítače**.
3. V seznamu virtuálních počítačů vyberte virtuální počítač, který chcete zálohovat.
4. V nabídce VM (virtuální počítač) klikněte na **zálohovat**.
5. V **Recovery Services trezoru**postupujte takto:
   - Pokud už máte trezor, klikněte na **Vybrat existující**a vyberte trezor.
   - Pokud nemáte trezor, klikněte na **vytvořit nový**. Zadejte název trezoru. Vytvoří se ve stejné oblasti a skupině prostředků jako virtuální počítač. Tato nastavení nemůžete změnit, když povolíte zálohování přímo z nastavení virtuálního počítače.

   ![Průvodce povolením zálohování](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. V části **zvolit zásadu zálohování**udělejte toto:

   - Ponechte výchozí zásadu. Tím se virtuální počítač zálohuje jednou denně v zadaném čase a zachovává zálohy v trezoru po dobu 30 dnů.
   - Vyberte existující zásadu zálohování, pokud ji máte.
   - Vytvořte novou zásadu a definujte nastavení zásad.  

   ![Výběr zásady zálohování](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klikněte na **Povolit zálohování**. Tím se přidruží zásady zálohování k virtuálnímu počítači.

    ![Tlačítko Povolit zálohování](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Průběh konfigurace můžete sledovat v oznámeních na portálu.
9. Po dokončení úlohy klikněte v nabídce virtuální počítač na **zálohovat**. Stránka zobrazuje stav zálohování pro virtuální počítač, informace o vydaných bodech obnovení, spuštěných úlohách a výstrahách.

   ![Stav služby Backup](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po povolení zálohování se spustí prvotní zálohování. Počáteční zálohu můžete spustit okamžitě nebo počkat, až začne v souladu s plánem zálohování.
    - Až do dokončení počáteční zálohy se **Stav poslední zálohy** zobrazí jako **varování (čeká se na prvotní zálohování)** .
    - Pokud chcete zjistit, kdy se spustí další naplánované zálohování, klikněte na název zásady zálohování.

> [!NOTE]
> Služba Azure Backup vytvoří samostatnou skupinu prostředků (jinou než skupinu prostředků virtuálního počítače) k uložení snímku s formátem názvů **AzureBackupRG_geography_number** (příklad: AzureBackupRG_northeurope_1). Data v této skupině prostředků se uchovávají po dobu ve dnech, jak je uvedeno v části uchování snímku okamžitého obnovení v zásadách zálohování virtuálních počítačů Azure. Použití zámku u této skupiny prostředků může způsobit selhání zálohování.<br>
Tato skupina prostředků by se měla taky vyloučit z omezení podle názvů nebo značek, protože zásady omezení by zablokovaly vytváření kolekcí bodů prostředků v takovém případě, že způsobí selhání zálohování.

## <a name="run-a-backup-immediately"></a>Okamžitě spustit zálohování

1. Pokud chcete spustit zálohování okamžitě, v nabídce virtuální počítač klikněte na **zálohovat** > **Zálohovat nyní**.

    ![Spustit zálohování](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. V části **Zálohovat nyní** použijte ovládací prvek Kalendář k výběru do až do doby, kdy se bude bod obnovení uchovávat > a **OK**.

    ![Den uchování zálohy](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Oznámení na portálu vám pomůžou zjistit, že se spustila úloha zálohování. Pokud chcete monitorovat průběh zálohování, klikněte na **Zobrazit všechny úlohy**.

## <a name="back-up-from-the-recovery-services-vault"></a>Zálohování z trezoru Recovery Services

Postupujte podle pokynů v tomto článku a povolte zálohování virtuálních počítačů Azure nastavením Recovery Services trezoru Azure Backup a povolením zálohování v trezoru.

## <a name="next-steps"></a>Další kroky

- Pokud máte problémy s některým z postupů v tomto článku, přečtěte si [příručku k odstraňování potíží](backup-azure-vms-troubleshoot.md).
- [Přečtěte si o](backup-azure-manage-vms.md) správě záloh.
