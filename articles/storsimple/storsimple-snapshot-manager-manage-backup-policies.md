---
title: Zásady zálohování StorSimple Snapshot Manageru | Dokumentace Microsoftu
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manageru k vytvoření a Správa zásad zálohování, které ovládací prvek naplánovaných záloh.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 7dac26b058b959011e38b4373369b8a1115d2705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303221"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Vytvoření a Správa zásad zálohování pomocí StorSimple Snapshot Manageru
## <a name="overview"></a>Přehled
Zásady zálohování vytvoří plán pro zálohování dat svazek, místně nebo v cloudu. Při vytváření zásady zálohování, můžete také zadat zásady uchovávání informací. (Můžete uchovávat maximálně 64 snímky.) Další informace o zásady zálohování, naleznete v tématu [zálohování typy](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) v [řady StorSimple 8000 series: řešení hybridního cloudového](storsimple-overview.md).

Tento kurz vysvětluje následující postupy:

* Vytvoření zásady zálohování
* Upravit zásady zálohování
* Odstraňování zásady zálohování

## <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování
Pomocí následujícího postupu vytvořte nové zásady zálohování.

#### <a name="to-create-a-backup-policy"></a>Chcete-li vytvořit zásadu zálohování
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte pravým tlačítkem na **zásady zálohování**a klikněte na tlačítko **vytvořit zásadu zálohování**.

    ![Vytvoření zásady zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **Vytvořit zásadu** zobrazí se dialogové okno.

    ![Vytvoření zásad – karta Obecné](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na **Obecné** kartu, zadejte následující informace:

   1. V **název** textového pole zadejte název zásady.
   2. V **skupiny svazků** textového pole, zadejte název skupiny svazků přidružených k zásadě.
   3. Vyberte buď **místní snímek** nebo **cloudových snímků**.
   4. Vyberte počet snímků, pokud chcete zachovat. Pokud vyberete **všechny**, 64 snímky budou zachovány (maximum).
4. Klikněte na tlačítko **plán** kartu.

    ![Vytvoření zásad – Záložka plánu](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na **plán** kartu, zadejte následující informace:

   1. Klikněte na tlačítko **povolit** zaškrtávací políčko k naplánování dalším zálohování.
   2. V části **nastavení**vyberte **jednou**, **denní**, **týdenní**, nebo **měsíční**.
   3. V **Start** textové pole, klikněte na ikonu kalendáře a vyberte počáteční datum.
   4. V části **Upřesnit nastavení**, lze nastavit volitelný plány opakování a koncové datum.
   5. Klikněte na **OK**.

Po vytvoření zásady zálohování se zobrazí následující informace v **výsledky** podokna:

* **Název** – název zásady zálohování.
* **Typ** – místní snímek nebo cloudový snímek.
* **Skupiny svazků** – skupiny svazků přidružených k zásadě.
* **Uchování** – počet snímků uchovávají; maximální počet je 64.
* **Vytvoření** – datum, které tato zásada byla vytvořena.
* **Povolené** – Určuje, zda zásady je aktuálně v platnosti: **Hodnota TRUE** označuje, že je v platnosti; **False** označuje, že není platná.

## <a name="edit-a-backup-policy"></a>Upravit zásady zálohování
Pomocí následujícího postupu upravit existující zásadu zálohování.

#### <a name="to-edit-a-backup-policy"></a>Chcete-li upravit zásady zálohování
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte na tlačítko **zásady zálohování** uzlu. Všechny zásady zálohování se zobrazí v **výsledky** podokně.
3. Klikněte pravým tlačítkem na zásadu, kterou chcete upravit a potom klikněte na tlačítko **upravit**.

    ![Upravit zásady zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Když **vytvořit zásadu** okna se zobrazí, zadejte změny a pak klikněte na tlačítko **OK**.

## <a name="delete-a-backup-policy"></a>Odstraňování zásady zálohování
Pomocí následujícího postupu odstranit zásady zálohování.

#### <a name="to-delete-a-backup-policy"></a>Chcete-li odstranit zásady zálohování
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně klikněte na tlačítko **zásady zálohování** uzlu. Všechny zásady zálohování se zobrazí v **výsledky** podokně.
3. Klikněte pravým tlačítkem na zásadu zálohování, který chcete odstranit a potom klikněte na tlačítko **odstranit**.
4. Jakmile se zobrazí potvrzovací zpráva, klikněte na tlačítko **Ano**.

    ![Zásady zálohování potvrzení odstranění](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manageru zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manageru k zobrazení a Správa úloh zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).
