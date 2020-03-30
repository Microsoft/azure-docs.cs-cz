---
title: Obnovení sdílených složek Azure
description: Zjistěte, jak pomocí portálu Azure obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného službou Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586835"
---
# <a name="restore-azure-file-shares"></a>Obnovení sdílených složek Azure

Tento článek vysvětluje, jak pomocí portálu Azure obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

V tomto článku se dozvíte, jak:

* Obnovte úplnou sdílenou složku Azure.
* Obnovte jednotlivé soubory nebo složky.
* Sledujte stav operace obnovení.

## <a name="steps-to-perform-a-restore-operation"></a>Kroky k provedení operace obnovení

Chcete-li provést operaci obnovení, postupujte takto.

### <a name="select-the-file-share-to-restore"></a>Vyberte sdílenou složku, kterou chcete obnovit.

1. Na [webu Azure Portal](https://portal.azure.com/)otevřete trezor služby Recovery Services, který jste použili ke konfiguraci zálohování pro sdílenou složku.

1. V podokně přehledu vyberte **zálohovat položky** v části **Chráněné položky.**

    ![Vybrat položky zálohování](./media/restore-afs/backup-items.png)

1. Po výběru **možnosti Zálohovat položky**se vedle podokna přehledu otevře nové podokno se seznamem všech typů správy zálohování.

    ![Typy správy zálohování](./media/restore-afs/backup-management.png)

1. V podokně **Položky zálohování** v části **Typ správy zálohování**vyberte Azure Storage **(Soubory Azure).** Zobrazí se seznam všech sdílených složek a jejich odpovídajících účtů úložiště zálohovaných pomocí tohoto trezoru.

    ![Seznam všech sdílených složek](./media/restore-afs/file-shares.png)

1. Ze seznamu sdílených složek Azure vyberte sdílenou složku, pro kterou chcete provést operaci obnovení.

### <a name="full-share-recovery"></a>Úplné obnovení podílu

Tuto možnost obnovení můžete použít k obnovení úplné sdílené složky v původním umístění nebo v alternativním umístění.

1. Vyberte možnost **Obnovit sdílenou** složku v podokně **Zálohovat položku,** které se zobrazí po výběru sdílené složky, kterou chcete obnovit v kroku 5 [oddílu Vybrat sdílenou složku k obnovení.](#select-the-file-share-to-restore)

   ![Vyberte Obnovit sdílenou složku.](./media/restore-afs/restore-share.png)

1. Po výběru **možnosti Obnovit sdílenou složku**se otevře podokno **Obnovení** s nabídkou **Bod obnovení,** která zobrazuje seznam bodů obnovení dostupných pro vybranou sdílenou složku.

1. Vyberte bod obnovení, který chcete použít k provedení operace obnovení, a vyberte **OK**.

    ![Vybrat bod obnovení](./media/restore-afs/restore-point.png)

1. Po výběru **možnosti OK**se nabídka podokna **Obnovení** přepne na **obnovit umístění**. V **části Obnovit umístění**určete, kde a jak obnovit data. Vyberte jednu z následujících dvou možností:

    * **Původní umístění**: Obnovení úplné sdílené složky do stejného umístění jako původní zdroj.
    * **Alternativní umístění**: Obnovte úplnou sdílenou složku do alternativního umístění a udržujte původní sdílenou složku tak, jak je.

#### <a name="restore-to-the-original-location"></a>Obnovit původní umístění

1. Jako **cíl obnovení**vyberte **Původní umístění** a vyberte, zda chcete přeskočit nebo přepsat, pokud dojde ke konfliktům. Po příslušném výběru vyberte **OK**.

    ![Vybrat původní umístění](./media/restore-afs/original-location.png)

1. Chcete-li spustit operaci obnovení, vyberte **možnost Obnovit.**

    ![Vyberte Obnovit, chcete-li začít.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

1. Jako **cíl obnovení**vyberte **možnost Alternativní umístění** .
1. V rozevíracím seznamu **Účet úložiště** vyberte účet cílového úložiště, ve kterém chcete zálohovaný obsah obnovit.
1. V rozevíracím seznamu **Vybrat sdílenou složku** se zobrazí sdílené složky v účtu úložiště, který jste vybrali v kroku 2. Vyberte sdílenou složku, ve které chcete zálohovaný obsah obnovit.
1. Do pole **Název složky** zadejte název složky, který chcete vytvořit ve sdílené složce cílového souboru s obnoveným obsahem.
1. Vyberte, zda chcete přeskočit nebo přepsat, pokud dojde ke konfliktům.
1. Po zadání příslušných hodnot do všech polí vyberte **OK**.

    ![Vybrat alternativní umístění](./media/restore-afs/alternate-location.png)

1. Chcete-li spustit operaci obnovení, vyberte **možnost Obnovit.**

    ![Vyberte Obnovit, chcete-li začít.](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Obnovení na úrovni položek

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním umístění nebo v alternativním umístění.

1. Vyberte možnost **Obnovení souboru** v podokně **Zálohovat položku,** která se zobrazí po výběru sdílené složky, kterou chcete obnovit v kroku 5 [oddílu Vybrat sdílenou složku k obnovení.](#select-the-file-share-to-restore)

    ![Vybrat obnovení souboru](./media/restore-afs/file-recovery.png)

1. Po výběru **možnosti Obnovení souboru**se otevře podokno **Obnovení** s nabídkou **Bod obnovení,** která zobrazuje seznam bodů obnovení dostupných pro vybranou sdílenou složku.

1. Vyberte bod obnovení, který chcete použít k provedení operace obnovení, a vyberte **OK**.

    ![Vybrat bod obnovení](./media/restore-afs/restore-point.png)

1. Po výběru **možnosti OK**se nabídka podokna obnovení přepne na **obnovit umístění**. V **části Obnovit umístění**určete, kde a jak obnovit data. Vyberte jednu z následujících dvou možností:

    * **Původní umístění**: Obnovení vybraných souborů nebo složek do stejné sdílené složky jako původní zdroj.
    * **Alternativní umístění**: Obnovte vybrané soubory nebo složky do alternativního umístění a udržujte původní obsah sdílené složky tak, jak je.

#### <a name="restore-to-the-original-location"></a>Obnovit původní umístění

1. Jako **cíl obnovení**vyberte **Původní umístění** a vyberte, zda chcete přeskočit nebo přepsat, pokud dojde ke konfliktům.

    ![Původní umístění pro obnovení na úrovni položky](./media/restore-afs/original-location-item-level.png)

1. Zvolte **Vybrat soubor,** chcete-li vybrat soubory nebo složky, které chcete obnovit.

    ![Zvolte Vybrat soubor](./media/restore-afs/select-file.png)

1. Po výběru **možnosti Vybrat soubor**se v podokně sdílení souborů zobrazí obsah bodu obnovení sdílené složky, který jste vybrali pro obnovení.

1. Zaškrtněte políčko, které odpovídá souboru nebo složce, kterou chcete obnovit, a zvolte **Vybrat**.

    ![Výběr souboru nebo složky](./media/restore-afs/select-file-folder.png)

1. Opakováním kroků 2 až 4 vyberte více souborů nebo složek pro obnovení.
1. Po výběru všech položek, které chcete obnovit, vyberte **ok**.

    ![Po výběru všech položek, které chcete obnovit, vyberte OK](./media/restore-afs/after-selecting-items.png)

1. Chcete-li spustit operaci obnovení, vyberte **možnost Obnovit.**

    ![Vyberte Obnovit, chcete-li začít.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

1. Jako **cíl obnovení**vyberte **možnost Alternativní umístění** .
1. V rozevíracím seznamu **Účet úložiště** vyberte účet cílového úložiště, ve kterém chcete zálohovaný obsah obnovit.
1. V rozevíracím seznamu **Vybrat sdílenou složku** se zobrazí sdílené složky v účtu úložiště, který jste vybrali v kroku 2. Vyberte sdílenou složku, ve které chcete zálohovaný obsah obnovit.
1. Do pole **Název složky** zadejte název složky, který chcete vytvořit ve sdílené složce cílového souboru s obnoveným obsahem.
1. Vyberte, zda chcete přeskočit nebo přepsat, pokud dojde ke konfliktům.
1. Zvolte **Vybrat soubor,** chcete-li vybrat soubory nebo složky, které chcete obnovit.

    ![Výběr položek, které chcete obnovit do alternativního umístění](./media/restore-afs/restore-to-alternate-location.png)

1. Když zvolíte **Vybrat soubor**, podokno sdílení souborů zobrazí obsah bodu obnovení sdílené složky, který jste vybrali pro obnovení.
1. Zaškrtněte políčko, které odpovídá souboru nebo složce, kterou chcete obnovit, a zvolte **Vybrat**.

    ![Vybrat cíl obnovení](./media/restore-afs/recovery-destination.png)

1. Opakováním kroků 6 až 8 vyberte více souborů nebo složek pro obnovení.
1. Po výběru všech položek, které chcete obnovit, vyberte **ok**.

    ![Výběr OK po výběru všech souborů](./media/restore-afs/after-selecting-all-items.png)

1. Chcete-li spustit operaci obnovení, vyberte **možnost Obnovit.**

## <a name="track-a-restore-operation"></a>Sledování operace obnovení

Po spuštění operace obnovení vytvoří služba zálohování úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Chcete-li zobrazit operace pro úlohu, vyberte hypertextový odkaz oznámení.

![Vybrat hypertextový odkaz oznámení](./media/restore-afs/notifications-link.png)

Průběh obnovení můžete sledovat také z trezoru služby Recovery Services:

1. Otevřete trezor služby Recovery Services, odkud jste spustili operaci obnovení.
1. V podokně přehledu vyberte **možnost Zálohovací úlohy** v části **Monitorování,** chcete-li zobrazit stav operací spuštěných s různými úlohami.

    ![Vybrat zálohovací úlohy](./media/restore-afs/backup-jobs.png)

1. Výběrem názvu pracovního vytížení, který odpovídá sdílené složce, zobrazíte další podrobnosti o operaci obnovení, jako jsou **přenesená data** a **počet obnovených souborů**.

    ![Viz obnovené podrobnosti](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [spravovat zálohy sdílení souborů Azure](manage-afs-backup.md).
