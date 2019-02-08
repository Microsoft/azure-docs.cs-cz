---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889575"
---
#### <a name="to-create-a-manual-backup"></a>Ruční vytvoření zálohy

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. V tabulkovém výpisu zařízení vyberte své zařízení. Přejděte do **Nastavení > Správa > Zásady zálohování**.

2. Okno **Zásady zálohování** obsahuje výpis všech zásad zálohování v tabulkovém formátu, včetně zásad svazku, který chcete zálohovat. Vyberte zásadu přidruženou ke svazku, který chcete zálohovat, a kliknutím pravým tlačítkem myši vyvolejte místní nabídku. Z rozevíracího seznamu vyberte **Zálohovat nyní**.

    ![Ruční vytvoření zálohy](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. V okně **Zálohovat nyní** proveďte následující kroky:

    1. Zvolte odpovídající **typ snímku** z rozevíracího seznamu: **Místní** snímku nebo **cloudu** snímku. Pokud chcete rychlejší zálohování a obnovení, vyberte místní snímek; cloudový snímek vyberte, pokud chcete větší odolnost dat.

        ![Ruční vytvoření zálohy](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Kliknutím na **OK** spusťte úlohu pro vytvoření snímku. Po úspěšném vytvoření úlohy se v horní části stránky zobrazí oznámení.

        ![Ruční vytvoření zálohy](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Pokud chcete úlohu monitorovat, klikněte na oznámení. Tím přejdete do okna **Úlohy**, kde můžete zobrazit průběh úlohy.


5. Po dokončení úlohy zálohování přejděte na kartu **Backup catalog** (Katalog zálohování).

6. Nastavte filtry na příslušné zařízení, zásady zálohování a časové rozmezí. Záloha by se měla zobrazit v seznamu sad záloh uvedených v katalogu.

