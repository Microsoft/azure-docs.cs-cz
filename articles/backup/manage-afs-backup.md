---
title: Správa záloh sdílených složek Azure
description: Tento článek popisuje běžné úlohy pro správu a monitorování sdílených složek Azure, které jsou zálohované službou Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294549"
---
# <a name="manage-azure-file-share-backups"></a>Správa záloh sdílených složek Azure

Tento článek popisuje běžné úlohy pro správu a monitorování sdílených složek Azure, které jsou zálohované službou [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) . Naučíte se, jak spustit následující úlohy správy v trezoru Recovery Services:

* [Monitorování úloh](#monitor-jobs)
* [Vytvoření nové zásady](#create-a-new-policy)
* [Upravit zásadu](#modify-policy)
* [Zastavení ochrany sdílené složky](#stop-protection-on-a-file-share)
* [Obnovení ochrany sdílené složky](#resume-protection-on-a-file-share)
* [Odstranění zálohovaných dat](#delete-backup-data)
* [Zrušit registraci účtu úložiště](#unregister-storage-account)

## <a name="monitor-jobs"></a>Monitorování úloh

Při aktivaci operace zálohování nebo obnovení vytvoří služba zálohování úlohu pro sledování. Průběh všech úloh můžete sledovat na stránce **Úlohy zálohování**.

Otevření stránky **Úlohy zálohování**:

1. Otevřete Recovery Services trezor, který jste použili ke konfiguraci zálohování sdílených složek. V okně **Přehled** klikněte v části **monitorování** na **úlohy zálohování** .

   ![Úlohy zálohování v části monitorování](./media/manage-afs-backup/backup-jobs.png)

2. Po kliknutí na OK se zobrazí okno **úlohy zálohování** se seznamem stavů všech úloh. Můžete kliknout na název úlohy odpovídající sdílené složce, kterou chcete monitorovat.

   ![Název úlohy](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Vytvoření nové zásady

Novou zásadu pro zálohování sdílených složek Azure můžete vytvořit v části **zásady zálohování** trezoru Recovery Services. Všechny zásady vytvořené při konfiguraci zálohování sdílených složek se zobrazí s typem zásady jako sdílená složka Azure.

Zobrazení existujících zásad zálohování:

1. Otevřete trezor Recovery Services, který jste použili ke konfiguraci zálohy pro sdílenou složku, a v nabídce Recovery Services trezoru klikněte v části spravovat oddíl na **zásady zálohování** . Zobrazí se všechny zásady zálohování nakonfigurované v trezoru.

   ![Všechny zásady zálohování](./media/manage-afs-backup/all-backup-policies.png)

2. Pokud chcete zobrazit zásady specifické pro sdílenou složku Azure, vyberte v rozevíracím seznamu vpravo nahoře položku **Azure File Share** .

   ![Výběr sdílené složky Azure](./media/manage-afs-backup/azure-file-share.png)

Vytvoření nové zásady zálohování:

1. Klikněte na tlačítko **+ Přidat** v okně zásady zálohování.

   ![Nové zásady zálohování](./media/manage-afs-backup/new-backup-policy.png)

2. Jako **Typ zásady** v okně **Přidat** vyberte **sdílená složka Azure** . Otevře se okno zásady zálohování pro sdílenou složku Azure. Zadejte název zásady, četnost zálohování a rozsah uchování bodů obnovení. Po definování zásady klikněte na **OK** .

   ![Definování zásad zálohování](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Změnit zásady

Chcete-li změnit četnost zálohování nebo rozsah uchování, můžete upravit zásady zálohování.

Postup úpravy zásady:

1. Otevřete trezor Recovery Services, který jste použili ke konfiguraci zálohy pro sdílenou složku, a v nabídce Recovery Services trezoru klikněte na **zásady zálohování** v části spravovat. Zobrazí se všechny zásady zálohování nakonfigurované v trezoru.

   ![Všechny zásady zálohování v trezoru](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Pokud chcete zobrazit zásady specifické pro sdílenou složku Azure, vyberte v rozevíracím seznamu vpravo nahoře položku **Azure File Share** . Klikněte na zásadu zálohování, kterou chcete upravit.

   ![Sdílená složka Azure, kterou chcete upravit](./media/manage-afs-backup/azure-file-share-modify.png)

3. Otevře se okno **plánování** . Podle potřeby upravte plán zálohování/rozsah uchování a klikněte na **Uložit**. V okně se zobrazí zpráva "Probíhá aktualizace" a když se změny zásad úspěšně aktualizují, zobrazí se zpráva "úspěšné aktualizace zásad zálohování".

   ![Uložit upravenou zásadu](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Zastavení ochrany sdílené složky

Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

* Zastavit všechny budoucí úlohy zálohování a *Odstranit všechny body obnovení*
* Zastavte všechny budoucí úlohy zálohování *, ale ponechejte body obnovení* .

Je možné, že jsou k dispozici náklady spojené s ponecháním bodů obnovení v úložišti, protože jsou zachovány základní snímky vytvořené nástrojem Azure Backup. Výhodou ponechání bodů obnovení však je, že v případě potřeby můžete později sdílenou složku obnovit. Informace o nákladech na ponechávání bodů obnovení najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/backup/). Pokud se rozhodnete odstranit všechny body obnovení, nebudete moct sdílenou složku obnovit.

Zastavení ochrany sdílené složky Azure:

1. Otevřete trezor Recovery Services, který obsahuje body obnovení sdílené složky a klikněte na položku **zálohovat položky** v části chráněné položky. Zobrazí se seznam typů zálohovaných položek.

   ![Zálohované položky](./media/manage-afs-backup/backup-items.png)

2. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)** . Zobrazí se seznam **zálohových položek pro (soubory Azure Storage (soubory Azure))** .

   ![Vybrat Azure Storage (soubory Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. V seznamu **zálohované položky (Azure Storage (soubory Azure))** vyberte zálohovanou položku, pro kterou chcete zastavit ochranu.

4. V nabídce okna **zálohovaná položka** vyberte možnost **Zastavit zálohování** .

   ![Vyberte Zastavit zálohování.](./media/manage-afs-backup/stop-backup.png)

5. V okně **Zastavit zálohování** vyberte možnost **zachovat zálohovaná data** , **odstraňte zálohovaná data** a klikněte na **Zastavit zálohování**.

    ![Zvolit zachování nebo odstranění zálohovaných dat](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Obnovení ochrany sdílené složky

Pokud byla při zastavení ochrany sdílené složky zvolena možnost **zachovat data záloh** , je možné obnovit ochranu. Pokud jste zvolili možnost **Odstranit zálohovaná data**, pak ochranu sdílené složky obnovit nejde.

Obnovení ochrany sdílené složky Azure:

1. Otevřete trezor Recovery Services, který obsahuje body obnovení sdílené složky a klikněte na položku **zálohovat položky** v části chráněné položky. Zobrazí se seznam typů zálohovaných položek.

   ![Zálohované položky pro pokračování](./media/manage-afs-backup/backup-items-resume.png)

2. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)** . Zobrazí se seznam **zálohových položek pro (soubory Azure Storage (soubory Azure))** .

   ![Seznam Azure Storage (soubory Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. V seznamu **zálohované položky (Azure Storage (soubory Azure))** vyberte zálohovanou položku, pro kterou chcete obnovit ochranu.

4. V nabídce okna **zálohovaná položka** vyberte možnost **pokračovat v zálohování** .

   ![Vybrat pokračovat v zálohování](./media/manage-afs-backup/resume-backup.png)

5. Otevře se okno **zásady zálohování** , ve kterém můžete zvolit zásadu pro obnovení zálohování.

6. Po výběru požadovaných **zásad zálohování**klikněte na **Uložit** . Na portálu se zobrazí zpráva "Probíhá aktualizace" a po úspěšném obnovení zálohy se zobrazí zpráva "úspěšně se aktualizovaly zásady zálohování pro chráněnou sdílenou složku Azure".

   ![Zásada zálohování se úspěšně aktualizovala.](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Zálohu sdílené složky můžete odstranit během úlohy **zastavení zálohování** nebo kdykoli po zastavení ochrany. Před odstraněním bodů obnovení dokonce může být užitečné několik dnů nebo týdnů počkat. Při odstraňování zálohovaných dat nemůžete zvolit konkrétní body obnovení, které se mají odstranit. Pokud se rozhodnete odstranit zálohovaná data, odstraníte všechny body obnovení spojené se sdílenou složkou souborů.

Následující postup předpokládá, že ochrana pro sdílenou složku byla zastavena.

Odstranění zálohovaných dat pro sdílenou složku Azure:

1. Po zastavení úlohy zálohování jsou v řídicím panelu **zálohovaná položka** k dispozici možnosti **obnovit zálohu** a **odstranit záložní data** . V nabídce okna **zálohovaná položka** klikněte na možnost **Odstranit data záloh** .

   ![Odstranění zálohovaných dat](./media/manage-afs-backup/delete-backup-data.png)

2. Otevře se okno **Odstranit data zálohy** . Zadáním názvu sdílené složky potvrďte odstranění. Volitelně můžete zadat **důvod** , jak odstranit nebo **komentovat**. Až budete mít jistotu, že se data záloh odstraňují, klikněte na **Odstranit** .

   ![Potvrdit odstranění dat](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Zrušit registraci účtu úložiště

Pokud chcete chránit sdílené složky v konkrétním účtu úložiště pomocí jiného trezoru služby Recovery Services, nejdřív [Zastavte ochranu pro všechny sdílené složky](#stop-protection-on-a-file-share) v tomto účtu úložiště. Pak zrušte registraci účtu z aktuálního trezoru služby Recovery Services, který se používá k ochraně.

Následující postup předpokládá, že se ochrana zastavila pro všechny sdílené složky v účtu úložiště, který chcete zrušit.

Zrušení registrace účtu úložiště:

1. Otevřete trezor služby Recovery Services, ve kterém je váš účet úložiště zaregistrovaný.
2. Klikněte na možnost **infrastruktura zálohování** v části **Spravovat** v okně **Přehled** .

   ![Klikněte na infrastruktura zálohování.](./media/manage-afs-backup/backup-infrastructure.png)

3. Otevře se okno **infrastruktura zálohování** . V tomto okně klikněte na **účty úložiště** v části **Azure Storage účty** .

   ![Klikněte na účty úložiště.](./media/manage-afs-backup/storage-accounts.png)

4. Po kliknutí na **účty úložiště**se zobrazí seznam účtů úložiště zaregistrovaných v trezoru.
5. Klikněte pravým tlačítkem na účet úložiště, který chcete zrušit, a vyberte **zrušit registraci**.

   ![Vybrat zrušit registraci](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [řešení chyb zálohování a obnovení sdílených složek Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files) .
