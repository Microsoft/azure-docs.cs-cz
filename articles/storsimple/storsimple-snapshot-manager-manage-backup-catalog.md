---
title: Katalog záloh Snapshot Manageru zařízení StorSimple | Dokumentace Microsoftu
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manageru k zobrazení a Správa katalogu záloh.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: dc24ebd59fd977ef35766c304aec5824e2c7bb4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127180"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Použití StorSimple Snapshot Manageru Správa katalogu záloh

## <a name="overview"></a>Přehled
Aby bylo možné vytvořit konzistentní záložní kopie svazky zařízení StorSimple ve formě snímky je primární funkce StorSimple Snapshot Manageru. Snímky jsou pak uvedeny v souboru XML s názvem *katalog záloh*. Katalog záloh slouží k uspořádání snímky podle skupiny svazků a pak podle místní snímek nebo cloudový snímek.

Tento kurz popisuje, jak můžete použít **katalog zálohování** uzel dokončit následující úlohy:

* Obnovit svazek
* Klonovat svazek nebo skupiny svazků
* Odstranit zálohu
* Obnovení souboru
* Obnovení databáze Storsimple Snapshot Manageru

Katalog záloh můžete zobrazit rozbalením **katalog záloh** uzlu **oboru** podokně a pak rozbalení skupiny svazků.

