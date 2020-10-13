---
title: Kurz zálohování virtuálních polí Microsoft Azure StorSimple | Microsoft Docs
description: Vytvořte naplánované a ruční zálohy pro Microsoft Azure StorSimple Virtual Array a proveďte obnovení na úrovni položek, abyste obnovili odstraněný soubor ve virtuálním poli.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5c49a46dfcd97f5feb43b0b910476ef3ad6f402a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742243"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Zálohování sdílených složek nebo svazků ve virtuálním poli StorSimple

## <a name="overview"></a>Přehled

Virtuální pole StorSimple je hybridní cloudové úložiště, které se dá nakonfigurovat jako souborový server nebo server iSCSI. Virtuální pole umožňuje uživateli vytvářet naplánované a ruční zálohy všech sdílených složek nebo svazků v zařízení. Když je nakonfigurovaný jako souborový server, umožňuje taky obnovení na úrovni položek. V tomto kurzu se dozvíte, jak vytvořit naplánované a ruční zálohy a provést obnovení na úrovni položky k obnovení odstraněného souboru ve virtuálním poli.

Tento kurz se vztahuje pouze na virtuální pole StorSimple. Informace o řadě 8000 najdete na webu [vytvoření zálohy pro zařízení 8000 series](storsimple-manage-backup-policies-u2.md) .

## <a name="back-up-shares-and-volumes"></a>Zálohování sdílených složek a svazků

Zálohy poskytují ochranu k určitému bodu v čase, zlepšují možnosti obnovení a minimalizují dobu obnovení sdílených složek a svazků. Sdílenou složku nebo svazek můžete na zařízení StorSimple zálohovat dvěma způsoby: **naplánované** nebo **Ruční**. Jednotlivé metody jsou popsány v následujících částech.

## <a name="change-the-backup-start-time"></a>Změna času zahájení zálohování

> [!NOTE]
> V této verzi se naplánovaná zálohování vytvoří pomocí výchozí zásady, která běží denně v zadanou dobu, a zálohuje všechny sdílené složky nebo svazky na zařízení. V tuto chvíli není možné vytvářet vlastní zásady pro plánované zálohy.


Vaše virtuální pole StorSimple má výchozí zásadu zálohování, která začíná v zadaném dni (22:30) a zálohuje všechny sdílené složky nebo svazky na zařízení jednou denně. Můžete změnit čas, kdy se zálohování spustí, ale frekvence a uchování (která určuje počet uchovávaných záloh) nelze změnit. Během těchto záloh se zálohuje celé virtuální zařízení. To může mít vliv na výkon zařízení a vliv na úlohy nasazené na zařízení. Proto doporučujeme, abyste tyto zálohy naplánovali na dobu mimo špičku.

 Chcete-li změnit výchozí čas zahájení zálohování, proveďte následující kroky v [Azure Portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Změna počátečního času pro výchozí zásady zálohování

1. Přejít na **zařízení**. Zobrazí se seznam zařízení zaregistrovaných ve službě StorSimple Device Manager. 
   
    ![Přejít na zařízení](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Vyberte zařízení a klikněte na něj. Zobrazí se okno **Nastavení** . Přejít na **správu zásad zálohování >**.
   
    ![Vyberte své zařízení.](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. V okně **zásady zálohování** je výchozí čas začátku 22:30. Můžete zadat nový čas zahájení denního plánu v časovém pásmu zařízení.
   
    ![Přejít k zásadám zálohování](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Klikněte na **Uložit**.

### <a name="take-a-manual-backup"></a>Provedení ručního zálohování

Kromě naplánovaných záloh můžete kdykoli ručně (na vyžádání) vytvořit zálohu dat zařízení.

#### <a name="to-create-a-manual-backup"></a>Ruční vytvoření zálohy

1. Přejít na **zařízení**. Vyberte zařízení a klikněte pravým tlačítkem myši na **...** úplně vpravo na vybraném řádku. V místní nabídce vyberte možnost **provést zálohování**.
   
    ![přejděte k provedení zálohování.](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. V okně **provést zálohování** klikněte na možnost **provést zálohování**. Tím dojde k zálohování všech sdílených složek na souborovém serveru nebo na všech svazcích serveru iSCSI. 
   
    ![spouští se zálohování](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Spustí se zálohování na vyžádání a uvidíte, že se spustila úloha zálohování.
   
    ![zálohování počínaje 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Po úspěšném dokončení úlohy budete znovu upozorněni. Pak se spustí proces zálohování.
   
    ![Úloha zálohování se vytvořila.](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Pokud chcete sledovat průběh zálohování a podívat se na Podrobnosti úlohy, klikněte na oznámení. Tím přejdete k  **podrobnostem úlohy**.
   
     ![Podrobnosti úlohy zálohování](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Po dokončení zálohování přejdete do části **správa > zálohovat katalog**. Na vašem zařízení se zobrazí snímek cloudu se všemi sdílenými složkami (nebo svazky).
   
    ![Zálohování se dokončilo.](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Zobrazit existující zálohy
Chcete-li zobrazit existující zálohy, proveďte následující kroky v Azure Portal.

#### <a name="to-view-existing-backups"></a>Zobrazení existujících záloh

1. Otevřete okno **zařízení** . Vyberte zařízení a klikněte na něj. V okně **Nastavení** otevřete **katalog Správa > zálohování**.
   
    ![Přejít ke katalogu záloh](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Zadejte následující kritéria, která budou použita pro filtrování:
   
   - **Časový rozsah** – může být **za 1 hodinu**, **posledních 24 hodin**, **posledních 7 dní**, **posledních 30 dní**, **minulý rok**a **vlastní datum**.
    
   - **Zařízení** – vyberte ze seznamu souborové servery nebo servery iSCSI, které jsou zaregistrované ve službě StorSimple Device Manager.
   
   - **Iniciované** – dá se automaticky **naplánovat** (zásady zálohování) nebo **ručně** iniciované (vámi).
   
     ![Filtrovat zálohy](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Klikněte na **Použít**. Filtrovaný seznam záloh se zobrazí v okně **katalog záloh** . Poznámka: v daný okamžik se dají zobrazit jenom 100 elementy zálohy.
   
    ![Aktualizovaný katalog záloh](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

