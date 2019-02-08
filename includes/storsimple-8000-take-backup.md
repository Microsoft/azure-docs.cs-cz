---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888465"
---
### <a name="to-take-a-backup"></a>Provedení zálohy

1. Přejděte do služby Správce zařízení StorSimple. V tabulkovém výpisu zařízení vyberte a klikněte na vaše zařízení a pak na **Všechna nastavení**. V okně **Nastavení** přejděte do **Nastavení > Správa > Zásady zálohování**.

    ![Přidání zásady zálohování](./media/storsimple-8000-take-backup/step8takebu1.png)

2. V okně **Zásady zálohování** klikněte na **Přidat zásadu**.

    ![Přidání zásady zálohování](./media/storsimple-8000-take-backup/step8takebu2.png)

3. V okně **Vytvořit zásadu zálohování** zadejte název zásady zálohování, který obsahuje 3 až 150 znaků.

4. Vyberte svazky, které chcete zálohovat. Pokud vyberete víc než jeden svazek, tyto svazky se seskupí, aby byla vytvořená záloha konzistentní v případě selhání.

    ![Přidání zásady zálohování](./media/storsimple-8000-take-backup/step8takebu4.png)

5. V okně **Přidat první pravidlo**:

    1. Vyberte typ zálohování. Pokud chcete rychlejší zálohování, vyberte **Místní** snímek. Pokud vám záleží na odolnosti dat, vyberte **Cloudový** snímek.
    2. Zadejte četnost zálohování v minutách, hodinách, dnech nebo týdnech.
    3. Vyberte dobu uchování. Volby uchování závisí na požadované četnosti zálohování. Třeba v případě denní zásady se dá doba uchování zadat v týdnech, kdežto v případě měsíční zásady v měsících.
    4. Vyberte počáteční čas a datum zásady zálohování.
    5. Kliknutím na **OK** vytvořte zásadu zálohování.

        ![Přidání zásady zálohování](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Kliknutím na **Vytvořit** spusťte vytváření zásady zálohování. Po úspěšném vytvoření zásady zálohování se zobrazí oznámení. Zároveň se aktualizuje seznam zásad zálohování.
      
      ![Přidání zásady zálohování](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Teď máte zásadu zálohování, která bude podle plánu vytvářet zálohy dat svazku.




