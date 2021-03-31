---
title: StorSimple Snapshot Manager katalogu zálohování | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in StorSimple Snapshot Manager konzoly MMC zobrazit a spravovat katalog záloh.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 2efc35e65ca1db2b5241e1d3b2798e068880c87e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054989"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Použití Snapshot Manager StorSimple ke správě katalogu záloh

## <a name="overview"></a>Přehled
Primární funkcí StorSimple Snapshot Manager je, aby bylo možné ve formě snímků vytvořit záložní kopie svazků StorSimple konzistentní vzhledem k aplikacím. Snímky se pak zobrazí v souboru XML s názvem *katalog záloh*. Katalog záloh Uspořádá snímky podle skupiny svazků a pak podle místního snímku nebo snímku cloudu.

V tomto kurzu se dozvíte, jak můžete použít uzel **katalogu zálohování** k dokončení následujících úloh:

* Obnovení svazku
* Klonování svazku nebo skupiny svazků
* Odstranění zálohy
* Obnovení souboru
* Obnovení databáze Snapshot Manager StorSimple

Katalog záloh můžete zobrazit rozbalením uzlu **zálohovat katalog** v podokně **oboru** a následným rozbalením skupiny svazků.

* Pokud kliknete na název skupiny svazků, v podokně **výsledků** se zobrazí počet místních snímků a cloudových snímků dostupných pro skupinu svazků. 
* Pokud kliknete na **místní** snímek nebo **snímek v cloudu**, v podokně **výsledků** se zobrazí následující informace o každém snímku zálohy (v závislosti na nastaveních **zobrazení** ):
  
  * **Název** – čas pořízení snímku.
  * **Typ** – určuje, zda se jedná o místní snímek nebo snímek v cloudu.
  * **Vlastník** – vlastník obsahu. 
  * **K dispozici** – zda je snímek aktuálně k dispozici. **Hodnota true** označuje, že je snímek dostupný a je možné ho obnovit. **Hodnota false** znamená, že snímek již není k dispozici. 
  * **Importováno** – bez ohledu na to, zda byla záloha naimportována. **Hodnota true** označuje, že záloha byla naimportována ze služby StorSimple Správce zařízení v době, kdy bylo zařízení nakonfigurované v StorSimple Snapshot Manager; **Hodnota false** označuje, že nebyla naimportována, ale byla vytvořena pomocí StorSimple Snapshot Manager. (Importovanou skupinu svazků můžete snadno identifikovat, protože je přidaná přípona, která identifikuje zařízení, ze kterého se naimportovala skupina svazků.)
    
    ![Katalog záloh](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Pokud rozbalíte snímek **místního snímku** nebo **cloudu** a potom kliknete na název jednotlivého snímku, v podokně **výsledků** se zobrazí následující informace o snímku, který jste vybrali:
  
  * **Název** – svazek identifikovaný písmenem jednotky. 
  * **Místní název** – místní název jednotky (je-li k dispozici). 
  * **Zařízení** – název zařízení, na kterém se svazek nachází. 
  * **K dispozici** – zda je snímek aktuálně k dispozici. **Hodnota true** označuje, že je snímek dostupný a je možné ho obnovit. **Hodnota false** znamená, že snímek již není k dispozici. 

## <a name="restore-a-volume"></a>Obnovení svazku
K obnovení svazku ze zálohy použijte následující postup.

#### <a name="prerequisites"></a>Požadavky
Pokud jste to ještě neudělali, vytvořte svazek a skupinu svazků a pak svazek odstraňte. Ve výchozím nastavení StorSimple Snapshot Manager zálohuje svazek před tím, než umožní jeho odstranění. Tato preventivní opatření můžou zabránit ztrátě dat, pokud se svazek neúmyslně odstraní nebo pokud je potřeba obnovit data z jakéhokoli důvodu. 

StorSimple Snapshot Manager zobrazí během vytváření předběžné opatrnosti následující zprávu.

![Automatická zpráva snímku](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nelze odstranit svazek, který je součástí skupiny svazků. Možnost odstranění není k dispozici. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Postup obnovení svazku
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager. 
2. V podokně **obor** rozbalte uzel **Katalog zálohování** , rozbalte skupinu svazků a potom klikněte na **místní snímky** nebo **snímky cloudu**. V podokně **výsledků** se zobrazí seznam záložních snímků.
3. Vyhledejte zálohu, kterou chcete obnovit, klikněte pravým tlačítkem myši a pak klikněte na tlačítko **obnovit**.
   
    ![Obnovit katalog záloh](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stránce potvrzení zkontrolujte podrobnosti, zadejte **Potvrdit** a pak klikněte na **OK**. StorSimple Snapshot Manager používá k obnovení svazku zálohu.
   
    ![Zpráva o potvrzení obnovení](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Můžete monitorovat akci obnovení při spuštění. V podokně **obor** rozbalte uzel **úlohy** a potom klikněte na možnost **spuštěno**. Podrobnosti úlohy se zobrazí v podokně **výsledků** . Po dokončení úlohy obnovení se podrobnosti úlohy přenesou do seznamu **posledních 24 hodin** .

## <a name="clone-a-volume-or-volume-group"></a>Klonování svazku nebo skupiny svazků
K vytvoření duplicitního (klonování) svazku nebo skupiny svazků použijte následující postup.

#### <a name="to-clone-a-volume-or-volume-group"></a>Naklonování svazku nebo skupiny svazků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **Katalog zálohování** , rozbalte skupinu svazků a potom klikněte na **snímky cloudu**. V podokně **výsledků** se zobrazí seznam záloh.
3. Najděte svazek nebo skupinu svazků, které chcete klonovat, klikněte pravým tlačítkem myši na svazek nebo název skupiny svazků a klikněte na **klonovat**. Zobrazí se dialogové okno **klonovat cloudový snímek** .
   
    ![Naklonování snímku v cloudu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončete dialog **klonovat cloudový snímek** následujícím způsobem: 
   
   1. Do textového pole **název** zadejte název klonovaného svazku. Tento název se zobrazí v uzlu **svazky** . 
   2. (Volitelné) vyberte **jednotka** a v rozevíracím seznamu vyberte písmeno jednotky.
   3. (Volitelné) vyberte **složku (NTFS)** a zadejte cestu ke složce nebo klikněte na Procházet a vyberte umístění složky. 
   4. Klikněte na **Vytvořit**.
5. Po dokončení procesu klonování musíte inicializovat Klonovaný svazek. Spusťte Správce serveru a potom spusťte správu disků. Podrobné pokyny najdete v tématu [připojení svazků](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci se svazek zobrazí pod uzlem **svazky** v podokně **Rozsah** . Pokud se svazek nezobrazí, aktualizujte seznam svazků (klikněte pravým tlačítkem na uzel **svazky** a pak klikněte na **aktualizovat**).

## <a name="delete-a-backup"></a>Odstranění zálohy
Pomocí následujícího postupu můžete odstranit snímek ze zálohy katalogu. 

> [!NOTE]
> Odstranění snímku odstraní zálohovaná data přidružená ke snímku. Proces mazání dat z cloudu může ale nějakou dobu trvat.<br>


#### <a name="to-delete-a-backup"></a>Odstranění zálohy
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **Katalog zálohování** , rozbalte skupinu svazků a potom klikněte na **místní snímky** nebo **snímky cloudu**. V podokně **výsledků** se zobrazí seznam snímků.
3. Klikněte pravým tlačítkem na snímek, který chcete odstranit, a pak klikněte na **Odstranit**.
4. Jakmile se zobrazí potvrzovací zpráva, klikněte na tlačítko **OK**.

## <a name="recover-a-file"></a>Obnovení souboru
Pokud se soubor ze svazku omylem odstraní, můžete soubor obnovit tak, že nakopírujete snímek, který se předá odstraněním, pomocí snímku vytvoříte klon svazku a pak zkopírujete soubor z klonovaného svazku do původního svazku.

#### <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte aktuální zálohu skupiny svazků. Pak odstraňte soubor uložený na jednom ze svazků v této skupině svazků. Nakonec pomocí následujících kroků obnovte odstraněný soubor ze zálohy. 

#### <a name="to-recover-a-deleted-file"></a>Obnovení odstraněné souboru
1. Klikněte na ikonu Snapshot Manager StorSimple na ploše. Zobrazí se okno StorSimple Snapshot Manager Console. 
2. V podokně **obor** rozbalte uzel **Katalog zálohování** a přejděte ke snímku, který obsahuje odstraněný soubor. Obvykle byste měli vybrat snímek, který byl vytvořen těsně před odstraněním.
3. Najděte svazek, který chcete klonovat, klikněte na něj pravým tlačítkem myši a klikněte na **klonovat**. Zobrazí se dialogové okno **klonovat cloudový snímek** .
   
    ![Naklonování snímku v cloudu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončete dialog **klonovat cloudový snímek** následujícím způsobem: 
   
   1. Do textového pole **název** zadejte název klonovaného svazku. Tento název se zobrazí v uzlu **svazky** . 
   2. Volitelné Vyberte **jednotka** a v rozevíracím seznamu vyberte písmeno jednotky. 
   3. Volitelné Vyberte **složku (NTFS)** a zadejte cestu ke složce nebo klikněte na **Procházet** a vyberte umístění složky. 
   4. Klikněte na **Vytvořit**. 
5. Po dokončení procesu klonování musíte inicializovat Klonovaný svazek. Spusťte Správce serveru a potom spusťte správu disků. Podrobné pokyny najdete v tématu [připojení svazků](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci se svazek zobrazí pod uzlem **svazky** v podokně **Rozsah** . 
   
    Pokud se svazek nezobrazí, aktualizujte seznam svazků (klikněte pravým tlačítkem na uzel **svazky** a pak klikněte na **aktualizovat**).
6. Otevřete složku NTFS, která obsahuje Klonovaný svazek, rozbalte uzel **svazky** a pak otevřete Klonovaný svazek. Vyhledejte soubor, který chcete obnovit, a zkopírujte jej do primárního svazku.
7. Po obnovení souboru můžete odstranit složku NTFS, která obsahuje Klonovaný svazek.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Obnovení databáze Snapshot Manager StorSimple
Pravidelně byste měli zálohovat databázi StorSimple Snapshot Manager v hostitelském počítači. Pokud dojde k havárii nebo z nějakého důvodu dojde k selhání hostitelského počítače, můžete ho obnovit ze zálohy. Vytvoření zálohy databáze je ruční proces.

#### <a name="to-back-up-and-restore-the-database"></a>Zálohování a obnovení databáze
1. Zastavte službu Microsoft StorSimple Management:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   3. V okně **služby** vyberte **službu Microsoft StorSimple Management Service**.
   4. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **Zastavit službu**.
2. V hostitelském počítači přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog.. 
   
   > [!NOTE]
   > Složka ProgramData je skrytá.
   > 
   > 
3. Vyhledejte soubor XML katalogu, zkopírujte soubor a uložte kopii v bezpečném umístění nebo v cloudu. Pokud se hostitel nepodaří, můžete pomocí tohoto záložního souboru obnovit zásady zálohování, které jste vytvořili v StorSimple Snapshot Manager.
   
    ![Soubor katalogu Azure StorSimple Backup](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple Management: 
   
   1. Na řídicím panelu Správce serveru v nabídce **nástroje** vyberte **služby**.
   2. V okně **služby** vyberte **službu Microsoft StorSimple Management Service**.
   3. V pravém podokně v části **Služba správy Microsoft StorSimple** klikněte na **restartovat službu**.
5. V hostitelském počítači přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog.. 
6. Odstraňte soubor XML katalogu a nahraďte ho verzí zálohy, kterou jste vytvořili. 
7. Kliknutím na ikonu Snapshot Manager plochy StorSimple spusťte StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [použití Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Přečtěte si další informace o [úlohách a pracovních postupech StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

