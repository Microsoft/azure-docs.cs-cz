---
title: Zásady zálohování Správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí modulu snap-in Správce snímků StorSimple mmc vytvořit a spravovat zásady zálohování, které řídí naplánované zálohy.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933364"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Vytvoření a správa zásad zálohování pomocí správce snímků StorSimple
## <a name="overview"></a>Přehled
Zásady zálohování vytvoří plán pro zálohování dat svazku místně nebo v cloudu. Při vytváření zásad zálohování můžete také zadat zásady uchovávání informací. (Můžete zachovat maximálně 64 snímků.) Další informace o zásadách zálohování naleznete v [tématu Typy zálohování](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) v [řadě StorSimple 8000: hybridní cloudové řešení](storsimple-overview.md).

Tento kurz vysvětluje následující postupy:

* Vytvoření zásad zálohování
* Úprava zásad zálohování
* Odstranění zásad zálohování

## <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování
Pomocí následujícího postupu vytvořte novou zásadu zálohování.

#### <a name="to-create-a-backup-policy"></a>Vytvoření zásad zálohování
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klepněte pravým tlačítkem myši na **položku Zásady zálohování**a klepněte na příkaz **Vytvořit zásady zálohování**.

    ![Vytvoření zásad zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Zobrazí se dialogové okno **Vytvořit zásadu.**

    ![Vytvořit zásadu – karta Obecné](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na kartě **Obecné** vyplňte následující informace:

   1. Do textového pole **Název** zadejte název zásady.
   2. Do textového pole **Skupina svazků** zadejte název skupiny svazků přidružené k této zásadě.
   3. Vyberte místní **snímek** nebo **snímek cloudu**.
   4. Vyberte počet snímků, které chcete zachovat. Pokud vyberete **vše**, 64 snímků zůstane zachováno (maximum).
4. Klikněte na kartu **Plán** .

    ![Vytvořit zásadu – naplánovat kartu](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na kartě **Plán** vyplňte následující informace:

   1. Kliknutím na políčko **Povolit** naplánujete další zálohování.
   2. V části **Nastavení**vyberte **Možnost Jednou ,** **Denně**, **Týdně**nebo **Měsíčně**.
   3. V textovém poli **Start** klikněte na ikonu kalendáře a vyberte počáteční datum.
   4. V části **Upřesnit nastavení**můžete nastavit volitelné plány opakování a koncové datum.
   5. Klikněte na tlačítko **OK**.

Po vytvoření zásady zálohování se v podokně **Výsledky** zobrazí následující informace:

* **Název** – název zásady zálohování.
* **Typ** – místní snímek nebo snímek cloudu.
* **Skupina svazků** – skupina svazků přidružená k zásadě.
* **Uchovávání** – počet snímků zachována; maximální hodnota je 64.
* **Vytvořeno** – datum vytvoření této zásady.
* **Povoleno** – zda je zásada aktuálně v platnosti: **True** označuje, že je v platnosti; **False** označuje, že není v platnosti.

## <a name="edit-a-backup-policy"></a>Úprava zásad zálohování
Pomocí následujícího postupu upravte existující zásady zálohování.

#### <a name="to-edit-a-backup-policy"></a>Úprava zásad zálohování
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klikněte na uzel **Zásady zálohování.** Všechny zásady zálohování se zobrazí v podokně **Výsledky.**
3. Klikněte pravým tlačítkem myši na zásadu, kterou chcete upravit, a potom klikněte na **Upravit**.

    ![Úprava zásad zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Po zobrazení okna **Vytvořit zásadu** zadejte změny a klepněte na tlačítko **OK**.

## <a name="delete-a-backup-policy"></a>Odstranění zásad zálohování
Pomocí následujícího postupu odstraňte zásady zálohování.

#### <a name="to-delete-a-backup-policy"></a>Odstranění zásad zálohování
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** klikněte na uzel **Zásady zálohování.** Všechny zásady zálohování se zobrazí v podokně **Výsledky.**
3. Klikněte pravým tlačítkem myši na zásadu zálohování, kterou chcete odstranit, a potom klikněte na **příkaz Odstranit**.
4. Po zobrazíní potvrzovací zpráva klepněte na tlačítko **Ano**.

    ![Odstranit potvrzení zásad zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Správce snímků StorSimple k zobrazení a správě úloh zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).
