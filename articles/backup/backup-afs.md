---
title: Zálohování sdílených složek Azure na webu Azure Portal
description: Přečtěte si, jak pomocí portálu Azure zálohovat sdílené složky Azure v trezoru služby Recovery Services.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938231"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Zálohování sdílených složek Azure v trezoru služby Recovery Services

Tento článek vysvětluje, jak používat portál Azure k zálohování [sdílených složek Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

V tomto článku se dozvíte, jak:

* Vytvořte trezor služby Recovery Services.
* Objevte sdílené složky a nakonfigurujte zálohy.
* Spusťte úlohu zálohování na vyžádání a vytvořte bod obnovení.

## <a name="prerequisites"></a>Požadavky

* Identifikujte nebo vytvořte [trezor služby Recovery Services](#create-a-recovery-services-vault) ve stejné oblasti jako účet úložiště, který je hostitelem sdílené složky.
* Ujistěte se, že sdílená složka souboru je k dispozici v jednom z [podporovaných typů účtů úložiště](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení pro zálohování sdílení souborů Azure během náhledu

Zálohování sdílených složek Azure je ve verzi Preview. Sdílené složky Azure v účtech úložiště pro obecné účely v1 i pro obecné účely v2 jsou podporované. Tady jsou omezení pro zálohování sdílených složek Azure:

* Podpora zálohování sdílených složek Azure v účtech úložiště s replikací [zónově redundantního úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) je momentálně omezená na [tyto oblasti](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup v současné době podporuje konfiguraci naplánované honosné zálohování sdílených složek Azure jednou denně.
* Maximální počet plánovaných záloh je jedna za den.
* Maximální počet záloh na vyžádání jsou čtyři za den.
* Pomocí [uzamčení prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) v účtu úložiště zabráníte náhodnému odstranění záloh v trezoru služby Recovery Services.
* Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může mít za následek ztrátu bodů obnovení nebo selhání obnovení.
* Neodstraňujte sdílené složky, které jsou chráněné službou Azure Backup. Aktuální řešení odstraní všechny snímky pořízené službou Azure Backup po odstranění sdílené složky, takže všechny body obnovení budou ztraceny.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Úprava replikace úložiště

Ve výchozím nastavení používají úložiště [geograficky redundantní úložiště (GRS).](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)

* Pokud je trezor primárním mechanismem zálohování, doporučujeme použít grs.
* Místně [redundantní úložiště (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) můžete použít jako možnost nízké náklady.

Změna typu replikace úložiště:

1. V novém trezoru vyberte **Vlastnosti** v části **Nastavení.**

1. Na stránce **Vlastnosti** vyberte v části **Konfigurace zálohování** **možnost Aktualizovat**.

1. Vyberte typ replikace úložiště a vyberte **Uložit**.

    ![Aktualizovat konfiguraci zálohování](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Po nastavení úložiště a obsahujícím položky zálohy nelze změnit typ replikace úložiště. Chcete-li to provést, je třeba úložiště znovu vytvořit.
>

## <a name="discover-file-shares-and-configure-backup"></a>Zjišťování sdílených složek a konfigurace zálohování

1. Na [webu Azure Portal](https://portal.azure.com/)otevřete trezor služby Recovery Services, který chcete použít k zálohování sdílené složky.

1. Na řídicím panelu **trezoru služby Recovery Services** vyberte **možnost +Backup**.

   ![Trezor služby Recovery Services](./media/backup-afs/recovery-services-vault.png)

    a. V **cíli zálohování** **Azure** **nastavte, kde běží vaše úloha?**

    ![Výběr služby Azure File Share jako cíle zálohování](./media/backup-afs/backup-goal.png)

    b.  V **části Co chcete zálohovat?**, vyberte v rozevíracím seznamu **položku Azure File Share.**

    c.  Vyberte **Zálohování,** chcete-li zaregistrovat příponu sdílené složky Azure v úschovně.

    ![Vyberte Zálohování, chcete-li přidružit sdílenou složku Azure k úschovně.](./media/backup-afs/register-extension.png)

1. Po výběru **možnosti Zálohování**se otevře podokno **Zálohování** a zobrazí výzvu k výběru účtu úložiště ze seznamu zjištěných podporovaných účtů úložiště. Jsou buď přidruženy k tomuto trezoru, nebo jsou přítomny ve stejné oblasti jako trezor, ale ještě nejsou přidruženy k žádnému trezoru služby Recovery Services.

   ![Výběr účtu úložiště](./media/backup-afs/select-storage-account.png)

1. Ze seznamu zjištěných účtů úložiště vyberte účet a vyberte **OK**. Azure prohledává účet úložiště pro sdílené složky, které lze zálohovat. Pokud jste nedávno přidali sdílené složky a nevidíte je v seznamu, vyčkejte nějakou dobu, než se sdílené složky zobrazí.

    ![Zjišťování sdílených složek](./media/backup-afs/discovering-file-shares.png)

1. Ze seznamu **Sdílené složky** vyberte jednu nebo více sdílených složek, které chcete zálohovat. Vyberte **OK**.

1. Po výběru sdílených složek se nabídka **Zálohování** přepne na **zásady zálohování**. V této nabídce vyberte existující zásady zálohování nebo vytvořte novou. Pak vyberte **Povolit zálohování**.

    ![Vybrat zásadu zálohování](./media/backup-afs/select-backup-policy.png)

Po nastavení zásady zálohování je v naplánovaném čase pořízen snímek sdílených složek. Bod obnovení je rovněž zachován pro zvolené období.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

V některých případech můžete chtít generovat snímek zálohy nebo bod obnovení mimo časy naplánované v zásadách zálohování. Běžným důvodem pro generování zálohy na vyžádání je hned po konfiguraci zásad zálohování. Na základě plánu v zásadách zálohování může být hodiny nebo dny, dokud snímek pořídil. Pokud chcete svá data chránit před zapojením zásady zálohování, vyvolejte zálohování na vyžádání. Před provedením plánovaných změn sdílených složek je často nutné vytvořit zálohu na vyžádání.

### <a name="create-a-backup-job-on-demand"></a>Vytvoření úlohy zálohování na vyžádání

1. Otevřete trezor služby Recovery Services, který jste použili k zálohování sdílené složky. V podokně **Přehled** vyberte **zálohovat položky** v části **Chráněné položky.**

   ![Vybrat položky zálohování](./media/backup-afs/backup-items.png)

1. Po výběru **možnosti Zálohovat položky**se vedle podokna **Přehled** zobrazí nové podokno se seznamem všech **typů správy zálohování.**

   ![Seznam typů správy zálohování](./media/backup-afs/backup-management-types.png)

1. V seznamu **Typ správy zálohování** vyberte Azure Storage **(Soubory Azure).** Zobrazí se seznam všech sdílených složek a odpovídajících účtů úložiště zálohovaných pomocí tohoto trezoru.

   ![Položky zálohování Azure Storage (Soubory Azure)](./media/backup-afs/azure-files-backup-items.png)

1. Ze seznamu sdílených složek Azure vyberte požadovanou sdílenou složku. Zobrazí se podrobnosti **položky zálohování.** V nabídce **Zálohování položky** vyberte **možnost Zálohovat nyní**. Vzhledem k tomu, že tato úloha zálohování je na vyžádání, neexistuje žádná zásada uchovávání informací spojená s bodem obnovení.

   ![Vybrat možnost Zálohovat](./media/backup-afs/backup-now.png)

1. Otevře se podokno **Zálohování.** Zadejte poslední den, ke kterému chcete uchovávat bod obnovení. Pro zálohování na vyžádání můžete mít maximální dobu uchovávání 10 let.

   ![Zvolit datum uchování](./media/backup-afs/retention-date.png)

1. Výběrem **možnosti OK** potvrďte spuštěnou úlohu zálohování na vyžádání.

1. Sledujte oznámení portálu a sledujte dokončení spuštění úlohy zálohování. Průběh úlohy můžete sledovat na řídicím panelu úschovny. Vyberte **možnost Probíhá zálohování** > **úloh**.

## <a name="next-steps"></a>Další kroky

Naučte se:
* [Obnovení sdílených složek Azure](restore-afs.md)
* [Správa záloh sdílení souborů Azure](manage-afs-backup.md)
