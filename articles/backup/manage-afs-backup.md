---
title: Správa záloh sdílených složek Azure
description: Tento článek popisuje běžné úlohy správy a monitorování sdílených složek Azure, které jsou zálohované pomocí Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 784b22f11b57fb025f6d9401e10d527c83751898
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88757621"
---
# <a name="manage-azure-file-share-backups"></a>Správa záloh sdílených složek Azure

Tento článek popisuje běžné úlohy správy a monitorování sdílených složek Azure, které jsou zálohované pomocí [Azure Backup](./backup-overview.md). Naučíte se, jak provádět úlohy správy v trezoru Recovery Services.

## <a name="monitor-jobs"></a>Monitorování úloh

Při aktivaci operace zálohování nebo obnovení vytvoří služba zálohování úlohu pro sledování. Průběh všech úloh můžete sledovat na stránce **Úlohy zálohování**.

Otevření stránky **Úlohy zálohování**:

1. Otevřete Recovery Services trezor, který jste použili ke konfiguraci zálohování sdílených složek. V podokně **Přehled** vyberte **úlohy zálohování** v části **monitorování** .

   ![Úlohy zálohování v části monitorování](./media/manage-afs-backup/backup-jobs.png)

1. Po výběru **OK**se v podokně **úlohy zálohování** zobrazí stav všech úloh. Vyberte název úlohy, který odpovídá sdílené složce, kterou chcete monitorovat.

   ![Název úlohy](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Vytvoření nové zásady

Novou zásadu pro zálohování sdílených složek Azure můžete vytvořit v části **zásady zálohování** trezoru Recovery Services. Všechny zásady vytvořené při konfiguraci zálohování sdílených složek se zobrazí s **typem zásady** jako **sdílená složka Azure**.

Chcete-li zobrazit existující zásady zálohování:

1. Otevřete Recovery Services trezor, který jste použili ke konfiguraci zálohování sdílené složky. V nabídce trezoru Recovery Services v části **Spravovat** vyberte **zásady zálohování** . Zobrazí se všechny zásady zálohování nakonfigurované v trezoru.

   ![Všechny zásady zálohování](./media/manage-afs-backup/all-backup-policies.png)

1. Pokud chcete zobrazit zásady specifické pro **sdílenou složku Azure**, vyberte v rozevíracím seznamu v pravém horním rohu položku **Azure File Share** .

   ![Vybrat sdílenou složku Azure](./media/manage-afs-backup/azure-file-share.png)

Vytvoření nové zásady zálohování:

1. V podokně **zásady zálohování** vyberte **+ Přidat**.

   ![Nové zásady zálohování](./media/manage-afs-backup/new-backup-policy.png)

1. V podokně **Přidat** jako **Typ zásady**vyberte **sdílená složka Azure** . Otevře se podokno **zásady zálohování** pro **sdílenou složku Azure** . Zadejte název zásady, četnost zálohování a rozsah uchování bodů obnovení. Po definování zásady vyberte **OK**.

   ![Definování zásad zálohování](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Upravit zásadu

Chcete-li změnit četnost zálohování nebo rozsah uchování, můžete upravit zásady zálohování.

Postup úpravy zásady:

1. Otevřete Recovery Services trezor, který jste použili ke konfiguraci zálohování sdílené složky. V nabídce trezoru Recovery Services v části **Spravovat** vyberte **zásady zálohování** . Zobrazí se všechny zásady zálohování nakonfigurované v trezoru.

   ![Všechny zásady zálohování v trezoru](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Pokud chcete zobrazit zásady specifické pro sdílenou složku Azure, v pravém horním rohu vyberte **Azure File Share** v rozevíracím seznamu. Vyberte zásadu zálohování, kterou chcete upravit.

   ![Sdílená složka Azure, kterou chcete upravit](./media/manage-afs-backup/azure-file-share-modify.png)

1. Otevře se podokno **plán** . Podle potřeby upravte **plán zálohování** a **Rozsah uchování** a vyberte **Uložit**. V podokně se zobrazí zpráva "Probíhá aktualizace". Po úspěšném dokončení aktualizace zásad se zobrazí zpráva "úspěšně se aktualizovala zásada zálohování".

   ![Uložit upravenou zásadu](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Zastavení ochrany sdílené složky

Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

* Zastavte všechny budoucí úlohy zálohování a *odstraňte všechny body obnovení*.
* Zastavte všechny budoucí úlohy zálohování, ale *ponechejte body obnovení*.

Je možné, že jsou k dispozici náklady spojené s ponecháním bodů obnovení v úložišti, protože se zachovají základní snímky vytvořené pomocí Azure Backup. Výhodou při ponechání bodů obnovení je, že později můžete sdílenou složku obnovit. Informace o nákladech na ponechávání bodů obnovení najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/backup/). Pokud se rozhodnete odstranit všechny body obnovení, sdílenou složku nemůžete obnovit.

Zastavení ochrany sdílené složky Azure:

1. Otevřete trezor Recovery Services, který obsahuje body obnovení sdílené složky. V části **chráněné položky** vyberte **zálohované položky** . Zobrazí se seznam typů záložních položek.

   ![Zálohované položky](./media/manage-afs-backup/backup-items.png)

1. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam **zálohované položky (soubory Azure Storage (soubory Azure))** .

   ![Vybrat Azure Storage (soubory Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. V seznamu **zálohované položky Azure Storage (soubory Azure)** vyberte zálohovanou položku, pro kterou chcete zastavit ochranu.

1. Vyberte možnost **Zastavit zálohování** .

   ![Vyberte Zastavit zálohování.](./media/manage-afs-backup/stop-backup.png)

1. V podokně **Zastavit zálohování** vyberte **zachovat zálohovaná data** nebo **odstraňte zálohovaná data**. Pak vyberte **Zastavit zálohování**.

    ![Vybrat zachovat zálohovaná data nebo odstranit data zálohy](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Obnovení ochrany sdílené složky

Pokud byla při zastavení ochrany sdílené složky vybraná možnost **zachovat data záloh** , je možné obnovit ochranu. Pokud jste vybrali možnost **Odstranit data záloh** , ochrana sdílené složky se nemůže obnovit.

Obnovení ochrany sdílené složky Azure:

1. Otevřete trezor Recovery Services, který obsahuje body obnovení sdílené složky. V části **chráněné položky** vyberte **zálohované položky** . Zobrazí se seznam typů záložních položek.

   ![Zálohované položky pro pokračování](./media/manage-afs-backup/backup-items-resume.png)

1. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam **zálohované položky (soubory Azure Storage (soubory Azure))** .

   ![Seznam Azure Storage (soubory Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. V seznamu **zálohované položky Azure Storage (soubory Azure)** vyberte zálohovanou položku, pro kterou chcete obnovit ochranu.

1. Vyberte možnost **obnovit zálohu** .

   ![Vybrat pokračovat v zálohování](./media/manage-afs-backup/resume-backup.png)

1. Otevře se podokno **zásady zálohování** . Vyberte zásadu, kterou zvolíte pro obnovení zálohování.

1. Po výběru zásady zálohování vyberte **Uložit**. Na portálu se zobrazí zpráva o tom, že probíhá aktualizace. Po úspěšném dokončení zálohování se zobrazí zpráva "úspěšně se aktualizovaly zásady zálohování pro chráněnou sdílenou složku Azure".

   ![Zásada zálohování se úspěšně aktualizovala.](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Zálohu sdílené složky můžete odstranit během úlohy **zastavení zálohování** nebo kdykoli po zastavení ochrany. Před odstraněním bodů obnovení může být výhodné počkat dny nebo dokonce týdny. Při odstraňování zálohovaných dat nemůžete zvolit konkrétní body obnovení, které se mají odstranit. Pokud se rozhodnete odstranit zálohovaná data, odstraníte všechny body obnovení spojené se sdílenou složkou souborů.

Následující postup předpokládá, že ochrana pro sdílenou složku byla zastavena.

Odstranění zálohovaných dat pro sdílenou složku Azure:

1. Po zastavení úlohy zálohování jsou v řídicím panelu **zálohovaná položka** k dispozici možnosti **obnovit zálohu** a **odstranit záložní data** . Vyberte možnost **Odstranit data záloh** .

   ![Odstranění zálohovaných dat](./media/manage-afs-backup/delete-backup-data.png)

1. Otevře se podokno **Odstranit zálohovaná data** . Zadáním názvu sdílené složky potvrďte odstranění. Volitelně můžete zadat další informace v polích **důvod** nebo **Komentáře** . Až si budete jisti, že se data záloh odstraňují, vyberte **Odstranit**.

   ![Potvrdit odstranění dat](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Zrušení registrace účtu úložiště

Chcete-li chránit sdílené složky v konkrétním účtu úložiště pomocí jiného trezoru Recovery Services, nejprve [Zastavte ochranu pro všechny sdílené složky](#stop-protection-on-a-file-share) v tomto účtu úložiště. Pak zrušte registraci účtu z aktuálního trezoru Recovery Services používaného k ochraně.

Následující postup předpokládá, že se ochrana zastavila pro všechny sdílené složky v účtu úložiště, který chcete zrušit.

Zrušení registrace účtu úložiště:

1. Otevřete Recovery Services trezor, ve kterém je váš účet úložiště zaregistrovaný.
1. V podokně **Přehled** vyberte v části **Spravovat** možnost **infrastruktura zálohování** .

   ![Výběr infrastruktury zálohování](./media/manage-afs-backup/backup-infrastructure.png)

1. Otevře se podokno **infrastruktura zálohování** . V části **Azure Storage účty** vyberte **účty úložiště** .

   ![Vybrat účty úložiště](./media/manage-afs-backup/storage-accounts.png)

1. Po výběru **účtů úložiště**se zobrazí seznam účtů úložiště zaregistrovaných v trezoru.
1. Klikněte pravým tlačítkem na účet úložiště, který chcete zrušit, a vyberte zrušit **registraci**.

   ![Vybrat zrušit registraci](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [řešení potíží se zálohováním sdílených složek Azure](./troubleshoot-azure-files.md).
