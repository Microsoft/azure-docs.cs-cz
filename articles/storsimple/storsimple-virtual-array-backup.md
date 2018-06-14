---
title: Kurz zálohování Microsoft Azure StorSimple virtuální pole | Microsoft Docs
description: Popisuje, jak vytvořit zálohu StorSimple virtuální pole složkami a svazky.
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
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875645"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Zálohování sdílené složky nebo svazky na pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Pole virtuální zařízení StorSimple je hybridní cloudové úložiště místní virtuální zařízení, které se dají konfigurovat jako souborový server nebo server se službou iSCSI. Pole virtuální umožňuje uživatelům vytvářet plánované a ruční zálohy všech sdílených složek nebo svazků v zařízení. Když nakonfigurovaný jako souborový server, také umožňuje obnovení na úrovni položek. Tento kurz popisuje, jak vytvářet zálohy plánované a ruční a proveďte obnovení na úrovni položek pro obnovení odstraněného souboru na vaše virtuální pole.

V tomto kurzu platí pro StorSimple virtuální pole pouze. Informace o řady 8000, přejděte na [vytvoření zálohy pro řady 8000 zařízení](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Zálohování sdílených složek a svazků

Zálohování poskytuje ochranu v okamžiku, zvyšují možnost a minimalizovat dobu obnovení sdílené složky a svazky. Můžete zálohovat na sdílené složky nebo svazku zařízení StorSimple dvěma způsoby: **naplánovaná** nebo **ruční**. Všechny metody popsané v následujících částech.

## <a name="change-the-backup-start-time"></a>Změňte čas spuštění zálohování

> [!NOTE]
> V této verzi jsou naplánované zálohy vytvořené pomocí výchozí zásadu, která spouští každý den v určitou dobu a zálohuje všechny sdílené složky nebo svazky na zařízení. Není možné vytvořit vlastní zásady pro naplánovaných záloh v tuto chvíli.


Pole virtuální zařízení StorSimple má výchozí zásady zálohování, který začíná v zadané době den (22:30) a zálohuje všechny sdílené složky nebo svazky na zařízení jednou denně. Můžete změnit čas, kdy zálohování se spustí, ale četnost a uchovávání dat (který určuje počet záloh chcete zachovat) nelze změnit. Během tyto zálohy je zálohovat celé virtuální zařízení. To může potenciálně ovlivnit výkon zařízení a ovlivnit úlohy nasazené na zařízení. Proto doporučujeme, abyste naplánovali tyto zálohy pro hodiny mimo špičku.

 Chcete-li změnit výchozí zálohování čas, proveďte následující kroky v [portál Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Chcete-li změnit čas zahájení pro výchozí zásady zálohování

1. Přejděte na **zařízení**. Zobrazí seznam zařízení zaregistrovaná s služby StorSimple Manager zařízení. 
   
    ![přejděte na zařízení](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Vyberte a klikněte na zařízení. **Nastavení** zobrazí se okno. Přejděte na **spravovat > zásady zálohování**.
   
    ![Vyberte příslušné zařízení.](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. V **zásady zálohování** okně výchozí čas spuštění je 22:30. Nové počáteční čas pro denní plán můžete zadat v zařízení časovém pásmu.
   
    ![Přejděte do zásady zálohování](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klikněte na **Uložit**.

### <a name="take-a-manual-backup"></a>Proveďte ruční zálohy

Kromě plánovaných záloh můžete provést ruční (na vyžádání), které jsou zálohování dat zařízení kdykoli.

#### <a name="to-create-a-manual-backup"></a>Ruční vytvoření zálohy

1. Přejděte na **zařízení**. Vyberte zařízení a klikněte pravým tlačítkem na **...**  na pravém v vybraný řádek. V místní nabídce vyberte **provést zálohování**.
   
    ![Přejděte do provést zálohování](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. V **provést zálohování** okně klikněte na tlačítko **provést zálohování**. To bude zálohovat všechny sdílené složky na souborovém serveru nebo všechny svazky na vašem serveru iSCSI. 
   
    ![spuštění zálohování](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Spustí zálohu na vyžádání a uvidíte, že byla spuštěna úloha zálohování.
   
    ![spuštění zálohování](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Když úloha úspěšně dokončí, upozornění se zobrazí znovu. Pak spustí proces zálohování.
   
    ![Vytvořit úlohu zálohování](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Chcete-li sledovat průběh zálohování a podívejte se na podrobnosti úlohy, kliknutím na oznámení. Tím přejdete **podrobnosti úlohy**.
   
     ![Podrobnosti úlohy zálohování.](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po dokončení zálohování přejděte na **správy > Katalog zálohování**. Zobrazí se cloudový snímek všechny sdílené složky (nebo svazky) ve vašem zařízení.
   
    ![Dokončené zálohování](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Zobrazit existující zálohy
Chcete-li zobrazit existující zálohy, proveďte následující kroky na portálu Azure.

#### <a name="to-view-existing-backups"></a>Chcete-li zobrazit existující zálohy

1. Přejděte na **zařízení** okno. Vyberte a klikněte na zařízení. V **nastavení** okno, přejděte na **správy > Katalog zálohování**.
   
    ![Přejděte do katalogu zálohy](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Zadejte následující kritéria, která má být použit pro filtrování:
   
    - **Čas rozsah** – může být **poslední 1 hodinu**, **za posledních 24 hodin**, **za posledních 7 dní**, **za posledních 30 dnů**, **za poslední rok**, a **vlastní datum**.
    
    - **Zařízení** – vyberte ze seznamu souborových serverů nebo serverů iSCSI zaregistrována služby StorSimple Manager zařízení.
   
    - **Iniciované** – lze automaticky **naplánovaná** (podle zásady zálohování) nebo **ručně** iniciovaná (můžete).
   
    ![Filtr zálohy](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klikněte na tlačítko **Použít**. Filtrovaný seznam zálohy se zobrazí v **katalog zálohování** okno. Poznámka: pouze 100 zálohování elementy lze zobrazit v daném okamžiku.
   
    ![Aktualizované zálohování katalogu](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Další kroky

Další informace o [Správa pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

