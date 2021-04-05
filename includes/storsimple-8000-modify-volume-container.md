---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545295"
---
> [!NOTE] 
> Po vytvoření nelze změnit nastavení šifrování a přihlašovací údaje účtu úložiště přidružené k kontejneru svazků.

#### <a name="to-modify-a-volume-container"></a>Postup úpravy kontejneru svazků

1. Přejděte do služby StorSimple Správce zařízení a přejděte do složky **správa > kontejnery svazků**.

2. V tabulkovém seznamu kontejnerů svazků vyberte kontejner svazků, který chcete upravit. Na stránce **zařízení** vyberte zařízení, dvakrát na něj klikněte a pak klikněte na kartu **kontejnery svazků** .

3. V tabulkovém výpisu kontejnerů svazků vyberte kontejner svazků, který chcete upravit. V okně, které se otevře, klikněte na panelu příkazů na **Upravit** .

    ![Upravit kontejner svazků](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. V okně **Upravit kontejner svazku** proveďte následující kroky:
   
   1. Po zadání názvu, šifrovacího klíče a účtu úložiště, který je přidružený k kontejneru svazků, se nedá změnit. Změňte nastavení přidružené šířky pásma.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Změnit nastavení šířky pásma](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Klikněte na **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Na další stránce dialogového okna **Upravit kontejner svazků** :<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. V rozevíracím seznamu vyberte existující šablonu šířky pásma.
   1. Zkontrolujte nastavení plánu pro zadanou šablonu šířky pásma.
   1. Klikněte na **Uložit** a potvrďte změny.
      
       ![Potvrdit změny](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      Okno **kontejnery svazků** se aktualizuje tak, aby odráželo změny.
