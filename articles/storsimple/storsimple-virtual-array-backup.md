---
title: Kurz zálohování Microsoft Azure StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje, jak zálohovat StorSimple Virtual Array sdílených složek a svazků.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60581301"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Zálohování sdílených složek nebo svazků na StorSimple Virtual Array

## <a name="overview"></a>Přehled

StorSimple Virtual Array je hybridní cloudové úložiště v místním virtuální zařízení, které se dají konfigurovat jako souborový server nebo iSCSI server. Virtuální pole umožňuje uživateli vytvořit ruční a plánovaná zálohování sdílených složek nebo svazků na zařízení. Když nakonfigurovaný jako souborový server, umožňuje také obnovení na úrovni položky. Tento kurz popisuje, jak vytvořit ruční a plánovaná zálohování a obnovení na úrovni položek pro obnovení odstraněného souboru na vaše virtuální pole.

Tento kurz se vztahuje na virtuální pole StorSimple pouze. Informace o 8000 series, přejděte na [vytvoření zálohy pro zařízení 8000 series](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Zálohování sdílených složek a svazků

Zálohování poskytuje ochranu v bodu v čase, zlepšuje obnovení a minimalizovat dobu obnovení sdílené složky a svazky. Můžete zálohovat sdílené složky nebo svazku na zařízení StorSimple dvěma způsoby: **Naplánované** nebo **ruční**. Každá z metod jsou popsány v následujících částech.

## <a name="change-the-backup-start-time"></a>Změňte čas spuštění zálohování

> [!NOTE]
> V této verzi jsou naplánované zálohy vytvořené výchozí zásady, které spouští každý den v určenou dobu a zálohování sdílených složek nebo svazků na zařízení. Není možné vytvořit vlastní zásady pro naplánované zálohy v tuto chvíli.


StorSimple Virtual Array má výchozí zásady zálohování, který začíná na určitou dobu během dne (22:30) a vytvoří zálohu všech sdílených složek nebo svazků na zařízení jednou denně. Můžete změnit čas, kdy nelze změnit spuštěním zálohování, ale četnost a uchovávání dat (který určuje počet záloh uchovávat). Během tyto zálohy se zálohovat celé virtuální zařízení. To potenciálně může mít vliv na výkon zařízení a ovlivnit úlohy nasazené na zařízení. Proto doporučujeme, abyste naplánovali tyto zálohy pro hodiny mimo špičku.

 Chcete-li změnit výchozí čas spuštění zálohování, proveďte následující kroky v [webu Azure portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Chcete-li změnit čas zahájení pro výchozí zásady zálohování

1. Přejděte na **zařízení**. Zobrazí se seznam zařízení registrovaná ve službě Správce zařízení StorSimple. 
   
    ![přejděte na zařízení](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Vyberte a klikněte na vašem zařízení. **Nastavení** zobrazí se okno. Přejděte na **Správa > zásady zálohování**.
   
    ![Vyberte zařízení](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. V **zásady zálohování** okně výchozí počáteční čas je 22:30. Nový počáteční čas pro denní plán můžete zadat v časovém pásmu zařízení.
   
    ![přejděte na zásady zálohování](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klikněte na **Uložit**.

### <a name="take-a-manual-backup"></a>Proveďte ruční zálohování

Kromě plánovaných záloh můžete provést ruční (na vyžádání), které jsou zálohování dat zařízení v každém okamžiku.

#### <a name="to-create-a-manual-backup"></a>Ruční vytvoření zálohy

1. Přejděte na **zařízení**. Vyberte zařízení a klikněte pravým tlačítkem na **...**  úplně vpravo v vybraný řádek. V místní nabídce vyberte **vytvořit zálohu**.
   
    ![přejděte na vytvořit zálohu](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. V **vytvořit zálohu** okna, klikněte na tlačítko **vytvořit zálohu**. To bude zálohování sdílených složek na souborovém serveru nebo všechny svazky na vašem serveru iSCSI. 
   
    ![spuštění zálohování](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Spustí zálohu na vyžádání a uvidíte, že byla spuštěna úloha zálohování.
   
    ![spuštění zálohování](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Po úspěšném dokončení úlohy se zobrazí oznámení znovu. Pak spustí proces zálohování.
   
    ![Úloha zálohování vytvoří](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Chcete-li sledovat průběh zálohy a podívejte se na podrobnosti o úloze, klikněte na oznámení. Tím přejdete do **podrobnosti úlohy**.
   
     ![Podrobnosti úlohy zálohování](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Až se zálohování dokončí, přejděte do **správy > Katalog zálohování**. Cloudový snímek všechny sdílené složky (nebo svazky) se zobrazí na vašem zařízení.
   
    ![Dokončené zálohování](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Zobrazení existujících záloh
Chcete-li zobrazit existující zálohy, proveďte následující kroky na webu Azure Portal.

#### <a name="to-view-existing-backups"></a>Chcete-li zobrazit existující zálohy

1. Přejděte na **zařízení** okno. Vyberte a klikněte na vašem zařízení. V **nastavení** okno, přejděte na **správy > Katalog zálohování**.
   
    ![Přejděte do katalogu záloh](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Zadejte následující kritéria pro filtrování:
   
   - **Časový rozsah** – může být **poslední 1 hodinu**, **za posledních 24 hodin**, **posledních 7 dnů**, **za posledních 30 dnů**, **za poslední rok** , a **vlastní datum**.
    
   - **Zařízení** – vyberte ze seznamu souborových serverech nebo servery iSCSI registraci ve službě Správce zařízení StorSimple.
   
   - **Zahájené** – může být automaticky **naplánované** (podle zásadu zálohování) nebo **ručně** iniciovaných (vy).
   
     ![Filtrovat zálohy](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klikněte na tlačítko **Použít**. Filtrovaný seznam záloh se zobrazí v **katalog zálohování** okno. Poznámka: jediným 100 prvků zálohy lze zobrazit v daném okamžiku.
   
    ![Aktualizace katalogu záloh](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Další postup

Další informace o [Správa StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

