---
title: StorSimple Snapshot Manageru skupin svazků | Dokumentace Microsoftu
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manageru k vytvoření a Správa skupin svazků.
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
ms.openlocfilehash: e84bc790ac577796e91be010deecc8c5cea1b010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303102"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Vytvoření a Správa skupin svazků pomocí StorSimple Snapshot Manageru
## <a name="overview"></a>Přehled
Můžete použít **skupin svazků** uzlu na **oboru** podoknem přiřadit svazky na svazku skupiny, zobrazení informací o skupině svazku naplánovat zálohování a upravit skupiny svazku.

Svazek skupiny jsou fondy související svazky používané k zajištění konzistentní zálohy. Další informace najdete v tématu [svazky a skupin svazků](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) a [integrace s Windows služby Stínová kopie svazku](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Všechny svazky ve skupině svazku musí pocházet z jednoho poskytovatele cloudových služeb.
> * Při konfiguraci skupin svazků Nekombinujte sdílené svazky clusteru (CSV) a jiné-sdílených svazků clusteru ve stejné skupině svazku. StorSimple Snapshot Manageru nepodporuje sdílené svazky clusteru i sdílených svazků clusteru ve stejné snímku.

![Uzel skupiny svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Obrázek 1: Uzel skupin StorSimple Snapshot Manager svazků** 

Tento kurz vysvětluje, jak můžete pomocí StorSimple Snapshot Manageru do:

* Zobrazení informací o svazku skupin
* Vytvoření skupiny svazků
* Zálohování skupiny svazků
* Upravit skupiny svazků
* Odstranění skupiny svazků

Všechny tyto akce jsou také k dispozici na **akce** podokně.

## <a name="view-volume-groups"></a>Zobrazení skupin svazků
Pokud kliknete **skupin svazků** uzlu, **výsledky** podokně se zobrazí následující informace o každé skupiny svazku, v závislosti na vybrané sloupce provedete. (Ve sloupcích v **výsledky** podokno se dají konfigurovat. Klikněte pravým tlačítkem myši **svazky** uzlu, vyberte **zobrazení**a pak vyberte **Přidat/odebrat sloupce**.)

| Sloupec výsledků | Popis |
|:--- |:--- |
| Name |**Název** sloupec obsahuje název skupiny svazků. |
| Aplikace |**Aplikací** sloupci se zobrazuje počet zapisovače VSS jsou aktuálně nainstalovaná a spuštěná na hostiteli s Windows. |
| Vybráno |**Vybrané** sloupci se zobrazuje počet svazků, které jsou obsaženy ve skupině svazku. Nula (0) znamená, že žádná aplikace přidružené svazky ve skupině svazku. |
| Naimportováno |**Importováno** sloupci se zobrazuje počet importovaných svazků. Pokud je nastavena na **True**, v tomto sloupci označuje, že skupiny svazků jsou naimportovaná z webu Azure portal a nebyl vytvořen StorSimple Snapshot Manageru. |

> [!NOTE]
> Skupiny svazek StorSimple Snapshot Manageru se zobrazují na **zásady zálohování** karta na portálu Azure portal.
> 
> 

## <a name="create-a-volume-group"></a>Vytvoření skupiny svazků
Pomocí následujícího postupu můžete vytvořit skupinu svazku.

#### <a name="to-create-a-volume-group"></a>Chcete-li vytvořit skupiny svazků
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte pravým tlačítkem na **skupin svazků**a potom klikněte na tlačítko **vytvořit skupiny svazků**.
   
    ![Vytvoření skupiny svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    **Vytvořte skupinu svazku** zobrazí se dialogové okno.
   
    ![Vytvoření dialogového okna skupiny svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Zadejte následující informace:
   
   1. V **název** zadejte jedinečný název pro novou skupinu svazku.
   2. V **aplikací** pole, vyberte aplikace, které jsou přidružené svazky, které přidáváte do skupiny svazku.
      
       **Aplikací** seznamy pouze aplikace, které používají svazky zařízení StorSimple a mají zapisovače VSS jsou povolené pro ně. Zapisovač VSS je povolená jenom v případě, že všechny svazky, které modul pro zápis je seznámen se svazky zařízení StorSimple. Pokud pole aplikace je prázdný, nenainstalují se žádné aplikace, které používají svazky zařízení StorSimple v Azure a máte podporovanou zapisovače VSS. (V současné době Azure StorSimple podporuje Microsoft Exchange a SQL Server.) Další informace o zapisovače VSS najdete v tématu [integrace s Windows služby Stínová kopie svazku](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Pokud vyberete aplikace, automaticky se vyberou všechny svazky, které s ním spojená. Naopak, pokud jste vybrali svazky přidružené k určité aplikace, aplikace je automaticky vybrána **aplikací** pole. 
   3. V **svazky** vyberte svazky zařízení StorSimple pro přidání do skupiny svazků. 
      
      * Můžete zahrnout svazků s jedním nebo několika oddílů. (Více svazků oddílů může být dynamické disky nebo základní disky s více oddílů.) Svazek, který obsahuje několik oddílů je považován za jednu jednotku. V důsledku toho pokud chcete přidat pouze jeden z oddílů na skupiny svazků, všechny ostatní oddíly jsou automaticky přidány do této skupiny svazku ve stejnou dobu. Po přidání více oddílů svazku do skupiny svazků více svazku oddílu i nadále být považován za jednu jednotku.
      * Můžete vytvořit prázdný svazek skupiny tak, že není k nim přiřadíte žádné svazky. 
      * Nekombinujte sdílené svazky clusteru (CSV) a jiné-sdílených svazků clusteru ve stejné skupině svazku. StorSimple Snapshot Manageru nepodporuje Sdílené svazky clusteru i bez Sdílené svazky clusteru ve stejné snímku.
4. Klikněte na tlačítko **OK** se uložit skupinu svazku.

## <a name="back-up-a-volume-group"></a>Zálohování skupiny svazků
Pomocí následujícího postupu zálohování skupiny svazků.

#### <a name="to-back-up-a-volume-group"></a>K zálohování skupiny svazků
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **skupin svazků** uzel, klikněte pravým tlačítkem na název skupiny svazků a pak klikněte na **provést zálohování**.
   
    ![Zálohovat hned skupiny svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. V **provést zálohování** dialogu **místní snímek** nebo **Cloudový snímek**a potom klikněte na tlačítko **vytvořit**.
   
    ![Provést zálohování dialogového okna](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Chcete-li ověřit, že zálohování běží, rozbalte **úlohy** uzlu a pak klikněte na tlačítko **systémem**. Záloha by měla být uvedená.
5. Chcete-li zobrazit dokončené snímku, rozbalte **katalog zálohování** uzlu, rozbalte název skupiny svazků a pak klikněte na tlačítko **místní snímek** nebo **Cloudový snímek**. Zálohování se zobrazí, pokud byl úspěšně dokončen.

## <a name="edit-a-volume-group"></a>Upravit skupiny svazků
Pomocí následujícího postupu upravit skupiny svazků.

#### <a name="to-edit-a-volume-group"></a>Chcete-li upravit skupiny svazků
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **skupin svazků** uzel, klikněte pravým tlačítkem na název skupiny svazků a pak klikněte na **upravit**.
3. ** Vytvořte skupinu svazku ** se zobrazí dialogové okno. Můžete změnit **název**, **aplikací**, a **svazky** položky.
4. Klikněte na tlačítko **OK** a uložte změny.

## <a name="delete-a-volume-group"></a>Odstranění skupiny svazků
Pomocí následujícího postupu odstranit skupiny svazků. 

> [!WARNING]
> Tím se odstraní také všechny zálohy, které jsou přidružené ke skupině svazku.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Chcete-li odstranit skupiny svazků
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **skupin svazků** uzel, klikněte pravým tlačítkem na název skupiny svazků a pak klikněte na **odstranit**.
3. **Odstranit skupiny svazků** zobrazí se dialogové okno. Typ **potvrdit** textového pole a pak klikněte na **OK**.
   
    Skupiny svazků odstraněné zmizí ze seznamu **výsledky** podokně a všechny zálohy, které jsou spojené s touto skupinou svazku se odstraní ze katalog záloh.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manageru zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manageru k vytvoření a Správa zásad zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

