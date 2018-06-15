---
title: Zásady zálohování Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Popisuje, jak pomocí modulu snap-in konzoly MMC StorSimple Snapshot Manager vytvořit a spravovat zásady zálohování, které řídí naplánovaných záloh.
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
ms.openlocfilehash: 218c89e403673c16c72da95aa2c1d685bbed5a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875470"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Pomocí StorSimple Snapshot Manager vytvořit a spravovat zásady zálohování
## <a name="overview"></a>Přehled
Zásady zálohování vytvoří plán pro zálohování dat svazku místně nebo v cloudu. Když vytvoříte zásadu zálohování, můžete také zadat zásady uchovávání informací. (Můžete uchovávat maximálně 64 snímky.) Další informace o zásady zálohování najdete v tématu [zálohování typy](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) v [řady StorSimple 8000: hybridní cloudové řešení](storsimple-overview.md).

Tento kurz vysvětluje postup:

* Vytvořit zásady zálohování
* Upravit zásady zálohování
* Odstranit zásady zálohování

## <a name="create-a-backup-policy"></a>Vytvořit zásady zálohování
Použijte následující postup k vytvoření nové zásady zálohování.

#### <a name="to-create-a-backup-policy"></a>Chcete-li vytvořit zásady zálohování
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte pravým tlačítkem na **zásady zálohování**a klikněte na tlačítko **vytvořit zásadu zálohování**.

    ![Vytvořit zásady zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **Vytvořit zásadu** zobrazí se dialogové okno.

    ![Vytvoření zásady – karta Obecné](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Na **Obecné** proveďte následující informace:

   1. V **název** textového pole zadejte název pro tuto zásadu.
   2. V **svazku skupiny** textového pole, zadejte název skupiny svazku přidružených k zásadě.
   3. Vyberte buď **místní snímek** nebo **cloudových snímků**.
   4. Vyberte počet snímků, které chcete zachovat. Pokud vyberete **všechny**, 64 snímky bude zachována (maximum).
4. Klikněte **plán** kartě.

    ![Vytvoření zásady – karta plán](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Na **plán** proveďte následující informace:

   1. Klikněte **povolit** políčko naplánovat další zálohování.
   2. V části **nastavení**, vyberte **jednou**, **denní**, **týdenní**, nebo **měsíční**.
   3. V **spustit** textového pole, klikněte na ikonu kalendáři a vyberte počáteční datum.
   4. V části **Upřesnit nastavení**, můžete nastavit volitelné opakování plány a koncové datum.
   5. Klikněte na **OK**.

Po vytvoření zásady zálohování, tyto informace se zobrazí v **výsledky** podokně:

* **Název** – název zásady zálohování.
* **Typ** – místní snímek nebo cloudový snímek.
* **Svazek skupiny** – skupině svazku přidružených k zásadě.
* **Uchování** – počet snímků uchovávají; maximální počet je 64.
* **Vytvořit** – datum, tato zásada byla vytvořena.
* **Povolit** – jestli zásady je aktuálně v platnosti: **True** označuje, že je v platnosti; **False** signalizuje, že není platný.

## <a name="edit-a-backup-policy"></a>Upravit zásady zálohování
Chcete-li upravit existující zásady zálohování použijte následující postup.

#### <a name="to-edit-a-backup-policy"></a>Chcete-li upravit zásady zálohování
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte **zásady zálohování** uzlu. Zobrazí všechny zásady zálohování v **výsledky** podokně.
3. Klikněte pravým tlačítkem na zásadu, kterou chcete upravit a pak klikněte na tlačítko **upravit**.

    ![Upravit zásady zálohování](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Když **vytvořit zásadu** okno se zobrazí, zadejte změny a pak klikněte na tlačítko **OK**.

## <a name="delete-a-backup-policy"></a>Odstranit zásady zálohování
Pomocí následujícího postupu můžete odstranit zásady zálohování.

#### <a name="to-delete-a-backup-policy"></a>Chcete-li odstranit zásady zálohování
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně klikněte **zásady zálohování** uzlu. Zobrazí všechny zásady zálohování v **výsledky** podokně.
3. Klikněte pravým tlačítkem na zásadu zálohování, který chcete odstranit a potom klikněte na **odstranit**.
4. Jakmile se zobrazí zpráva o potvrzení, klikněte na tlačítko **Ano**.

    ![Odstranit zásady zálohování potvrzení](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [pomocí StorSimple Snapshot Manager můžete zobrazit a spravovat úlohy zálohování](storsimple-snapshot-manager-manage-backup-jobs.md).
