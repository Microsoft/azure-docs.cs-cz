---
title: Správa záloh sdílených složek Azure
description: Tento článek popisuje běžné úkoly pro správu a monitorování sdílených složek Azure, které jsou zálohovány službou Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247654"
---
# <a name="manage-azure-file-share-backups"></a>Správa záloh sdílených složek Azure

Tento článek popisuje běžné úkoly pro správu a monitorování sdílených složek Azure, které jsou zálohovány [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). V trezoru služby Recovery Services se dozvíte, jak provést úkoly správy.

## <a name="monitor-jobs"></a>Monitorování úloh

Když spustíte operaci zálohování nebo obnovení, služba zálohování vytvoří úlohu pro sledování. Průběh všech úloh můžete sledovat na stránce **Úlohy zálohování**.

Otevření stránky **Úlohy zálohování**:

1. Otevřete trezor služby Recovery Services, který jste použili ke konfiguraci zálohování sdílených složek. V podokně **Přehled** vyberte v části **Monitorování** **položku Zálohování úloh.**

   ![Zálohování úloh v části Monitorování](./media/manage-afs-backup/backup-jobs.png)

1. Po výběru **možnosti OK**zobrazí podokno **Úlohy zálohování** stav všech úloh. Vyberte název pracovního vytížení, který odpovídá sdílené složce, kterou chcete sledovat.

   ![Název pracovního vytížení](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Vytvoření nové zásady

Můžete vytvořit novou zásadu pro zálohování sdílených složek Azure z části **Zásady zálohování** v trezoru služby Recovery Services. Všechny zásady vytvořené při konfiguraci zálohování sdílených složek se zobrazí s **typem zásad jako** **Azure File Share**.

Zobrazení existujících zásad zálohování:

1. Otevřete trezor služby Recovery Services, který jste použili ke konfiguraci zálohy pro sdílenou složku. V nabídce trezoru služby Recovery Services vyberte v části **Správa** **možnost Zásady zálohování.** Zobrazí se všechny zásady zálohování nakonfigurované v úschovně.

   ![Všechny zásady zálohování](./media/manage-afs-backup/all-backup-policies.png)

1. Pokud chcete zobrazit zásady specifické pro **Azure File Share**, vyberte Azure File **Share** z rozevíracího seznamu v pravém horním části.

   ![Výběr sdílené složky Azure](./media/manage-afs-backup/azure-file-share.png)

Vytvoření nové zásady zálohování:

1. V podokně **Zásady zálohování** vyberte **+ Přidat**.

   ![Nové zásady zálohování](./media/manage-afs-backup/new-backup-policy.png)

1. V podokně **Přidat** vyberte jako **typ zásadu** **Sdílení souborů Azure** . Otevře se podokno **zásad zálohování** pro sdílení **souborů Azure.** Zadejte název zásady, frekvenci zálohování a rozsah uchování bodů obnovení. Po definování zásady vyberte **ok**.

   ![Definování zásad zálohování](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Změnit zásadu

Můžete upravit zásady zálohování změnit frekvenci zálohování nebo rozsah uchování.

Změna zásady:

1. Otevřete trezor služby Recovery Services, který jste použili ke konfiguraci zálohy pro sdílenou složku. V nabídce trezoru služby Recovery Services vyberte v části **Správa** **možnost Zásady zálohování.** Zobrazí se všechny zásady zálohování nakonfigurované v úschovně.

   ![Všechny zásady zálohování v trezoru](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Pokud chcete zobrazit zásady specifické pro sdílenou složku Azure, vyberte **Azure File Share** z rozevíracího seznamu v pravém horním části. Vyberte zásadu zálohování, kterou chcete upravit.

   ![Sdílení souborů Azure k úpravám](./media/manage-afs-backup/azure-file-share-modify.png)

1. Otevře se podokno **Plán.** Podle potřeby upravte **plán zálohování** a **rozsah uchovávání** a vyberte **Uložit**. V podokně se zobrazí zpráva "Aktualizace probíhá". Po úspěšné aktualizaci zásad se zobrazí zpráva "Úspěšně aktualizována zásady zálohování.".

   ![Uložit upravenou zásadu](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Zastavení ochrany sdílené složky

Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

* Zastavte všechny budoucí úlohy zálohování a *odstraňte všechny body obnovení*.
* Zastavte všechny budoucí úlohy zálohování, ale *ponechte body obnovení*.

Může být náklady spojené s opuštěním body obnovení v úložišti, protože základní snímky vytvořené službou Azure Backup zůstanou zachovány. Výhodou opuštění bodů obnovení je, že můžete obnovit sdílenou složku později. Informace o nákladech na opuštění bodů obnovení naleznete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/backup/). Pokud se rozhodnete odstranit všechny body obnovení, nelze sdílenou složku obnovit.

Zastavení ochrany sdílené složky Azure:

1. Otevřete trezor služby Recovery Services, který obsahuje body obnovení sdílené složky. V části **Chráněné položky** vyberte **Položky zálohování.** Zobrazí se seznam typů položek zálohy.

   ![Položky zálohování](./media/manage-afs-backup/backup-items.png)

1. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam **Položky zálohování (Azure Storage (Soubory Azure)).**

   ![Vyberte Azure Storage (soubory Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. V seznamu **Položky zálohování (Azure Storage (Soubory Azure))** vyberte položku zálohování, pro kterou chcete ochranu zastavit.

1. Vyberte možnost **Zastavit zálohování.**

   ![Vybrat možnost Zastavit zálohování.](./media/manage-afs-backup/stop-backup.png)

1. V podokně **Zastavit zálohování** vyberte Zachovat **záložní data** nebo Odstranit **záložní data**. Pak vyberte **Zastavit zálohování**.

    ![Vyberte Zachovat záložní data nebo Odstranit záložní data.](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Obnovení ochrany sdílené složky

Pokud byla při zastavení ochrany sdílené složky vybrána možnost **Zachovat záložní data,** je možné ochranu obnovit. Pokud byla vybrána možnost **Odstranit záložní data,** ochrana sdílené složky nemůže pokračovat.

Obnovení ochrany sdílené složky Azure:

1. Otevřete trezor služby Recovery Services, který obsahuje body obnovení sdílené složky. V části **Chráněné položky** vyberte **Položky zálohování.** Zobrazí se seznam typů položek zálohy.

   ![Položky zálohování pro životopis](./media/manage-afs-backup/backup-items-resume.png)

1. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam **Položky zálohování (Azure Storage (Soubory Azure)).**

   ![Seznam Azure Storage (soubory Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. V seznamu **Položky zálohování (Azure Storage (Soubory Azure))** vyberte položku zálohování, pro kterou chcete obnovit ochranu.

1. Vyberte možnost **Pokračovat v zálohování.**

   ![Vybrat pokračovat v zálohování](./media/manage-afs-backup/resume-backup.png)

1. Otevře se podokno **Zásadzálohování.** Vyberte zásadu podle svého výběru, chcete-li pokračovat v zálohování.

1. Po výběru zásady zálohování vyberte **uložit**. Na portálu se zobrazí zpráva "Aktualizace probíhá". Po úspěšném obnovení zálohování se zobrazí zpráva "Úspěšně aktualizované zásady zálohování pro chráněnou sdílenou složku Azure".

   ![Zásady zálohování byly úspěšně aktualizovány](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Odstranění zálohovaných dat

Zálohu sdílené složky můžete odstranit během **úlohy Zastavit zálohování** nebo kdykoli po ukončení ochrany. Může být výhodné čekat dny nebo dokonce týdny, než odstraníte body obnovení. Když odstraníte zálohovaná data, nemůžete vybrat konkrétní body obnovení, které chcete odstranit. Pokud se rozhodnete zálohovat data, odstraníte všechny body obnovení přidružené ke sdílené složce.

Následující postup předpokládá, že ochrana byla zastavena pro sdílenou složku.

Odstranění záložních dat pro sdílenou složku Azure:

1. Po zastavení úlohy zálohování jsou v řídicím panelu **Položky zálohování** k dispozici možnosti **Obnovit zálohování** a Odstranit **data zálohování.** Vyberte možnost **Odstranit záložní data.**

   ![Odstranění zálohovaných dat](./media/manage-afs-backup/delete-backup-data.png)

1. Otevře se podokno **Odstranit záložní data.** Zadejte název sdílené složky pro potvrzení odstranění. Volitelně můžete zadat další informace v polích **Důvod** nebo **Komentáře.** Až budete si jistí, že chcete zálohovat data, vyberte **Odstranit**.

   ![Potvrdit odstranění dat](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Zrušení registrace účtu úložiště

Chcete-li chránit sdílené složky souborů v určitém účtu úložiště pomocí jiného trezoru služeb pro obnovení, [nejprve zastavte ochranu všech sdílených složek](#stop-protection-on-a-file-share) v tomto účtu úložiště. Potom zrušit registraci účtu z aktuálního trezoru služeb obnovení, který slouží k ochraně.

Následující postup předpokládá, že ochrana byla zastavena pro všechny sdílené složky v účtu úložiště, který chcete zrušit registraci.

Zrušení registrace účtu úložiště:

1. Otevřete trezor služby Recovery Services, kde je váš účet úložiště registrován.
1. V podokně **Přehled** vyberte možnost **Infrastruktura zálohování** v části **Správa.**

   ![Vybrat infrastrukturu zálohování](./media/manage-afs-backup/backup-infrastructure.png)

1. Otevře se podokno **Infrastruktura zálohování.** V části Účty úložiště Azure vyberte **Účty** **úložiště.**

   ![Vybrat účty úložiště](./media/manage-afs-backup/storage-accounts.png)

1. Po výběru **účtů úložiště**se zobrazí seznam účtů úložiště registrovaných v úschovně.
1. Klikněte pravým tlačítkem myši na účet úložiště, který chcete zrušit, a vyberte **zrušit registraci**.

   ![Vybrat zrušit registraci](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu Poradce při potížích se zálohováním sdílených složek Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files).
