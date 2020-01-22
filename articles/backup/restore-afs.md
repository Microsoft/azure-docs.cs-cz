---
title: Obnovení sdílených složek Azure
description: Naučte se používat Azure Portal k obnovení celé sdílené složky nebo konkrétních souborů z bodu obnovení vytvořeného službou Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294393"
---
# <a name="restore-azure-file-shares"></a>Obnovení sdílených složek Azure

Tento článek vysvětluje, jak použít Azure Portal k obnovení celé sdílené složky nebo určitých souborů z bodu obnovení vytvořeného službou [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) .

V této příručce se dozvíte, jak:

* Obnovení úplné sdílené složky Azure
* Obnovení jednotlivých souborů nebo složek
* Sledovat stav operace obnovení

## <a name="steps-to-perform-restore"></a>Kroky k provedení obnovení

### <a name="select-the-file-share-to-restore"></a>Vyberte sdílenou složku, kterou chcete obnovit.

1. V [Azure Portal](https://portal.azure.com/)otevřete Recovery Services trezor, který jste použili ke konfiguraci zálohování pro sdílenou složku.

2. V části **chráněné položky** v okně **Přehled** klikněte na **zálohované položky** .

    ![Klikněte na zálohované položky.](./media/restore-afs/backup-items.png)

3. Po kliknutí na **zálohované položky**se vedle okna s **přehledem** zobrazí nové okno se seznamem všech typů správy zálohování, jak vidíte níže:

    ![Typy správy zálohování](./media/restore-afs/backup-management.png)

4. V části **zálohované položky**v části **typ správy zálohování**vyberte **Azure Storage (soubory Azure)** . Zobrazí se seznam všech sdílených složek a jejich odpovídající účet úložiště zálohovaný pomocí tohoto trezoru.

    ![Seznam všech sdílených složek](./media/restore-afs/file-shares.png)

5. V seznamu sdílených složek Azure vyberte požadovanou sdílenou složku, pro kterou chcete operaci obnovení provést.

### <a name="full-share-recovery"></a>Úplné obnovení sdílené složky

Pomocí této možnosti obnovení můžete obnovit úplnou sdílenou složku v původním nebo alternativním umístění.

1. V okně **zálohovaná položka** vyberte možnost **obnovit sdílenou** položku, která se zobrazí po výběru požadované sdílené složky, která se má obnovit v kroku 5 v části [vyberte sdílenou složku, kterou chcete obnovit](#select-the-file-share-to-restore) .

   ![Vybrat obnovit sdílenou složku](./media/restore-afs/restore-share.png)

2. Jakmile kliknete na **obnovit sdílenou složku**, otevře se okno **obnovit** s nabídkou **bodu obnovení** , ve kterém se zobrazí seznam bodů obnovení dostupných pro vybranou sdílenou složku.

3. Vyberte bod obnovení, který chcete použít k provádění operace obnovení, a klikněte na tlačítko **OK**.

    ![Vybrat bod obnovení](./media/restore-afs/restore-point.png)

4. Po kliknutí na tlačítko OK se nabídka pro obnovení přepne na **umístění pro obnovení**. V části **umístění pro obnovení**určete, kam (nebo jak) se mají data obnovit. Můžete zvolit jednu z následujících dvou možností:

    * **Původní umístění**: Obnovte úplnou sdílenou složku do stejného umístění jako původní zdroj.
    * **Alternativní umístění**: Obnovte úplnou sdílenou složku do alternativního umístění a zachovejte původní sdílenou složku **tak, jak je**.

#### <a name="restore-to-original-location"></a>Obnovit do původního umístění

1. Jako **cíl obnovení** zvolte **původní umístění** a vyberte, jestli se má přeskočit nebo přepsat, pokud dojde ke konfliktům. Po provedení příslušného výběru klikněte na **OK** .

    ![Zvolit původní umístění](./media/restore-afs/original-location.png)

2. Kliknutím na tlačítko **obnovit** spusťte operaci obnovení.

    ![Kliknutím na obnovit spusťte](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Obnovit do alternativního umístění

1. Jako cíl obnovení vyberte **alternativní umístění** .
2. Vyberte cílový účet úložiště, ve kterém chcete obnovit zálohovaný obsah, z rozevírací nabídky pole **účet úložiště** .
3. V závislosti na účtu úložiště, který jste vybrali v kroku 2, v rozevírací nabídce **Vybrat sdílenou složku** se zobrazí seznam sdílených složek, které jsou ve vybraném účtu úložiště k dispozici. Vyberte sdílenou složku, ve které chcete obnovit zálohovaný obsah.
4. V poli **název složky** zadejte název složky, kterou chcete vytvořit v cílové sdílené složce s obnoveným obsahem.
5. Vyberte, jestli se mají přeskočit nebo přepsat, pokud dojde ke konfliktům.
6. Po zadání odpovídajících hodnot do všech polí klikněte na **OK** .

    ![Vybrat alternativní umístění](./media/restore-afs/alternate-location.png)

7. Kliknutím na tlačítko obnovit spusťte operaci obnovení.

    ![Kliknutím na obnovit spusťte](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Obnovení na úrovni položek

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním nebo alternativním umístění.

1. Vyberte možnost **obnovení souboru** z okna **zálohovaná položka** , která se zobrazí po výběru požadované sdílené složky, která se má obnovit v kroku 5 části [Výběr sdílené složky pro obnovení](#select-the-file-share-to-restore) .

    ![Vybrat obnovení souboru](./media/restore-afs/file-recovery.png)

2. Jakmile kliknete na **obnovení souborů**, otevře se okno **obnovení** s nabídkou **bodů obnovení** , ve kterém se zobrazí seznam bodů obnovení dostupných pro vybranou sdílenou složku.

3. Vyberte bod obnovení, který chcete použít k provádění operace obnovení, a klikněte na tlačítko **OK**.

    ![Vybrat bod obnovení](./media/restore-afs/restore-point.png)

4. Po kliknutí na tlačítko OK se nabídka pro obnovení přepne na **umístění pro obnovení**. V části **umístění pro obnovení**určete, kam (nebo jak) se mají data obnovit. Můžete zvolit jednu z následujících dvou možností:

    * **Původní umístění**: Obnovit vybrané soubory nebo složky do stejné sdílené složky jako původní zdroj.
    * **Alternativní umístění**: Obnovte vybrané soubory nebo složky do alternativního umístění a zachovejte **Původní obsah sdílení**souborů.

#### <a name="restore-to-original-location"></a>Obnovit do původního umístění

1. Jako **cíl obnovení** zvolte **původní umístění** a vyberte, jestli se má přeskočit nebo přepsat, pokud dojde ke konfliktům.

    ![Původní umístění pro obnovení na úrovni položky](./media/restore-afs/original-location-item-level.png)

2. Klikněte na **Vybrat soubor** a zvolte soubory nebo složky, které chcete obnovit.

    ![Klikněte na vybrat soubor.](./media/restore-afs/select-file.png)

3. Po kliknutí na tlačítko **Vybrat soubor**, sdílenou složku se zobrazí obsah bodu obnovení sdílené složky, který jste zvolili pro obnovení.

4. Zaškrtněte políčko odpovídající souboru nebo složce, kterou chcete obnovit, a klikněte na **Vybrat**.

    ![Vybrat soubor nebo složku](./media/restore-afs/select-file-folder.png)

5. Opakujte kroky 2-4 pro výběr více souborů nebo složek pro obnovení.
6. Po výběru všech položek, které chcete obnovit, klikněte na tlačítko **OK**.

    ![Po výběru všech položek, které se mají obnovit, klikněte na OK.](./media/restore-afs/after-selecting-items.png)

7. Kliknutím na tlačítko obnovit spusťte operaci obnovení.

    ![Kliknutím na obnovit spusťte](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Obnovit do alternativního umístění

1. Jako cíl obnovení vyberte **alternativní umístění** .
2. Vyberte cílový účet úložiště, ve kterém chcete obnovit zálohovaný obsah, z rozevírací nabídky pole **účet úložiště** .
3. V závislosti na účtu úložiště, který jste vybrali v kroku 2, v rozevírací nabídce **Vybrat sdílenou složku** se zobrazí seznam sdílených složek, které jsou ve vybraném účtu úložiště k dispozici. Vyberte sdílenou složku, ve které chcete obnovit zálohovaný obsah.
4. V poli **název složky** zadejte název složky, kterou chcete vytvořit v cílové sdílené složce s obnoveným obsahem.
5. Vyberte, jestli se mají přeskočit nebo přepsat, pokud dojde ke konfliktům.
6. Klikněte na **Vybrat soubor** a zvolte soubory nebo složky, které chcete obnovit.

    ![Vyberte položky, které chcete obnovit do alternativního umístění.](./media/restore-afs/restore-to-alternate-location.png)

7. Po kliknutí na tlačítko **Vybrat soubor**, sdílenou složku se zobrazí obsah bodu obnovení sdílené složky, který jste zvolili pro obnovení.
8. Zaškrtněte políčko odpovídající souboru nebo složce, kterou chcete obnovit, a klikněte na **Vybrat**.

    ![Vybrat cíl obnovení](./media/restore-afs/recovery-destination.png)

9. Opakujte kroky 6-8 pro výběr více souborů nebo složek pro obnovení.
10. Po výběru všech položek, které chcete obnovit, klikněte na tlačítko **OK**.

    [Po výběru všech souborů klikněte na OK.](./media/restore-afs/after-selecting-all-items.png)

11. Kliknutím na tlačítko **obnovit** spusťte operaci obnovení.

## <a name="track-restore-operation"></a>Sledovat operaci obnovení

Po aktivaci operace obnovení vytvoří služba Backup úlohu pro sledování. Azure Backup zobrazuje oznámení o úloze na portálu. Chcete-li zobrazit operace pro úlohu, klikněte na hypertextový odkaz oznámení.

![Odkaz na oznámení](./media/restore-afs/notifications-link.png)

Můžete také monitorovat průběh obnovení z trezoru služby Recovery Services. Tady je postup kontroly stavu operace obnovení:

1. Otevřete Recovery Services trezor, ze kterého jste aktivovali operaci obnovení.
2. V **části monitorování** v okně **Přehled** klikněte na **úlohy zálohování** , abyste viděli stav operací spuštěných na různých úlohách.

    ![Klikněte na úlohy zálohování.](./media/restore-afs/backup-jobs.png)

3. Kliknutím na název úlohy odpovídající sdílené složce zobrazíte další podrobnosti o operaci obnovení, jako jsou přenesená data, počet obnovených souborů atd.

    ![Zobrazit obnovené podrobnosti](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Další kroky

* Naučte se [Spravovat zálohy sdílených složek Azure](manage-afs-backup.md)
