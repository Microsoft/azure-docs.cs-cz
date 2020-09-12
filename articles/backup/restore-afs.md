---
title: Obnovení sdílených složek Azure
description: Naučte se používat Azure Portal k obnovení celé sdílené složky nebo konkrétních souborů z bodu obnovení vytvořeného pomocí Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 35ca0936ae6efba716adb51f43326cdd5bfa2d98
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376770"
---
# <a name="restore-azure-file-shares"></a>Obnovení sdílených složek Azure

Tento článek vysvětluje, jak použít Azure Portal k obnovení celé sdílené složky nebo určitých souborů z bodu obnovení vytvořeného pomocí [Azure Backup](./backup-overview.md).

V tomto článku se dozvíte, jak:

* Obnovte úplnou sdílenou složku Azure.
* Obnovte jednotlivé soubory nebo složky.
* Sledujte stav operace obnovení.

## <a name="steps-to-perform-a-restore-operation"></a>Postup provedení operace obnovení

Chcete-li provést operaci obnovení, postupujte podle těchto kroků.

### <a name="select-the-file-share-to-restore"></a>Vyberte sdílenou složku, kterou chcete obnovit.

1. V [Azure Portal](https://portal.azure.com/)otevřete Recovery Services trezor, který jste použili ke konfiguraci zálohování pro sdílenou složku.

1. V podokně Přehled vyberte **položky zálohování** v části **chráněné položky** .

    ![Vybrat zálohované položky](./media/restore-afs/backup-items.png)

1. Po výběru **položek zálohy**se v podokně přehledu otevře nové podokno se seznamem všech typů správy zálohování.

    ![Typy správy zálohování](./media/restore-afs/backup-management.png)

1. V podokně **zálohované položky** v části **typ správy zálohování**vyberte **Azure Storage (soubory Azure)**. Zobrazí se seznam všech sdílených složek a jejich odpovídajících účtů úložiště, které se zálohují pomocí tohoto trezoru.

    ![Seznam všech sdílených složek](./media/restore-afs/file-shares.png)

1. V seznamu sdílených složek Azure vyberte sdílenou složku, pro kterou chcete operaci obnovení provést.

### <a name="full-share-recovery"></a>Úplné obnovení sdílené složky

Pomocí této možnosti obnovení můžete obnovit úplnou sdílenou složku v původním umístění nebo v alternativním umístění.

1. V podokně **zálohovaná položka** vyberte možnost **obnovit sdílenou** položku, která se zobrazí po výběru sdílené složky, která se má obnovit v kroku 5 části [Výběr sdílené složky pro obnovení](#select-the-file-share-to-restore) .

   ![Vybrat obnovit sdílenou složku](./media/restore-afs/restore-share.png)

1. Jakmile vyberete možnost **obnovit sdílenou složku**, otevře se podokno **obnovení** . Chcete-li vybrat bod obnovení, který chcete použít k operaci obnovení, zvolte **text odkazu pod** textovým polem **bod obnovení** .

    ![Vyberte bod obnovení kliknutím na vybrat.](./media/restore-afs/select-restore-point.png)

1. Na pravé straně se otevře místní podokno **Vybrat bod obnovení** , ve kterém se zobrazí seznam bodů obnovení dostupných pro vybranou sdílenou složku. Vyberte bod obnovení, který chcete použít k provedení operace obnovení, a vyberte **OK**.

    ![Vybrat bod obnovení](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >Ve výchozím nastavení má podokno **Vybrat bod obnovení** seznam bodů obnovení za posledních 30 dní. Chcete-li se podívat na body obnovení vytvořené během určité doby trvání, určete rozsah tak, že vyberete příslušný **čas spuštění** a **čas ukončení** a kliknete na tlačítko **aktualizovat** .

1. Dalším krokem je výběr **umístění pro obnovení**. V části **cíl obnovení** určete, kde nebo jak mají být data obnovena. Pomocí přepínacího tlačítka vyberte jednu z následujících dvou možností:

    * **Původní umístění**: Obnovte úplnou sdílenou složku do stejného umístění jako původní zdroj.
    * **Alternativní umístění**: Obnovte úplnou sdílenou složku do alternativního umístění a zachovejte původní sdílenou složku tak, jak je.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Obnovit do původního umístění (úplné obnovení sdílené složky)

1. Jako **cíl obnovení**vyberte **původní umístění** a vyberte, jestli se má přeskočit nebo přepsat, pokud dojde ke konfliktům, a to tak, že vyberete vhodnou možnost z rozevíracího seznamu **v případě konfliktů** .

1. Vyberte **obnovit** a spusťte operaci obnovení.

    ![Vyberte Obnovit a začněte.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Obnovit do alternativního umístění (úplné obnovení sdílené složky)

1. Jako **cíl obnovení**vyberte **alternativní umístění** .
1. V rozevíracím seznamu **účet úložiště** vyberte cílový účet úložiště, ve kterém chcete obnovit zálohovaný obsah.
1. V rozevíracím seznamu **Vybrat sdílenou složku** se zobrazí sdílené složky, které jsou k dispozici v účtu úložiště, který jste vybrali v kroku 2. Vyberte sdílenou složku, ve které chcete obnovit zálohovaný obsah.
1. V poli **název složky** zadejte název složky, kterou chcete vytvořit v cílové sdílené složce s obnoveným obsahem.
1. Vyberte, jestli se mají přeskočit nebo přepsat, pokud dojde ke konfliktům.
1. Po zadání odpovídajících hodnot do všech polí vyberte **obnovit** a spusťte operaci obnovení.

    ![Vybrat alternativní umístění](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>Obnovení na úrovni položek

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním umístění nebo alternativním umístění.

1. Vyberte možnost **obnovení souboru** v podokně **zálohovaná položka** , která se zobrazí po výběru sdílené složky, která se má obnovit v kroku 5 části [Výběr sdílené složky pro obnovení](#select-the-file-share-to-restore) .

    ![Vybrat obnovení souboru](./media/restore-afs/file-recovery.png)

1. Po výběru **obnovení souboru**se otevře podokno **obnovení** . Chcete-li vybrat bod obnovení, který chcete použít k operaci obnovení **, vyberte text odkazu pod** textovým polem **bod obnovení** .

    ![Vyberte možnost bod obnovení kliknutím na odkaz vybrat.](./media/restore-afs/select-restore-point.png)

1. Na pravé straně se otevře místní podokno **Vybrat bod obnovení** , ve kterém se zobrazí seznam bodů obnovení dostupných pro vybranou sdílenou složku. Vyberte bod obnovení, který chcete použít k provedení operace obnovení, a vyberte **OK**.

    ![Vybrat bod obnovení](./media/restore-afs/restore-point.png)

1. Dalším krokem je výběr **umístění pro obnovení**. V části **cíl obnovení** určete, kde nebo jak mají být data obnovena. Pomocí přepínacího tlačítka vyberte jednu z následujících dvou možností:

    * **Původní umístění**: Obnovte vybrané soubory nebo složky do stejné sdílené složky jako původní zdroj.
    * **Alternativní umístění**: Obnovte vybrané soubory nebo složky do alternativního umístění a zachovejte původní obsah sdílení souborů.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Obnovit do původního umístění (obnovení na úrovni položek)

1. Jako **cíl obnovení**vyberte **původní umístění** a vyberte, jestli se má přeskočit nebo přepsat, pokud dojde ke konfliktům, a to výběrem příslušné možnosti v rozevíracím seznamu **v případě konfliktů** .

    ![Původní umístění pro obnovení na úrovni položek](./media/restore-afs/original-location-item-level.png)

1. Chcete-li vybrat soubory nebo složky, které chcete obnovit, vyberte tlačítko **Přidat soubor** . Tím se na pravé straně otevře podokno kontextu, ve kterém se zobrazí obsah bodu obnovení sdílené složky, který jste vybrali pro obnovení.

    ![Zvolit možnost Přidat soubor](./media/restore-afs/add-file.png)

1. Zaškrtněte políčko, které odpovídá souboru nebo složce, které chcete obnovit, a zvolte **Vybrat**.

    ![Vybrat soubor nebo složku](./media/restore-afs/select-file-folder.png)

1. Opakujte kroky 2 až 4 pro výběr více souborů nebo složek pro obnovení.
1. Po výběru všech položek, které chcete obnovit, vyberte **obnovit** a spusťte operaci obnovení.

    ![Vyberte Obnovit a začněte.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Obnovit do alternativního umístění (obnovení na úrovni položek)

1. Jako **cíl obnovení**vyberte **alternativní umístění** .
1. V rozevíracím seznamu **účet úložiště** vyberte cílový účet úložiště, ve kterém chcete obnovit zálohovaný obsah.
1. V rozevíracím seznamu **Vybrat sdílenou složku** se zobrazí sdílené složky, které jsou k dispozici v účtu úložiště, který jste vybrali v kroku 2. Vyberte sdílenou složku, ve které chcete obnovit zálohovaný obsah.
1. V poli **název složky** zadejte název složky, kterou chcete vytvořit v cílové sdílené složce s obnoveným obsahem.
1. Vyberte, jestli se mají přeskočit nebo přepsat, pokud dojde ke konfliktům.
1. Chcete-li vybrat soubory nebo složky, které chcete obnovit, vyberte tlačítko **Přidat soubor** . Tím se otevře kontextové podokno na pravé straně, kde se zobrazí obsah bodu obnovení sdílené složky, který jste zvolili pro obnovení.

    ![Vyberte položky, které chcete obnovit do alternativního umístění.](./media/restore-afs/restore-to-alternate-location.png)

1. Zaškrtněte políčko, které odpovídá souboru nebo složce, které chcete obnovit, a zvolte **Vybrat**.

    ![Vybrat cíl obnovení](./media/restore-afs/recovery-destination.png)

1. Opakujte kroky 6 až 8 pro výběr více souborů nebo složek pro obnovení.
1. Po výběru všech položek, které chcete obnovit, vyberte **obnovit** a spusťte operaci obnovení.

    ![Po výběru všech souborů vybrat OK](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Sledování operace obnovení

Po aktivaci operace obnovení vytvoří služba Backup úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Chcete-li zobrazit operace pro úlohu, vyberte hypertextový odkaz oznámení.

![Hypertextový odkaz pro výběr oznámení](./media/restore-afs/notifications-link.png)

Můžete také monitorovat průběh obnovení z trezoru Recovery Services:

1. Otevřete Recovery Services trezor, ze kterého jste aktivovali operaci obnovení.
1. V podokně Přehled vyberte **úlohy zálohování** v části **monitorování** a zobrazte stav operací spuštěných na různých úlohách.

    ![Vybrat úlohy zálohování](./media/restore-afs/backup-jobs.png)

1. Pokud chcete zobrazit další podrobnosti o operaci obnovení, třeba **přenesená data** a **počet obnovených souborů**, vyberte název úlohy, který odpovídá vaší sdílené složce.

    ![Zobrazit obnovené podrobnosti](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Další kroky

* Naučte se [Spravovat zálohy sdílených složek Azure](manage-afs-backup.md).
