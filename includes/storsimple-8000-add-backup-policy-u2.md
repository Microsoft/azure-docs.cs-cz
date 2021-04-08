---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552830"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Přidání zásady zálohování StorSimple

1. Přejděte ke svému zařízení StorSimple a klikněte na **Zásady zálohování**.

2. V okně **Zásady zálohování** klikněte na panelu příkazů na **Přidat zásadu**.
   
    ![Přidání zásady zálohování](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. V okně **Vytvořit zásadu zálohování** proveďte následující kroky:
   
   1. Pole **Vybrat zařízení** se automaticky vyplní podle zařízení, které jste vybrali.
   
   2. Zadejte **název zásady** zálohování, který obsahuje 3 až 150 znaků. Po vytvoření už zásadu není možné přejmenovat.
       
   3. Pokud chcete k této zásadě zálohování přiřadit svazky, vyberte **Přidat svazky** a potom v tabulkovém výpisu svazků zaškrtnutím políček přiřaďte této zásadě zálohování jeden nebo několik svazků.

       ![Přidání zásady zálohování 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Pokud chcete pro tuto zásadu zálohování nadefinovat plán, klikněte na **První plán** a potom upravte následující parametry:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Přidání zásady zálohování 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Jako **Typ snímku** vyberte **Cloud** nebo **Místní**.

       2. Určete frekvenci záloh (zadejte číslo a potom v rozevíracím seznamu zvolte **dny** nebo **týdny** .

       3. Zadejte plán uchovávání.

       4. Zadejte čas a datum zahájení platnosti zásady zálohování.

       5. Kliknutím na **OK** nadefinujte plán.

   5. Kliknutím na **Vytvořit** vytvořte zásadu zálohování.
   
   6. Po vytvoření zásady zálohování se zobrazí oznámení. Nově přidaná zásada se zobrazí v tabulkovém zobrazení v okně **Zásady zálohování**.

       ![Přidání zásady zálohování 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
