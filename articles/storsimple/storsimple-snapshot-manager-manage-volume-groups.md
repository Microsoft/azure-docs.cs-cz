---
title: StorSimple Snapshot Manager skupiny svazků | Microsoft Docs
description: V této části najdete popis postupu při vytváření a správě skupin svazků pomocí modulu snap-in StorSimple Snapshot Manager MMC.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: df2d74701e21c3773d96564f1b06d80ddb9fce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209215"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Použití Snapshot Manager StorSimple k vytváření a správě skupin svazků
## <a name="overview"></a>Přehled
Uzel **skupiny svazků** v podokně **oboru** můžete použít k přiřazení svazků ke skupinám svazků, zobrazení informací o skupině svazků, plánování záloh a úpravám skupin svazků.

Skupiny svazků jsou fondy souvisejících svazků, pomocí kterých je zajištěno, že zálohy jsou konzistentní vzhledem k aplikacím. Další informace najdete v tématu [svazky a skupiny](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) svazků a [integrace s Windows služba Stínová kopie svazku](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Všechny svazky ve skupině svazků musí pocházet od jednoho poskytovatele cloudové služby.
> * Když konfigurujete skupiny svazků, Nekombinujte sdílené svazky clusteru (CSV) a jiné než CSV ve stejné skupině svazků. StorSimple Snapshot Manager nepodporuje kombinaci CSV a non-CSV ve stejném snímku.

![Uzel skupiny svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Obrázek 1: uzel StorSimple Snapshot Manager skupiny svazků** 

V tomto kurzu se dozvíte, jak můžete použít StorSimple Snapshot Manager k těmto akcím:

* Zobrazit informace o skupinách svazků
* Vytvoření skupiny svazků
* Zálohování skupiny svazků
* Úprava skupiny svazků
* Odstranění skupiny svazků

Všechny tyto akce jsou také k dispozici v podokně **Akce** .

## <a name="view-volume-groups"></a>Zobrazit skupiny svazků
Pokud kliknete na uzel **skupiny svazků** , v podokně **výsledků** se zobrazí následující informace o každé skupině svazků v závislosti na výběrech sloupců, které provedete. (Sloupce v podokně **výsledků** se dají konfigurovat. Klikněte pravým tlačítkem myši na uzel **svazky** , vyberte možnost **zobrazení** a pak vyberte možnost **Přidat nebo odebrat sloupce**.)

| Sloupec výsledků | Popis |
|:--- |:--- |
| Název |Sloupec **název** obsahuje název skupiny svazků. |
| Aplikace |Sloupec **aplikace** zobrazuje počet zapisovačů VSS aktuálně nainstalovaných a spuštěných na hostiteli Windows. |
| Vybráno |**Vybraný** sloupec zobrazuje počet svazků, které jsou obsaženy ve skupině svazků. Nula (0) znamená, že ke svazkům ve skupině svazků není přidružena žádná aplikace. |
| Dovážel |**Importovaný** sloupec zobrazuje počet importovaných svazků. Když se nastaví na **true**, v tomto sloupci se zobrazí zpráva o tom, že skupina svazků byla naimportována z Azure Portal a nebyla vytvořena v Snapshot Manager StorSimple. |

> [!NOTE]
> Skupiny svazků StorSimple Snapshot Manager se zobrazují také na kartě **zásady zálohování** v Azure Portal.
> 
> 

## <a name="create-a-volume-group"></a>Vytvoření skupiny svazků
Chcete-li vytvořit skupinu svazků, použijte následující postup.

#### <a name="to-create-a-volume-group"></a>Vytvoření skupiny svazků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na **skupiny svazků** a pak klikněte na **vytvořit skupinu svazků**.
   
    ![Vytvořit skupinu svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Zobrazí se dialogové okno **vytvořit skupinu svazků** .
   
    ![Dialog vytvořit skupinu svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Zadejte následující informace:
   
   1. Do pole **název** zadejte jedinečný název nové skupiny svazků.
   2. V poli **aplikace** vyberte aplikace přidružené ke svazkům, které budete přidávat do skupiny svazků.
      
       V poli **aplikace** jsou uvedeny pouze aplikace, které používají svazky StorSimple a mají pro ně zapisovače VSS povoleny. Zapisovač VSS je povolený jenom v případě, že všechny svazky, na kterých je zapisovatelný, jsou StorSimple svazky. Pokud je pole aplikace prázdné, jsou nainstalovány žádné aplikace, které nepoužívají svazky Azure StorSimple a podporované moduly pro zápisy služby VSS. (V současné době Azure StorSimple podporuje Microsoft Exchange a SQL Server.) Další informace o zapisovačích VSS najdete v tématu [integrace s Windows služba Stínová kopie svazku](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Když vyberete aplikaci, automaticky se vybere všechny svazky, které jsou k nim přidružené. Pokud naopak vyberete svazky přidružené ke konkrétní aplikaci, aplikace se automaticky vybere v poli **aplikace** . 
   3. V poli **svazky** vyberte svazky StorSimple, které chcete přidat do skupiny svazků. 
      
      * Můžete zahrnout svazky s jedním nebo více oddíly. (Více svazků oddílu může být dynamické disky nebo základní disky s více oddíly.) Svazek, který obsahuje více oddílů, se považuje za jednu jednotku. Pokud tedy do skupiny svazků přidáte pouze jeden z oddílů, všechny ostatní oddíly budou do této skupiny svazků automaticky přidány. Po přidání svazku s více oddíly do skupiny svazků bude i nadále zacházeno s více svazky oddílu jako s jednou jednotkou.
      * Můžete vytvořit prázdné skupiny svazků tím, že k nim nepřiřazují žádné svazky. 
      * Nepoužívejte kombinaci sdílených svazků clusteru (CSV) a non-CSV ve stejné skupině svazků. StorSimple Snapshot Manager nepodporuje kombinaci svazků sdíleného svazku clusteru a svazků jiných než CSV ve stejném snímku.
4. Kliknutím na tlačítko **OK** uložte skupinu svazků.

## <a name="back-up-a-volume-group"></a>Zálohování skupiny svazků
K zálohování skupiny svazků použijte následující postup.

#### <a name="to-back-up-a-volume-group"></a>Zálohování skupiny svazků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **skupiny svazků** , klikněte pravým tlačítkem na název skupiny svazků a pak klikněte na **vytvořit zálohu**.
   
    ![Zálohování skupiny svazků hned](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. V dialogovém okně **provést zálohování** vyberte **místní snímek** nebo **cloudový snímek** a potom klikněte na **vytvořit**.
   
    ![Dialogové okno pro zálohování](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Chcete-li ověřit, zda je zálohování spuštěno, rozbalte uzel **úlohy** a potom klikněte na možnost **spuštěno**. Záloha by měla být uvedena v seznamu.
5. Chcete-li zobrazit dokončený snímek, rozbalte uzel **Katalog zálohování** , rozbalte položku název skupiny svazků a potom klikněte na možnost **místní snímek** nebo **cloudový snímek**. Záloha bude uvedena v případě úspěšného dokončení.

## <a name="edit-a-volume-group"></a>Úprava skupiny svazků
Pro úpravu skupiny svazků použijte následující postup.

#### <a name="to-edit-a-volume-group"></a>Úprava skupiny svazků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **skupiny svazků** , klikněte pravým tlačítkem na název skupiny svazků a pak klikněte na **Upravit**.
3. Zobrazí se dialogové okno **vytvořit skupinu svazků** . Můžete změnit položky **název**, **aplikace** a **svazky** .
4. Klikněte na tlačítko **OK** a uložte změny.

## <a name="delete-a-volume-group"></a>Odstranění skupiny svazků
Skupinu svazků můžete odstranit pomocí následujícího postupu. 

> [!WARNING]
> Tím se odstraní také všechna zálohování přidružená ke skupině svazků.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Odstranění skupiny svazků
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **skupiny svazků** , klikněte pravým tlačítkem na název skupiny svazků a pak klikněte na **Odstranit**.
3. Zobrazí se dialogové okno **Odstranit skupinu svazků** . Do textového pole zadejte **Confirm** a pak klikněte na **OK**.
   
    Odstraněná skupina svazků zmizí ze seznamu v podokně **výsledků** a všechny zálohy, které jsou přidružené k této skupině svazků, se odstraní z katalogu záloh.

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Snapshot Manager StorSimple k vytváření a správě zásad zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

