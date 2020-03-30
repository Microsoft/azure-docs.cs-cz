---
title: Katalog zálohování Správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje použití modulu snap-in Správce snímků StorSimple MMC k zobrazení a správě katalogu záloh.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 38ef7774263e4b28b7c316fd0870ca8f7b89d6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931707"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Správa katalogu záloh pomocí Správce snímků StorSimple

## <a name="overview"></a>Přehled
Primární funkcí Správce snímků StorSimple je umožnit vytvářet záložní kopie svazků StorSimple konzistentní chodaplikace ve formě snímků. Snímky jsou pak uvedeny v souboru XML nazývaném *záložní katalog*. Katalog zálohování uspořádá snímky podle skupiny svazků a potom podle místnísnímek nebo snímek cloudu.

Tento kurz popisuje, jak můžete pomocí uzlu **Katalog zálohování** k dokončení následujících úkolů:

* Obnovení svazku
* Klonování svazku nebo skupiny svazků
* Odstranění zálohy
* Obnovení souboru
* Obnovení databáze Správce snímků Storsimple

Katalog záloh můžete zobrazit rozbalením uzlu **Katalog zálohování** v podokně **Obor** a následným rozbalením skupiny svazků.

* Pokud klepnete na název skupiny svazků, podokno **Výsledky** zobrazí počet místních snímků a cloudových snímků, které jsou pro skupinu svazků k dispozici. 
* Pokud klepnete na **položku Místní snímek** nebo **Snímek cloudu**, zobrazí se v podokně **Výsledky** následující informace o každém snímku zálohy (v závislosti na nastavení **zobrazení):**
  
  * **Název** – čas pořízení snímku.
  * **Typ** – zda se jedná o místní snímek nebo snímek cloudu.
  * **Vlastník** – vlastník obsahu. 
  * **K dispozici** – zda je snímek aktuálně k dispozici. **True** označuje, že snímek je k dispozici a lze obnovit; **False** označuje, že snímek již není k dispozici. 
  * **Importováno** – zda byla záloha importována. **True** označuje, že záloha byla importována ze služby StorSimple Device Manager v době, kdy bylo zařízení nakonfigurováno ve Správci snímků StorSimple; **False** označuje, že nebyl importován, ale byl vytvořen Správcem snímků StorSimple. (Importovanou skupinu svazků můžete snadno identifikovat, protože je přidána přípona identifikující zařízení, ze kterého byla skupina svazků importována.)
    
    ![Katalog záloh](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Pokud rozbalíte **položku Místní snímek** nebo **Snímek cloudu**a potom klepnete na název jednotlivého snímku, zobrazí se v podokně **Výsledky** následující informace o vybraném snímku:
  
  * **Název** – svazek označený písmenem jednotky. 
  * **Místní název** – místní název jednotky (je-li k dispozici). 
  * **Zařízení** – název zařízení, na kterém je svazek umístěn. 
  * **K dispozici** – zda je snímek aktuálně k dispozici. **True** označuje, že snímek je k dispozici a lze obnovit; **False** označuje, že snímek již není k dispozici. 

## <a name="restore-a-volume"></a>Obnovení svazku
Pomocí následujícího postupu obnovte svazek ze zálohy.

#### <a name="prerequisites"></a>Požadavky
Pokud jste tak ještě neučinili, vytvořte skupinu svazků a svazků a odstraňte jej. Ve výchozím nastavení Správce snímků StorSimple zálohuje svazek před povolením jeho odstranění. Toto opatření může zabránit ztrátě dat, pokud je svazek odstraněn neúmyslně nebo pokud je z nějakého důvodu potřeba data obnovit. 

Správce snímků StorSimple zobrazí následující zprávu při vytváření zálohy pro bezpečné.

![Zpráva o automatickém snímku](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Svazek, který je součástí skupiny svazků, nelze odstranit. Možnost odstranění není k dispozici. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Obnovení svazku
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple. 
2. V podokně **Obor** rozbalte uzel **Katalog zálohování,** rozbalte skupinu svazků a klikněte na **položku Místní snímky** nebo Snímky **cloudu**. Seznam snímků záloh se zobrazí v podokně **Výsledky.**
3. Najděte zálohu, kterou chcete obnovit, klikněte pravým tlačítkem myši a potom klikněte na **obnovit**.
   
    ![Obnovení zálohovacího katalogu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stránce s potvrzením zkontrolujte podrobnosti, zadejte **potvrdit**a klepněte na tlačítko **OK**. Správce snímků StorSimple používá zálohu k obnovení svazku.
   
    ![Obnovit zprávu s potvrzením](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Akci obnovení můžete sledovat při spuštění. V podokně **Obor** rozbalte uzel **Úlohy** a klikněte na **položku Spuštěno**. Podrobnosti o úloze se zobrazí v podokně **Výsledky.** Po dokončení úlohy obnovení jsou podrobnosti úlohy převedeny do seznamu **Posledních 24 hodin.**

## <a name="clone-a-volume-or-volume-group"></a>Klonování svazku nebo skupiny svazků
Pomocí následujícího postupu vytvořte duplicitní (klonovací) svazek nebo skupinu svazků.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klonování svazku nebo skupiny svazků
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Katalog zálohování,** rozbalte skupinu svazků a klikněte na **položku Snímky cloudu**. V podokně **Výsledky** se zobrazí seznam záloh.
3. Najděte svazek nebo skupinu svazků, kterou chcete klonovat, klikněte pravým tlačítkem myši na název svazku nebo skupiny svazků a klikněte na **příkaz Klonovat**. Zobrazí se dialogové okno **Snímek klonování cloudu.**
   
    ![Klonování snímku cloudu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončete dialogové okno **Snímek clone cloudu** následujícím způsobem: 
   
   1. Do textového pole **Název** zadejte název klonovaného svazku. Tento název se zobrazí v uzlu **Svazky.** 
   2. (Volitelné) vyberte **Řídit**a pak v rozevíracím seznamu vyberte písmeno jednotky.
   3. (Volitelné) vyberte **Složku (NTFS)** a zadejte cestu ke složce nebo klepněte na Procházet a vyberte umístění složky. 
   4. Klikněte na **Vytvořit**.
5. Po dokončení procesu klonování je nutné klonovaný svazek inicializovat. Spusťte Správce serveru a potom spusťte správu disků. Podrobné pokyny naleznete v tématu [Připojení svazků](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci bude svazek uveden pod uzly **Svazky** v podokně **Obor.** Pokud není svazek v seznamu uveden, aktualizujte seznam svazků (klikněte pravým tlačítkem myši na uzel **Svazky** a potom klikněte na **Aktualizovat).**

## <a name="delete-a-backup"></a>Odstranění zálohy
Pomocí následujícího postupu odstraňte snímek z katalogu záloh. 

> [!NOTE]
> Odstranění snímku odstraní zálohovaná data přidružená ke snímku. Proces čištění dat z cloudu však může nějakou dobu trvat.<br>


#### <a name="to-delete-a-backup"></a>Odstranění zálohy
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Katalog zálohování,** rozbalte skupinu svazků a klikněte na **položku Místní snímky** nebo Snímky **cloudu**. Seznam snímků se zobrazí v podokně **Výsledky.**
3. Klikněte pravým tlačítkem myši na snímek, který chcete odstranit, a potom klikněte na **Odstranit**.
4. Po zobrazíní potvrzovací zpráva klepněte na tlačítko **OK**.

## <a name="recover-a-file"></a>Obnovení souboru
Pokud je soubor omylem odstraněn ze svazku, můžete soubor obnovit načtením snímku, který předepište odstranění, pomocí snímku vytvořit klon svazku a potom zkopírovat soubor z klonovaného svazku na původní svazek.

#### <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte aktuální zálohu skupiny svazků. Potom odstraňte soubor uložený na jednom ze svazků v této skupině svazků. Nakonec pomocí následujících kroků obnovte odstraněný soubor ze zálohy. 

#### <a name="to-recover-a-deleted-file"></a>Obnovení odstraněného souboru
1. Klikněte na ikonu Správce snímků StorSimple na ploše. Zobrazí se okno konzoly Správce snímků StorSimple. 
2. V podokně **Obor** rozbalte uzel **Katalog zálohování** a přejděte na snímek, který obsahuje odstraněný soubor. Obvykle byste měli vybrat snímek, který byl vytvořen těsně před odstraněním.
3. Najděte svazek, který chcete klonovat, klikněte pravým tlačítkem myši a klikněte na **Klonovat**. Zobrazí se dialogové okno **Snímek klonování cloudu.**
   
    ![Klonování snímku cloudu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončete dialogové okno **Snímek clone cloudu** následujícím způsobem: 
   
   1. Do textového pole **Název** zadejte název klonovaného svazku. Tento název se zobrazí v uzlu **Svazky.** 
   2. (Nepovinné) Vyberte **Jednotka**a pak v rozevíracím seznamu vyberte písmeno jednotky. 
   3. (Nepovinné) Vyberte **Složka (NTFS)** a zadejte cestu ke složce nebo klepněte na **Procházet** a vyberte umístění složky. 
   4. Klikněte na **Vytvořit**. 
5. Po dokončení procesu klonování je nutné klonovaný svazek inicializovat. Spusťte Správce serveru a potom spusťte správu disků. Podrobné pokyny naleznete v tématu [Připojení svazků](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci bude svazek uveden pod uzly **Svazky** v podokně **Obor.** 
   
    Pokud není svazek v seznamu uveden, aktualizujte seznam svazků (klikněte pravým tlačítkem myši na uzel **Svazky** a potom klikněte na **Aktualizovat).**
6. Otevřete složku NTFS, která obsahuje klonovaný svazek, rozbalte uzel **Svazky** a otevřete klonovaný svazek. Najděte soubor, který chcete obnovit, a zkopírujte ho na primární svazek.
7. Po obnovení souboru můžete odstranit složku NTFS, která obsahuje klonovaný svazek.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Obnovení databáze Správce snímků StorSimple
Měli byste pravidelně zálohovat databázi Správce snímků StorSimple v hostitelském počítači. Pokud dojde k pohromě nebo hostitelský počítač z nějakého důvodu selže, můžete jej obnovit ze zálohy. Vytvoření zálohy databáze je ruční proces.

#### <a name="to-back-up-and-restore-the-database"></a>Zálohování a obnovení databáze
1. Zastavte službu Microsoft StorSimple Management Service:
   
   1. Spusťte Správce serveru.
   2. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   3. V okně **Služby** vyberte **službu Microsoft StorSimple Management Service**.
   4. V pravém podokně klikněte v části **Microsoft StorSimple Management Service**na zastavit **službu**.
2. V hostitelském počítači přejděte na c:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Složka ProgramData je skrytá.
   > 
   > 
3. Najděte soubor XML katalogu, zkopírujte soubor a uložte kopii na bezpečném místě nebo v cloudu. Pokud se hostitel nezdaří, můžete pomocí tohoto záložního souboru obnovit zásady zálohování, které jste vytvořili ve Správci snímků StorSimple.
   
    ![Soubor zálohovacího katalogu Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple Management Service: 
   
   1. Na řídicím panelu Správce serveru vyberte v nabídce **Nástroje** **položku Služby**.
   2. V okně **Služby** vyberte **službu Microsoft StorSimple Management Service**.
   3. V pravém podokně klepněte v části **Microsoft StorSimple Management Service**na **restartovat službu**.
5. V hostitelském počítači přejděte na c:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Odstraňte soubor XML katalogu a nahraďte jej záložní verzí, kterou jste vytvořili. 
7. Kliknutím na ikonu Správce snímků StorSimple na ploše spusťte Správce snímků StorSimple. 

## <a name="next-steps"></a>Další kroky
* Další informace o [použití Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Další informace o [úkolech a pracovních postupech správce snímků StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

