---
title: Skupiny svazků Správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje použití modulu snap-in Správce snímků StorSimple MMC k vytvoření a správě skupin svazků.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 5198729cf96fb48c5dcd05096c04ea4d77c26de5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931496"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Vytvoření a správa skupin svazků pomocí Správce snímků StorSimple
## <a name="overview"></a>Přehled
Uzel **Skupiny svazků** v podokně **Obor** můžete použít k přiřazení svazků skupinám svazků, zobrazení informací o skupině svazků, plánování zálohování a úpravám skupin svazků.

Skupiny svazků jsou fondy souvisejících svazků, které se používají k zajištění toho, aby zálohy byly konzistentní s aplikací. Další informace naleznete v [tématu Svazky a skupiny svazků](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) a [Integrace se službou Stínové kopie svazků systému Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Všechny svazky ve skupině svazků musí pocházet od jednoho poskytovatele cloudových služeb.
> * Při konfiguraci skupin svazků nekombinujte svazky sdílené clusterem (CSV) a jiné než CSV ve stejné skupině svazků. Správce snímků StorSimple nepodporuje kombinaci csv a necsvs ve stejném snímku.

![Uzel skupin y svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Obrázek 1: Uzel Skupinskupinskupiny skaný správce snímků StorSimple** 

Tento kurz vysvětluje, jak můžete použít Správce snímků StorSimple k:

* Zobrazení informací o skupinách svazků
* Vytvoření skupiny svazků
* Zálohování skupiny svazků
* Úprava skupiny svazků
* Odstranění skupiny svazků

Všechny tyto akce jsou také k dispozici v podokně **Akce.**

## <a name="view-volume-groups"></a>Zobrazit skupiny svazků
Pokud klepnete na uzel **Skupiny svazků,** v podokně **Výsledky** se zobrazí následující informace o jednotlivých skupinách svazků v závislosti na výběru sloupců, který provedete. (Sloupce v podokně **Výsledky** jsou konfigurovatelné. Klepněte pravým tlačítkem myši na uzel **Svazky,** vyberte **Zobrazit**a pak vyberte **Přidat nebo odebrat sloupce**.)

| Sloupec Výsledky | Popis |
|:--- |:--- |
| Name (Název) |Sloupec **Název** obsahuje název skupiny svazků. |
| Aplikace |Sloupec **Aplikace** zobrazuje počet autorů služby VSS, kteří jsou aktuálně nainstalováni a spuštěni na hostiteli systému Windows. |
| Vybráno |Sloupec **Vybrané** zobrazuje počet svazků, které jsou obsaženy ve skupině svazků. Nula (0) označuje, že žádná aplikace není přidružena ke svazkům ve skupině svazků. |
| Importované |Sloupec **Importovaný** zobrazuje počet importovaných svazků. Pokud je nastavena na **True**, tento sloupec označuje, že skupina svazků byla importována z portálu Azure a nebyla vytvořena ve Správci snímků StorSimple. |

> [!NOTE]
> Skupiny svazků Správce snímků StorSimple se taky zobrazují na kartě **Zásady zálohování** na webu Azure Portal.
> 
> 

## <a name="create-a-volume-group"></a>Vytvoření skupiny svazků
K vytvoření skupiny svazků použijte následující postup.

#### <a name="to-create-a-volume-group"></a>Vytvoření skupiny svazků
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na **položku Skupiny svazků**a potom klepněte na příkaz **Vytvořit skupinu svazků**.
   
    ![Vytvořit skupinu svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Zobrazí se dialogové okno **Vytvořit skupinu svazků.**
   
    ![Dialogové okno Vytvořit skupinu svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Zadejte následující informace:
   
   1. Do pole **Název** zadejte jedinečný název nové skupiny svazků.
   2. V poli **Aplikace** vyberte aplikace přidružené ke svazkům, které budete přidávat do skupiny svazků.
      
       V poli **Aplikace** jsou uvedeny pouze aplikace, které používají svazky StorSimple a mají pro ně povolené zapisovače VSS. Zapisovač VSS je povolenpouze v případě, že všechny svazky, které je autor vědom jsou Svazky StorSimple. Pokud je pole Aplikace prázdné, pak nejsou nainstalovány žádné aplikace, které používají svazky Azure StorSimple a mají podporované zapisovače VSS. (V současné době Azure StorSimple podporuje Microsoft Exchange a SQL Server.) Další informace o zapisovačích služby VSS naleznete [v tématu Integrace se službou Stínové kopírování svazků systému Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Pokud vyberete aplikaci, budou automaticky vybrány všechny svazky, které jsou k ní přidruženy. Naopak pokud vyberete svazky přidružené k určité aplikaci, aplikace je automaticky vybrána v poli **Aplikace.** 
   3. V poli **Svazky** vyberte StorSimple svazky, které chcete přidat do skupiny svazků. 
      
      * Můžete zahrnout svazky s jedním nebo více oddíly. (Více svazků oddílů může být dynamické disky nebo základní disky s více oddíly.) Svazek, který obsahuje více oddílů, je považován za jednu jednotku. V důsledku toho pokud přidáte pouze jeden z oddílů do skupiny svazků, všechny ostatní oddíly jsou automaticky přidány do této skupiny svazků ve stejnou dobu. Po přidání více svazků oddílů do skupiny svazků bude svazek více oddílů nadále považován za jednu jednotku.
      * Prázdné skupiny svazků můžete vytvořit tak, že k nim nepřiřadíte žádné svazky. 
      * Nekombinujte svazky sdílené clusterem (CSV) a necsv ve stejné skupině svazků. Správce snímků StorSimple nepodporuje kombinaci svazků CSV a svazků bez CSV ve stejném snímku.
4. Klepnutím na **tlačítko OK** skupinu svazků uložte.

## <a name="back-up-a-volume-group"></a>Zálohování skupiny svazků
K zálohování skupiny svazků použijte následující postup.

#### <a name="to-back-up-a-volume-group"></a>Zálohování skupiny svazků
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Skupiny svazků,** klikněte pravým tlačítkem myši na název skupiny svazků a potom klikněte na **příkaz Převzít zálohu**.
   
    ![Okamžitě zálohovat skupinu svazků](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. V dialogovém okně **Převzít zálohu** vyberte **položku Místní snímek** nebo **Snímek cloudu**a klepněte na tlačítko **Vytvořit**.
   
    ![Dialogové okno Vzít zálohu](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Chcete-li ověřit, zda je záloha spuštěna, rozbalte uzel **Úlohy** a klepněte na tlačítko **Spuštěno**. Záloha by měla být uvedena.
5. Chcete-li zobrazit dokončený snímek, rozbalte uzel **Katalog zálohování,** rozbalte název skupiny svazků a klepněte na **položku Místní snímek** nebo **Snímek cloudu**. Záloha bude uvedena, pokud byla úspěšně dokončena.

## <a name="edit-a-volume-group"></a>Úprava skupiny svazků
K úpravě skupiny svazků použijte následující postup.

#### <a name="to-edit-a-volume-group"></a>Úprava skupiny svazků
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Skupiny svazků,** klikněte pravým tlačítkem myši na název skupiny svazků a potom klikněte na **Upravit**.
3. Zobrazí se dialogové okno **Vytvořit skupinu svazků **. Položky **Název**, **Aplikace**a **Svazky** můžete změnit.
4. Klikněte na tlačítko **OK** a uložte změny.

## <a name="delete-a-volume-group"></a>Odstranění skupiny svazků
Pomocí následujícího postupu odstraňte skupinu svazků. 

> [!WARNING]
> Tím také odstraníte všechny zálohy přidružené ke skupině svazků.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Odstranění skupiny svazků
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Skupiny svazků,** klikněte pravým tlačítkem myši na název skupiny svazků a potom klikněte na **Odstranit**.
3. Zobrazí se dialogové okno **Odstranit skupinu svazků.** Do textového pole **zadejte Potvrdit** a klepněte na tlačítko **OK**.
   
    Odstraněná skupina svazků zmizí ze seznamu v podokně **Výsledky** a všechny zálohy přidružené k této skupině svazků budou odstraněny z katalogu záloh.

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Správce snímků StorSimple k vytváření a správě zásad zálohování](storsimple-snapshot-manager-manage-backup-policies.md).

