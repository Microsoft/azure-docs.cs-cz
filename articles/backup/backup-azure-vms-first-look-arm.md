---
title: Zálohování virtuálního počítače Azure z nastavení virtuálních počítače
description: V tomto článku se dozvíte, jak zálohovat jedinečný virtuální počítač Azure nebo více virtuálních počítačích Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705441"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Zálohování virtuálního počítače Azure z nastavení virtuálních počítače

Tento článek vysvětluje, jak zálohovat virtuální počítače Azure se službou [Azure Backup.](backup-overview.md) Virtuální počítače Azure můžete zálohovat pomocí několika metod:

- Jeden virtuální počítač Azure: Pokyny v tomto článku popisují, jak zálohovat virtuální počítač Azure přímo z nastavení virtuálních počítače.
- Více virtuálních počítačích Azure: Můžete nastavit trezor služby Recovery Services a nakonfigurovat zálohování pro více virtuálních počítačích Azure. Postupujte podle pokynů v [tomto článku](backup-azure-arm-vms-prepare.md) pro tento scénář.

## <a name="before-you-start"></a>Než začnete

1. [Zjistěte,](backup-architecture.md#how-does-azure-backup-work) jak zálohování funguje, a [ověřte](backup-support-matrix.md#azure-vm-backup-support) požadavky na podporu.
2. [Získejte přehled](backup-azure-vms-introduction.md) o zálohování virtuálních počítačů Azure.

### <a name="azure-vm-agent-installation"></a>Instalace agenta virtuálního počítače Azure

Aby bylo možné zálohovat virtuální počítače Azure, Azure Backup nainstaluje rozšíření na agenta virtuálního počítače spuštěné na počítači. Pokud váš virtuální počítač byl vytvořen z image Azure marketplace, agent bude spuštěn. V některých případech například pokud vytvoříte vlastní virtuální počítač nebo migrujete počítač z místního. pravděpodobně bude nutné nainstalovat agenta ručně.

- Pokud potřebujete nainstalovat agenta virtuálního počítače ručně, postupujte podle pokynů pro virtuální počítače [se systémem Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) nebo [Linux.](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)
- Po instalaci agenta, když povolíte zálohování, Azure Backup nainstaluje rozšíření zálohy agentovi. Aktualizuje a opravuje rozšíření bez zásahu uživatele.

## <a name="back-up-from-azure-vm-settings"></a>Zálohování z nastavení virtuálních zařízení Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte na **Všechny služby** a ve filtru zadejte **Virtuální počítače**a potom klikněte na **Virtuální počítače**.
3. Ze seznamu virtuálních discích vyberte virtuální hod, který chcete zálohovat.
4. V nabídce Virtuální počítač klepněte na **tlačítko Zálohovat**.
5. V **trezoru služby Recovery Services**postupujte takto:
   - Pokud již trezor máte, klepněte na **Vybrat existující**a vyberte úschovnu.
   - Pokud trezor nemáte, klikněte na **Vytvořit nový**. Zadejte název trezoru. Je vytvořen ve stejné oblasti a skupiny prostředků jako virtuální hod. Tato nastavení nelze změnit, když povolíte zálohování přímo z nastavení virtuálního počítače.

   ![Průvodce povolením zálohování](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. V **části Zvolit zásady zálohování**postupujte takto:

   - Ponechte výchozí zásadu. To zálohuje virtuální ho virtuálního počítače jednou denně v době zadané a uchovává zálohy v trezoru po dobu 30 dnů.
   - Vyberte existující zásady zálohování, pokud ji máte.
   - Vytvořte novou zásadu a definujte nastavení zásad.  

   ![Výběr zásady zálohování](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Klepněte na **tlačítko Povolit zálohování**. To přidruží zásady zálohování k virtuálnímu počítače.

    ![Tlačítko Povolit zálohování](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Průběh konfigurace můžete sledovat v oznámeních portálu.
9. Po dokončení úlohy klikněte v nabídce Virtuální počítače na **zálohovat**. Na stránce se zobrazí stav zálohování pro virtuální počítače, informace o bodech obnovení, spuštěné úlohy a výstrahy vydané.

   ![Stav zálohy](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Po povolení zálohování se spustí počáteční záloha. Počáteční zálohu můžete spustit okamžitě nebo počkejte, až se spustí v souladu s plánem zálohování.
    - Dokud nebude počáteční záloha dokončena, **stav Poslední záloha** se zobrazí jako **Upozornění (Počáteční záloha čeká na vyřízení).**
    - Chcete-li zjistit, kdy bude spuštěna další plánovaná záloha, klikněte na název zásady zálohování.

## <a name="run-a-backup-immediately"></a>Okamžité spuštění zálohy

1. Chcete-li zálohu spustit okamžitě, klepněte v nabídce Virtuální počítače na **položku Zálohování** > **zálohy .**

    ![Spustit zálohování](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. V **aplikaci Backup Now** vyberte ovládací prvek kalendáře, dokud nebude bod obnovení zachován > a **OK**.

    ![Den uchování záloh](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Oznámení portálu vás naštve, že se aktivovala úloha zálohování. Chcete-li sledovat průběh zálohování, klepněte na tlačítko **Zobrazit všechny úlohy**.

## <a name="back-up-from-the-recovery-services-vault"></a>Zálohování z trezoru služby Recovery Services

Podle pokynů v tomto článku povolte zálohování pro virtuální počítače Azure nastavením trezoru služby Azure Backup Recovery Services a povolením zálohování v trezoru.

>[!NOTE]
> **Azure Backup teď podporuje selektivní zálohování a obnovení disku pomocí řešení zálohování virtuálního počítače Azure.**
>
>Azure Backup dnes podporuje zálohování všech disků (operačního systému a dat) ve virtuálním počítači společně pomocí řešení zálohování virtuálního počítače. S funkcí vyloučit disk, získáte možnost zálohovat jeden nebo několik z mnoha datových disků ve virtuálním počítače. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých do operace zálohování, což dále umožňuje obnovit podmnožinu disků z daného bodu obnovení během operace obnovení. To platí pro obnovení ze snímku a úložiště.
>
>**Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Další kroky

- Pokud máte potíže s některou z postupů v tomto článku, naleznete v [průvodci odstraňováním potíží](backup-azure-vms-troubleshoot.md).
- [Přečtěte si o](backup-azure-manage-vms.md) správě záloh.
