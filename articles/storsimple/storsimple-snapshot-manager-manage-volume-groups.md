---
title: Skupiny svazek StorSimple Snapshot Manager | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in konzoly MMC StorSimple Snapshot Manager vytvořit a spravovat skupiny svazku.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Použít k vytvoření a Správa skupin svazku Snapshot Manager zařízení StorSimple
## <a name="overview"></a>Přehled
Můžete použít **svazku skupiny** uzlu **oboru** přiřadit svazek skupiny, zobrazení informací o skupině svazku, svazky plánování zálohování a upravit skupiny pro svazek.

Svazek skupiny jsou fondy související svazků, které se používá k zajištění, že zálohy jsou konzistentní s aplikací. Další informace najdete v tématu [svazky a svazek skupiny](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) a [integrace s Windows služby Stínová kopie svazku](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Všechny svazky ve skupině svazku musí pocházet ze zprostředkovatele služeb jeden cloud.
> * Když konfigurujete skupiny svazku, nemíchat sdílených svazcích clusteru (CSV) a jiný-sdílené svazky clusteru ve stejné skupině svazku. Snapshot Manager zařízení StorSimple nepodporuje směs sdílené svazky clusteru a sdílených svazků clusteru ve stejném snímku.

![Uzel skupiny svazku](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Obrázek 1: Uzel StorSimple Snapshot Manager svazku skupiny** 

Tento kurz vysvětluje, jak můžete pomocí StorSimple Snapshot Manager do:

* Zobrazení informací o svazku skupin
* Vytvořte skupinu svazku
* Zálohování svazku skupiny
* Úprava skupiny svazku
* Odstranit skupinu svazku

Všechny tyto akce jsou také k dispozici na **akce** podokně.

## <a name="view-volume-groups"></a>Zobrazit svazku skupiny
Pokud kliknete **svazku skupiny** uzlu, **výsledky** podokně zobrazí následující informace o každé skupiny svazku, v závislosti na vybrané sloupce provedete. (Ve sloupcích v **výsledky** podokně se dají konfigurovat. Klikněte pravým tlačítkem myši **svazky** uzlu, vyberte **zobrazení**a potom vyberte **přidat nebo odebrat sloupce**.)

| Sloupec výsledků | Popis |
|:--- |:--- |
| Název |**Název** sloupec obsahuje název skupiny svazku. |
| Aplikace |**Aplikace** sloupci se zobrazuje počet zapisovače VSS aktuálně nainstalovaná a spuštěná v hostiteli systému Windows. |
| Vybráno |**Vybrané** sloupci se zobrazuje počet svazků, které jsou obsaženy ve skupině svazku. Nula (0) znamená, že žádná aplikace přidružené svazky ve skupině svazku. |
| Naimportováno |**Importovaný** sloupci se zobrazuje počet importovaných svazků. Pokud nastavíte hodnotu **True**, v tomto sloupci označuje, že svazek skupiny naimportované z portálu Azure a v zařízení StorSimple Snapshot Manager nebyla vytvořena. |

> [!NOTE]
> Skupiny svazek StorSimple Snapshot Manager se zobrazuje také na **zásady zálohování** na portálu Azure.
> 
> 

## <a name="create-a-volume-group"></a>Vytvořte skupinu svazku
Pomocí následujícího postupu můžete vytvořit skupinu na svazku.

#### <a name="to-create-a-volume-group"></a>Vytvoření skupiny svazku
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte pravým tlačítkem na **svazku skupiny**a potom klikněte na **vytvořit skupinu svazku**.
   
    ![Vytvoření svazku skupiny](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    **Vytvořte skupinu svazku** zobrazí se dialogové okno.
   
    ![Vytvoření skupiny dialogu svazku](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Zadejte následující informace:
   
   1. V **název** zadejte jedinečný název pro novou skupinu svazku.
   2. V **aplikace** pole, vyberte aplikace, které jsou přidružené svazky, které budete přidávat do skupiny svazku.
      
       **Aplikace** seznamy pro je povoleno pouze aplikace, které používají svazky zařízení StorSimple a mají zapisovače služby Stínová kopie svazku. Zapisovač VSS je povoleno pouze v případě, že jsou všechny svazky, které si je vědoma zapisovač svazky zařízení StorSimple. Pokud pole aplikace je prázdný, nenainstalují se žádné aplikace, které používají Azure StorSimple svazky a podporovaná zapisovače služby Stínová kopie svazku. (V současné době Azure StorSimple podporuje Microsoft Exchange a SQL Server.) Další informace o zapisovače VSS najdete v tématu [integrace s Windows služby Stínová kopie svazku](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Pokud vyberete aplikace, automaticky se vyberou všechny svazky, které jsou s ním spojená. Naopak, pokud jste vybrali svazky, které jsou přidruženy k určité aplikaci, aplikace se automaticky vybere v **aplikace** pole. 
   3. V **svazky** vyberte svazky zařízení StorSimple pro přidání do skupiny svazku. 
      
      * Můžete zahrnout svazků s jednoho nebo více oddílů. (Více svazků oddílu může být dynamické disky nebo základní disky s více oddílů.) Svazek, který obsahuje více oddílů je považována za jednu jednotku. V důsledku toho pokud přidáte do skupiny svazku pouze jeden oddíl, všechny ostatní oddíly se automaticky přidají do této skupiny svazku ve stejnou dobu. Po přidání více oddílů svazku do skupiny svazek, svazek více oddílů nadále jednal jako na jednu jednotku.
      * Můžete vytvořit prázdnou svazku skupiny není přiřazením žádné svazky k nim. 
      * Nemíchat sdílených svazcích clusteru (CSV) a jiný-sdílené svazky clusteru ve stejné skupině svazku. Snapshot Manager zařízení StorSimple nepodporuje směs Sdílené svazky clusteru a bez Sdílené svazky clusteru ve stejném snímku.
4. Klikněte na tlačítko **OK** se uložit skupinu svazku.

## <a name="back-up-a-volume-group"></a>Zálohování svazku skupiny
Použijte následující postup k zálohování svazku skupiny.

#### <a name="to-back-up-a-volume-group"></a>Zálohování svazku skupiny
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **svazku skupiny** uzel, klikněte pravým tlačítkem na název skupiny svazku a pak klikněte na tlačítko **provést zálohování**.
   
    ![Zálohování svazku skupiny okamžitě](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. V **provést zálohování** dialogové okno, vyberte **místní snímek** nebo **Cloudový snímek**a potom klikněte na **vytvořit**.
   
    ![Trvat zálohování dialogové okno](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Potvrďte, zda je spuštěna zálohování, rozbalte **úlohy** uzel a pak klikněte na tlačítko **systémem**. Zálohování by měl být uvedený.
5. Chcete-li zobrazit dokončené snímku, rozbalte **katalog zálohování** uzlu, rozbalte název skupiny svazku a pak klikněte na tlačítko **místní snímek** nebo **Cloudový snímek**. Zálohování se objeví, pokud byl úspěšně dokončen.

## <a name="edit-a-volume-group"></a>Úprava skupiny svazku
Pomocí následujícího postupu upravit skupinu svazku.

#### <a name="to-edit-a-volume-group"></a>Chcete-li upravit skupinu svazku
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **svazku skupiny** uzel, klikněte pravým tlačítkem na název skupiny svazku a pak klikněte na tlačítko **upravit**.
3. ** Vytvořte skupinu svazku ** zobrazí se dialogové okno. Můžete změnit **název**, **aplikace**, a **svazky** položky.
4. Klikněte na tlačítko **OK** a uložte změny.

## <a name="delete-a-volume-group"></a>Odstranit skupinu svazku
Pomocí následujícího postupu můžete odstranit skupinu svazku. 

> [!WARNING]
> To také odstraní všechny zálohy přidružených ke skupině svazku.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Chcete odstranit skupinu svazku
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **svazku skupiny** uzel, klikněte pravým tlačítkem na název skupiny svazku a pak klikněte na tlačítko **odstranit**.
3. **Odstranit skupinu svazku** zobrazí se dialogové okno. Typ **potvrdit** textového pole a pak klikněte na **OK**.
   
    Skupiny odstranila svazek zmizí ze seznamu v **výsledky** podokně a všechny zálohy, které jsou přidružené k této skupině svazku se odstraní z katalogu služby zálohování.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manager vytvořit a spravovat zásady zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

