---
title: Kurz zálohování virtuálního pole Microsoft Azure StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak zálohovat sdílené složky a svazky virtuálního pole StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60581301"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Zálohování sdílených složek nebo svazků ve virtuálním poli StorSimple

## <a name="overview"></a>Přehled

Virtuální pole StorSimple je místní virtuální zařízení s hybridním cloudovým úložištěm, které lze nakonfigurovat jako souborový server nebo server iSCSI. Virtuální pole umožňuje uživateli vytvořit naplánované a ruční zálohování všech sdílených složek nebo svazků v zařízení. Při konfiguraci jako souborový server také umožňuje obnovení na úrovni položky. Tento kurz popisuje, jak vytvořit naplánované a ruční zálohování a provést obnovení na úrovni položky k obnovení odstraněného souboru ve virtuálním poli.

Tento kurz se vztahuje pouze na virtuální pole StorSimple. Informace o řadě 8000 naleznete v části [Vytvoření zálohy pro zařízení řady 8000.](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Zálohování sdílených složek a svazků

Zálohy poskytují ochranu v okamžiku, zlepšují obnovitelnost a minimalizují dobu obnovení sdílených složek a svazků. Sdílenou složku nebo svazek můžete zálohovat na zařízení StorSimple dvěma způsoby: **Naplánované** nebo **Ruční**. Každá z metod je popsána v následujících částech.

## <a name="change-the-backup-start-time"></a>Změna počátečního času zálohování

> [!NOTE]
> V této verzi jsou naplánované zálohy vytvořeny výchozí zásadou, která se spouští denně v určený čas a zálohuje všechny sdílené složky nebo svazky v zařízení. V tuto chvíli není možné vytvořit vlastní zásady pro naplánované zálohy.


Virtuální pole StorSimple má výchozí zásady zálohování, které začíná v určitou denní dobu (22:30) a zálohuje všechny sdílené složky nebo svazky v zařízení jednou denně. Můžete změnit čas spuštění zálohy, ale frekvenci a uchovávání (která určuje počet záloh, které chcete zachovat) nelze změnit. Během těchto záloh je zálohováno celé virtuální zařízení. To může potenciálně ovlivnit výkon zařízení a ovlivnit úlohy nasazené na zařízení. Proto doporučujeme naplánovat tyto zálohy pro špičku.

 Chcete-li změnit výchozí čas zahájení zálohování, proveďte na [webu Azure Portal](https://portal.azure.com/)následující kroky .

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Změna počátečního času výchozí zásady zálohování

1. Přejděte na **zařízení**. Zobrazí se seznam zařízení registrovaných ve službě StorSimple Device Manager. 
   
    ![přejít na zařízení](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Vyberte a klikněte na zařízení. Zobrazí se okno **Nastavení.** Přejděte na **spravovat zásady zálohování >**.
   
    ![vyberte zařízení](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. V okně **zásad zálohování** je výchozí čas zahájení 22:30. Můžete určit nový čas zahájení denního plánu v časovém pásmu zařízení.
   
    ![přechod na zásady zálohování](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klikněte na **Uložit**.

### <a name="take-a-manual-backup"></a>Ruční zálohování

Kromě naplánovaných záloh můžete kdykoli provést ruční (na vyžádání) zálohování dat zařízení.

#### <a name="to-create-a-manual-backup"></a>Ruční vytvoření zálohy

1. Přejděte na **zařízení**. Vyberte zařízení a klikněte pravým tlačítkem myši **...** zcela vpravo ve vybraném řádku. V místní nabídce vyberte **Převzít zálohu**.
   
    ![navigace pro zálohování](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. V okně **Převzít zálohu** klikněte na **Take backup**. Tím se zálohují všechny sdílené složky na souborovém serveru nebo všechny svazky na serveru iSCSI. 
   
    ![spuštění zálohy](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Spustí se záloha na vyžádání a uvidíte, že byla spuštěna úloha zálohování.
   
    ![spuštění zálohy](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Po úspěšném dokončení úlohy budete znovu upozorněni. Proces zálohování se spustí.
   
    ![vytvořena úloha zálohování](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Chcete-li sledovat průběh zálohování a podívat se na podrobnosti o úloze, klikněte na oznámení. Tím přejdete na **podrobnosti o úloze**.
   
     ![podrobnosti o úloze zálohování](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po dokončení zálohování přejděte do **katalogu zálohování služby Management >**. Na zařízení se zobrazí snímek cloudu všech sdílených složek (nebo svazků).
   
    ![Dokončená záloha](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Zobrazit existující zálohy
Chcete-li zobrazit existující zálohy, proveďte následující kroky na webu Azure Portal.

#### <a name="to-view-existing-backups"></a>Zobrazení existujících záloh

1. Přejděte na **okno Zařízení.** Vyberte a klikněte na zařízení. V okně **Nastavení** přejděte do **katalogu zálohování > správy**.
   
    ![Přechod do katalogu záloh](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Zadejte následující kritéria, která mají být použita pro filtrování:
   
   - **Časové rozpětí** – může být **past 1 hodina**, Past **24 hodin**, Past 7 **days**, **Past 30 days**, Past **year**, a **Custom date**.
    
   - **Zařízení** – vyberte ze seznamu souborových serverů nebo serverů iSCSI registrovaných ve službě StorSimple Device Manager.
   
   - **Inicializováno** – může být automaticky **naplánováno** (pomocí zásad zálohování) nebo **ručně** iniciováno (vámi).
   
     ![Filtrování záloh](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klikněte na **Použít**. Filtrovaný seznam záloh se zobrazí v okně **katalogu zálohování.** Poznámka: v daném okamžiku lze zobrazit pouze 100 záložních prvků.
   
    ![Aktualizovaný katalog záloh](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Další kroky

Další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

