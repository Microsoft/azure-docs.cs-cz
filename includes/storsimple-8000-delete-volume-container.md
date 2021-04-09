---
title: zahrnout soubor
description: zahrnout soubor
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 386a39d76bf47da45f07054124886e174104849e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545294"
---
Pokud chcete odstranit kontejner svazků, musíte
 - Odstraňte svazky v kontejneru svazků. Pokud má kontejner svazků přidružené svazky, nejprve tyto svazky převeďte do režimu offline. Postupujte podle kroků v části [převedení svazku do režimu offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Až budou svazky v režimu offline, můžete je odstranit. 
 - Odstraňte přidružené zásady zálohování a cloudové snímky. Ověřte, jestli má kontejner svazků přidružené zásady zálohování a cloudové snímky. Pokud ano, [odstraňte zásady zálohování](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Tím se také odstraní snímky v cloudu. 
 
Pokud kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, můžete ho odstranit. Chcete-li odstranit kontejner svazků, proveďte následující postup.

#### <a name="to-delete-a-volume-container"></a>Odstranění kontejneru svazků

1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Vyberte zařízení a klikněte na něj a potom přejděte na **nastavení > spravovat > kontejnery svazků**.

    ![Okno kontejnery svazků](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. V tabulkovém seznamu kontejnerů svazků vyberte kontejner svazků, který chcete odstranit, klikněte pravým tlačítkem na **...** a pak vyberte **Odstranit**.

    ![Odstranit kontejner svazků](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. Pokud kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, můžete ho odstranit. Po zobrazení výzvy k potvrzení zkontrolujte a zaškrtněte políčko s informacemi o dopadu odstranění kontejneru svazků. Kliknutím na **Odstranit** odstraňte kontejner svazků.

    ![Potvrzení odstranění](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)

Seznam kontejnerů svazků se aktualizuje tak, aby odrážel odstraněný kontejner svazků.

![Snímek obrazovky se stránkou kontejneru svazků. Tabulkový seznam kontejnerů svazků už neobsahuje odstraněný kontejner.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
