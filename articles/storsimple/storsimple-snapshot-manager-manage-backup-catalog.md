---
title: Katalog zálohování Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manager můžete zobrazit a spravovat zálohy katalogu.
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
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876709"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Použití StorSimple Snapshot Manager ke správě katalogu zálohování

## <a name="overview"></a>Přehled
Primární funkce služby StorSimple Snapshot Manager je umožnit vám umožní vytvořit konzistentní s aplikací záložní kopie svazky zařízení StorSimple ve formě snímky. Snímky jsou pak uvedeny v souboru XML s názvem *zálohování katalogu*. Zálohování katalogu Uspořádá snímky skupinou svazku a poté podle místní snímek nebo cloudový snímek.

Tento kurz popisuje, jak můžete použít **katalog zálohování** uzlu dokončit následující úlohy:

* Obnovení svazku
* Klonování svazku nebo svazku skupiny
* Odstranit zálohy
* Obnovit soubor
* Obnovit databázi Snapshot Manager zařízení Storsimple

Můžete zobrazit katalog záloh rozšířením **zálohování katalogu** uzel v **oboru** podokně a pak rozšiřování skupiny svazku.

* Pokud kliknete na název skupiny svazku **výsledky** podokně se zobrazuje číslo místní snímky a cloudových snímků, které jsou k dispozici pro skupinu svazku. 
* Pokud kliknete na tlačítko **místní snímek** nebo **Cloudový snímek**, **výsledky** podokně se zobrazují následující informace o jednotlivých snímek zálohy (v závislosti na vaší  **Zobrazit** nastavení):
  
  * **Název** – čas pořízení snímku.
  * **Typ** – jestli se jedná o místní snímek nebo cloudový snímek.
  * **Vlastník** – vlastníka obsahu. 
  * **K dispozici** – jestli snímku je nyní k dispozici. **Hodnota TRUE,** označuje, že snímek je k dispozici a může být obnovena; **False** označuje, že snímek je již k dispozici. 
  * **Importovat** – jestli zálohování byly naimportovány. **Hodnota TRUE,** označuje, že zálohy byla naimportována ze služby StorSimple Manager zařízení v době zařízení bylo nakonfigurováno v zařízení StorSimple Snapshot Manager; **False** znamená, že nebyl importován, ale byla vytvořena ve Snapshot Manageru zařízení StorSimple. (Umožňuje snadno identifikovat skupinu importované svazku protože příponu je přidána, která určuje zařízení, ze kterého byl importován skupině svazku.)
    
    ![Zálohování katalogu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Pokud rozbalíte **místní snímek** nebo **Cloudový snímek**a potom klikněte na název jednotlivých snímku **výsledky** podokně se zobrazují následující informace o snímku jestli jste vybrali:
  
  * **Název** – svazek, identifikuje písmeno jednotky. 
  * **Místní název** – místní název disku (Pokud je k dispozici). 
  * **Zařízení** – název zařízení, na kterém se nachází svazku. 
  * **K dispozici** – jestli snímku je nyní k dispozici. **Hodnota TRUE,** označuje, že snímek je k dispozici a může být obnovena; **False** označuje, že snímek je již k dispozici. 

## <a name="restore-a-volume"></a>Obnovení svazku
Následujícím postupem obnovit ze zálohy na svazek.

#### <a name="prerequisites"></a>Požadavky
Pokud jste tak již neučinili, vytvořte svazek a svazek skupiny a pak odstraňte svazku. Ve výchozím nastavení StorSimple Snapshot Manager zálohuje svazek před umožňující pro odstranění. Toto opatření můžete zabránit ztrátě dat. Pokud se svazek neúmyslně odstraní nebo pokud dat je potřeba obnovit z jakéhokoli důvodu. 

StorSimple Snapshot Manager vytvoří preventivní zálohování, zobrazí se následující zpráva.

![Zpráva automatické snímku](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nelze odstranit svazek, který je součástí skupiny svazku. Možnost odstranění je k dispozici. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>K obnovení svazku
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager. 
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu, rozbalte skupinu svazku a pak klikněte na tlačítko **místní snímky** nebo **cloudových snímků**. Zobrazí se seznam snímků zálohy v **výsledky** podokně.
3. Najít zálohu, kterou chcete obnovit, klikněte pravým tlačítkem a pak klikněte na tlačítko **obnovení**.
   
    ![Obnovit zálohování katalogu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stránce potvrzení zkontrolujte podrobnosti, typ **potvrdit**a potom klikněte na **OK**. StorSimple Snapshot Manager používá zálohu k obnovení svazku.
   
    ![Obnovit zprávy s potvrzením](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Akce obnovení můžete sledovat při jeho spuštění. V **oboru** podokně rozbalte **úlohy** uzel a pak klikněte na tlačítko **systémem**. Podrobnosti úlohy se zobrazí v **výsledky** podokně. Po dokončení úlohy obnovení, jsou podrobnosti úlohy přeneseny na **posledních 24 hodin** seznamu.

## <a name="clone-a-volume-or-volume-group"></a>Klonování svazku nebo svazku skupiny
Použijte následující postup k vytvoření duplicitní (klonování) svazek nebo svazek skupiny.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klonování svazku nebo svazku skupiny
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu, rozbalte skupinu svazku a pak klikněte na tlačítko **cloudových snímků**. V se zobrazí seznam záloh **výsledky** podokně.
3. Najít svazku nebo svazku skupiny, který chcete vytvořit kopii, klikněte pravým tlačítkem na svazek nebo název skupiny svazku a klikněte na **klon**. **Klon Cloudový snímek** zobrazí se dialogové okno.
   
    ![Klonování cloudový snímek](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončení **klon Cloudový snímek** dialogové okno následujícím způsobem: 
   
   1. V **název** textového pole zadejte název klonovaného svazku. Tento název se zobrazí v **svazky** uzlu. 
   2. (Volitelné) vyberte **jednotky**a potom v rozevíracím seznamu vyberte písmeno jednotky.
   3. (Volitelné) vyberte **složky (NTFS)** a zadejte cestu ke složce nebo klikněte na tlačítko Procházet a vyberte umístění složky. 
   4. Klikněte na možnost **Vytvořit**.
5. Po dokončení procesu klonování je třeba inicializovat klonovaný svazku. Spusťte správce serveru a pak spusťte nástroj Správa disků. Podrobné pokyny najdete v tématu [připojit svazky](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci, zobrazí se svazku v části **svazky** uzlu **oboru** podokně. Pokud se svazek uvedené nezobrazí, aktualizujte seznam svazků (klikněte pravým tlačítkem myši **svazky** uzel a pak klikněte na tlačítko **aktualizovat**).

## <a name="delete-a-backup"></a>Odstranit zálohy
Pomocí následujícího postupu můžete odstranit snímek z katalogu služby zálohování. 

> [!NOTE]
> Odstranění snímku odstraní zálohovaná data související s snímku. Proces vyčištění dat z cloudu však může chvíli trvat.<br>


#### <a name="to-delete-a-backup"></a>Chcete-li odstranit zálohy
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu, rozbalte skupinu svazku a pak klikněte na tlačítko **místní snímky** nebo **cloudových snímků**. Zobrazí se seznam snímky v **výsledky** podokně.
3. Klikněte pravým tlačítkem na snímek, který se má odstranit, a pak klikněte na **odstranit**.
4. Jakmile se zobrazí zpráva o potvrzení, klikněte na tlačítko **OK**.

## <a name="recover-a-file"></a>Obnovit soubor
Pokud soubor je omylem odstraněn ze svazku, můžete obnovit soubor načítání snímek, který předem data odstranění, pomocí snímku naklonoval svazku a poté kopírování souboru z klonovaného svazku do původního svazku.

#### <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte aktuální záloha skupiny svazku. Potom odstraňte soubor uložené v jednom ze svazků v dané skupině svazku. Nakonec použijte následující postup k odstraněnému souboru obnovení ze zálohy. 

#### <a name="to-recover-a-deleted-file"></a>Obnovení odstraněného souboru
1. Klikněte na ikonu Snapshot Manager zařízení StorSimple na ploše. Zobrazí se v okně konzoly StorSimple Snapshot Manager. 
2. V **oboru** podokně rozbalte **katalog zálohování** uzlu a přejděte na snímek, který obsahuje soubor odstraněné. Obvykle byste měli vybrat snímek, který byl vytvořen těsně před odstranění.
3. Najít svazek, který chcete vytvořit kopii, klikněte pravým tlačítkem a klikněte na tlačítko **klon**. **Klon Cloudový snímek** zobrazí se dialogové okno.
   
    ![Klonování cloudový snímek](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Dokončení **klon Cloudový snímek** dialogové okno následujícím způsobem: 
   
   1. V **název** textového pole zadejte název klonovaného svazku. Tento název se zobrazí v **svazky** uzlu. 
   2. (Volitelné) Vyberte **jednotky**a potom v rozevíracím seznamu vyberte písmeno jednotky. 
   3. (Volitelné) Vyberte **složky (NTFS)** a zadejte cestu ke složce, nebo klikněte na tlačítko **Procházet** a vyberte umístění složky. 
   4. Klikněte na možnost **Vytvořit**. 
5. Po dokončení procesu klonování je třeba inicializovat klonovaný svazku. Spusťte správce serveru a pak spusťte nástroj Správa disků. Podrobné pokyny najdete v tématu [připojit svazky](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po inicializaci, zobrazí se svazku v části **svazky** uzlu **oboru** podokně. 
   
    Pokud se svazek uvedené nezobrazí, aktualizujte seznam svazků (klikněte pravým tlačítkem myši **svazky** uzel a pak klikněte na tlačítko **aktualizovat**).
6. Otevřete složku systému souborů NTFS, která obsahuje klonovaný svazku, rozbalte **svazky** uzel a potom otevřete klonovaný svazku. Vyhledejte soubor, který chcete obnovit a zkopírujte jej do primární svazku.
7. Po obnovení souboru, můžete odstranit složku systému souborů NTFS, která obsahuje klonovaný svazku.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Obnovit databázi Snapshot Manager zařízení StorSimple
Pravidelně zálohujte databázi Snapshot Manager zařízení StorSimple na hostitelském počítači. Pokud dojde k havárii nebo hostitelský počítač z nějakého důvodu selže, ho můžete obnovit ze zálohy. Vytvoření zálohy databáze je ruční proces.

#### <a name="to-back-up-and-restore-the-database"></a>K zálohování a obnovení databáze
1. Zastavte službu Microsoft StorSimple správy:
   
   1. Spusťte správce serveru.
   2. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   3. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   4. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **zastavit službu**.
2. Na hostitelském počítači vyhledejte C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > ProgramData je skrytá složka.
   > 
   > 
3. Najít soubor XML katalogu, zkopírujte soubor a uložení kopie do bezpečného umístění nebo v cloudu. Pokud hostitel selže, můžete tento záložní soubor můžete obnovit zásady zálohování, které jste vytvořili v zařízení StorSimple Snapshot Manager.
   
    ![Soubor zálohy katalogu Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Restartujte službu Microsoft StorSimple Management: 
   
   1. Na řídicím panelu Správce serveru na **nástroje** nabídce vyberte možnost **služby**.
   2. Na **služby** vyberte **služba správy zařízení StorSimple Microsoft**.
   3. V pravém podokně v části **služba správy zařízení StorSimple Microsoft**, klikněte na tlačítko **restartujte službu**.
5. Na hostitelském počítači vyhledejte C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Odstraňte soubor XML katalogu a nahraďte ji metodou verze zálohy, který jste vytvořili. 
7. Klikněte na ikonu plochy Snapshot Manager zařízení StorSimple Snapshot Manager zařízení StorSimple spusťte. 

## <a name="next-steps"></a>Další kroky
* Další informace o [použití ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Další informace o [úlohy StorSimple Snapshot Manager a pracovních postupů](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

