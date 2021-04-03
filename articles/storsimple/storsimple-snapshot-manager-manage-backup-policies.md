---
title: Zásady zálohování StorSimple Snapshot Manager | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in StorSimple Snapshot Manager MMC vytvářet a spravovat zásady zálohování, které řídí naplánované zálohy.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054938"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Použití Snapshot Manager StorSimple k vytváření a správě zásad zálohování
## <a name="overview"></a>Přehled
Zásada zálohování vytvoří plán pro zálohování dat svazku místně nebo v cloudu. Když vytváříte zásady zálohování, můžete taky zadat zásady uchovávání informací. (Můžete uchovávat maximálně 64 snímků.) Další informace o zásadách zálohování najdete v tématu [typy zálohování](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) v [StorSimple 8000 series: hybridní cloudové řešení](storsimple-overview.md).

Tento kurz vysvětluje následující postupy:

* Vytvoření zásad zálohování
* Úprava zásady zálohování
* Odstranění zásady zálohování

## <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování
Pomocí následujícího postupu vytvořte nové zásady zálohování.

#### <a name="to-create-a-backup-policy"></a>Vytvoření zásady zálohování
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte pravým tlačítkem na **zásady zálohování** a pak klikněte na **vytvořit zásadu zálohování**.

    ![Vytvoření zásad zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Zobrazí se dialogové okno **vytvořit zásadu** .

    ![Vytvoření zásady – karta Obecné](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na kartě **Obecné** zadejte následující informace:

   1. Do textového pole **název** zadejte název zásady.
   2. Do textového pole **skupina svazků** zadejte název skupiny svazků přidružené k zásadě.
   3. Vyberte buď **místní snímek** , nebo **snímek v cloudu**.
   4. Vyberte počet snímků, které mají být zachovány. Pokud vyberete možnost **všechny**, snímky 64 se zachovají (maximum).
4. Klikněte na kartu **Plán** .

    ![Vytvoření zásady – karta plán](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na kartě **plán** vyplňte následující informace:

   1. Chcete-li naplánovat další zálohování, klikněte na zaškrtávací políčko **Povolit** .
   2. V části nastavení vyberte **jednu z** **možností**:, **denně**, **týdně** nebo **měsíčně**.
   3. V textovém poli **začátek** klikněte na ikonu kalendáře a vyberte počáteční datum.
   4. V části **Upřesnit nastavení** můžete nastavit volitelné plány opakování a koncové datum.
   5. Klikněte na **OK**.

Po vytvoření zásady zálohování se v podokně **výsledků** zobrazí následující informace:

* **Název** – název zásady zálohování.
* **Typ** – místní snímek nebo cloudový snímek.
* **Skupina svazků** – skupina svazků přidružená k zásadě.
* **Uchování** – počet uchovávaných snímků; maximální hodnota je 64.
* **Vytvořeno** – datum, kdy se tato zásada vytvořila.
* **Povoleno** – zda je zásada aktuálně platná: **hodnota true** označuje, že je v platnosti. **Hodnota false** znamená, že není platná.

## <a name="edit-a-backup-policy"></a>Úprava zásady zálohování
Pomocí následujícího postupu můžete upravit existující zásady zálohování.

#### <a name="to-edit-a-backup-policy"></a>Postup úpravy zásady zálohování
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na uzel **zásady zálohování** . Všechny zásady zálohování se zobrazí v podokně **výsledků** .
3. Klikněte pravým tlačítkem na zásadu, kterou chcete upravit, a pak klikněte na **Upravit**.

    ![Úprava zásady zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Po zobrazení okna **vytvořit zásadu** zadejte změny a pak klikněte na **OK**.

## <a name="delete-a-backup-policy"></a>Odstranění zásady zálohování
Zásadu zálohování odstraníte pomocí následujícího postupu.

#### <a name="to-delete-a-backup-policy"></a>Odstranění zásady zálohování
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** klikněte na uzel **zásady zálohování** . Všechny zásady zálohování se zobrazí v podokně **výsledků** .
3. Klikněte pravým tlačítkem na zásadu zálohování, kterou chcete odstranit, a pak klikněte na **Odstranit**.
4. Jakmile se zobrazí potvrzovací zpráva, klikněte na tlačítko **Ano**.

    ![Odstranit potvrzení zásady zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Snapshot Manager StorSimple k zobrazení a správě úloh zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).