* Pokud kliknete na název skupiny svazků **výsledky** podokno zobrazuje počet místních snímků a cloudové snímky, které jsou k dispozici pro skupinu svazku. 
* Vyberete-li **místní snímek** nebo **Cloudový snímek**, **výsledky** podokně se zobrazí následující informace o jednotlivých snímek zálohy (v závislosti na vaší  **Zobrazit** nastavení):
  
  * **Název** – čas pořízení snímku.
  * **Typ** – Určuje, zda je to místní snímek nebo cloudový snímek.
  * **Vlastník** – vlastníka obsahu. 
  * **K dispozici** – Určuje, zda je aktuálně k dispozici snímek. **Hodnota TRUE** označuje, že snímku je k dispozici a je možné obnovit; **False** označuje, že snímek už není k dispozici. 
  * **Importovat** – Určuje, zda byl importován zálohování. **Hodnota TRUE** označuje, že zálohování bylo importováno z ve službě Správce zařízení StorSimple v době zařízení bylo konfigurováno ve StorSimple Snapshot Manageru; **False** znamená, že nebyl importován, ale byla vytvořena pomocí StorSimple Snapshot Manageru. (Umožňuje snadno identifikovat skupinu importované svazku protože přidá příponu, která identifikuje zařízení, ze které bylo importováno skupiny svazků.)
    
    ![katalog záloh](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Pokud rozbalíte **místní snímek** nebo **Cloudový snímek**a potom klikněte na název jednotlivých snímků **výsledky** podokně se zobrazí následující informace o snímku který jste vybrali:
  
  * **Název** – svazek identifikován písmenem jednotky. 
  * **Místní název** – místní název jednotky (Pokud je k dispozici). 
  * **Zařízení** – název zařízení, na kterém se nachází svazku. 
  * **K dispozici** – Určuje, zda je aktuálně k dispozici snímek. **Hodnota TRUE** označuje, že snímku je k dispozici a je možné obnovit; **False** označuje, že snímek už není k dispozici. 

## <a name="restore-a-volume"></a>Obnovit svazek
Pomocí následujícího postupu můžete obnovit svazek ze zálohy.

#### <a name="prerequisites"></a>Požadavky
Pokud jste tak již neučinili, vytvořte svazek a skupiny svazků a pak odstranit svazek. Ve výchozím nastavení StorSimple Snapshot Manageru zálohuje svazku před umožňující ho odstraníme. Toto opatření můžete zabránit ztrátě dat, pokud svazek je odstraněn omylem nebo pokud je potřeba obnovit z jakéhokoli důvodu data. 

StorSimple Snapshot Manager zobrazí následující zprávu při vytváření preventivní zálohy.

![Zpráva automatické snímku](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nelze odstranit svazek, který je součástí skupiny svazků. Možnost odstranění je k dispozici. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Chcete-li obnovit svazek
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru. 
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu, rozbalte skupinu svazku a pak klikněte na tlačítko **místní snímky** nebo **cloudové snímky**. Zobrazí se seznam snímků zálohy v **výsledky** podokně.
3. Najít zálohu, kterou chcete obnovit, klikněte pravým tlačítkem a pak klikněte na tlačítko **obnovení**.
   
    ![Obnovit zálohování katalogu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stránce potvrzení zkontrolujte podrobnosti, typ **potvrdit**a potom klikněte na tlačítko **OK**. StorSimple Snapshot Manageru používá zálohu k obnovení svazku.
   
    ![Obnovení potvrzení](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Akce obnovení můžete sledovat, při jejím spuštění. V **oboru** podokně rozbalte **úlohy** uzlu a pak klikněte na tlačítko **systémem**. Zobrazí podrobnosti o úloze v **výsledky** podokně. Po dokončení úlohy obnovení, podrobnosti o úloze se přesunou **posledních 24 hodin** seznamu.

## <a name="clone-a-volume-or-volume-group"></a>Klonovat svazek nebo skupiny svazků
Použijte následující postup k vytvoření duplicitní (klonovat) svazku nebo skupiny svazků.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klonování svazku nebo skupiny svazků
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu, rozbalte skupinu svazku a pak klikněte na tlačítko **cloudové snímky**. Zobrazí seznam záloh v **výsledky** podokně.
3. Najít svazek nebo skupiny svazků, které chcete naklonovat, klikněte pravým tlačítkem na svazku nebo název skupiny svazků a klikněte na tlačítko **klonování**. **Klonování Cloudový snímek** zobrazí se dialogové okno.
   
    ![Klonovat snímek v cloudu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončení **klonování Cloudový snímek** dialogové okno následujícím způsobem: 
   
   1. V **název** textového pole zadejte název pro klonovaný svazek. Tento název se zobrazí v **svazky** uzlu. 
   2. (Volitelné) vyberte **jednotky**a potom z rozevíracího seznamu vyberte písmeno jednotky.
   3. (Volitelné) vyberte **složky (NTFS)** a zadejte cestu ke složce nebo klikněte na tlačítko Procházet a vyberte umístění složky. 
   4. Klikněte na možnost **Vytvořit**.
5. Po dokončení klonování se musí inicializovat klonovaný svazek. Spusťte správce serveru a pak spusťte správu disků. Podrobné pokyny najdete v tématu [připojit svazky](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci, svazek budou uvedené v části **svazky** uzlu **oboru** podokně. Pokud nevidíte svazku uvedené, aktualizujte seznam svazků (klikněte pravým tlačítkem myši **svazky** uzlu a pak klikněte na tlačítko **aktualizovat**).

## <a name="delete-a-backup"></a>Odstranit zálohu
Pomocí následujícího postupu můžete odstranit snímek z katalogu záloh. 

> [!NOTE]
> Odstraňuje se snímek odstraní zálohovaná data přidružená k snímku. Proces vyčištění dat z cloudu, ale může chvíli trvat.<br>


#### <a name="to-delete-a-backup"></a>Chcete-li odstranit zálohy
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu, rozbalte skupinu svazku a pak klikněte na tlačítko **místní snímky** nebo **cloudové snímky**. Seznam snímků, které se zobrazí v **výsledky** podokně.
3. Klikněte pravým tlačítkem na snímek, kterou chcete odstranit a potom klikněte na tlačítko **odstranit**.
4. Jakmile se zobrazí potvrzovací zpráva, klikněte na tlačítko **OK**.

## <a name="recover-a-file"></a>Obnovení souboru
Pokud soubor náhodně odstraní ze svazku, můžete obnovit soubor načítání, který předem data odstranění snímku, pomocí snímku na vytvoření klonu svazku a pak kopírování souboru z klonovaný svazek do původního svazku.

#### <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte aktuální záloha skupiny svazků. Odstraňte soubor uložený na jednom svazku v dané skupině svazku. Nakonec pomocí následujících kroků k obnovení odstraněného souboru ze zálohy. 

#### <a name="to-recover-a-deleted-file"></a>Obnovení odstraněného souboru
1. Klikněte na ikonu StorSimple Snapshot Manageru na ploše. Zobrazí se okno konzoly StorSimple Snapshot Manageru. 
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu a přejděte na snímek, který obsahuje odstraněný soubor. Obvykle byste měli vybrat snímek, který byl vytvořen před odstranění.
3. Najít svazek, který chcete naklonovat, klikněte pravým tlačítkem a klikněte na tlačítko **klonování**. **Klonování Cloudový snímek** zobrazí se dialogové okno.
   
    ![Klonovat snímek v cloudu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončení **klonování Cloudový snímek** dialogové okno následujícím způsobem: 
   
   1. V **název** textového pole zadejte název pro klonovaný svazek. Tento název se zobrazí v **svazky** uzlu. 
   2. (Volitelné) Vyberte **jednotky**a potom z rozevíracího seznamu vyberte písmeno jednotky. 
   3. (Volitelné) Vyberte **složky (NTFS)** a zadejte cestu ke složce, nebo klikněte na tlačítko **Procházet** a vyberte umístění složky. 
   4. Klikněte na možnost **Vytvořit**. 
5. Po dokončení klonování se musí inicializovat klonovaný svazek. Spusťte správce serveru a pak spusťte správu disků. Podrobné pokyny najdete v tématu [připojit svazky](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci, svazek budou uvedené v části **svazky** uzlu **oboru** podokně. 
   
    Pokud nevidíte svazku uvedené, aktualizujte seznam svazků (klikněte pravým tlačítkem myši **svazky** uzlu a pak klikněte na tlačítko **aktualizovat**).
6. Otevřete složku systému souborů NTFS, který obsahuje klonovaný svazek, rozbalte **svazky** uzlu a pak otevřete klonovaný svazek. Vyhledejte soubor, který chcete obnovit a zkopírujte ho do primárního svazku.
7. Po obnovení souboru můžete odstranit složku systému souborů NTFS, která obsahuje klonovaný svazek.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Obnovení databáze StorSimple Snapshot Manageru
Na hostitelském počítači byste měli pravidelně zálohovat databázi StorSimple Snapshot Manageru. Pokud dojde k havárii nebo hostitelský počítač z nějakého důvodu selže, ji můžete obnovit ze zálohy. Vytvoření zálohy databáze, který provádí ručně.

#### <a name="to-back-up-and-restore-the-database"></a>K zálohování a obnovení databáze
1. Zastavte službu Microsoft StorSimple pro správu:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** okna, vyberte **službě pro správu Microsoft StorSimple**.
   4. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **zastavit službu**.
2. Na hostitelském počítači přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData je skrytá složka.
   > 
   > 
3. Najít soubor XML katalogu, zkopírujte soubor a kopii uložit na bezpečném místě nebo v cloudu. Pokud z hostitelů selže, můžete použít tento záložní soubor můžete obnovit zásady zálohování, které jste vytvořili ve StorSimple Snapshot Manageru.
   
    ![Azure StorSimple katalog záloh souboru](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple pro správu: 
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** okna, vyberte **službě pro správu Microsoft StorSimple**.
   3. V pravém podokně v části **službě pro správu Microsoft StorSimple**, klikněte na tlačítko **restartujte službu**.
5. Na hostitelském počítači přejděte na C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Odstranit soubor XML katalogu a nahraďte verzi zálohy, kterou jste vytvořili. 
7. Klepněte na ikonu klasické pracovní plochy Snapshot Manageru zařízení StorSimple Snapshot Manageru zařízení StorSimple spusťte. 

## <a name="next-steps"></a>Další postup
* Další informace o [pomocí StorSimple Snapshot Manager ke správě vašeho řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Další informace o [StorSimple Snapshot Manageru úloh a pracovních postupů](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

