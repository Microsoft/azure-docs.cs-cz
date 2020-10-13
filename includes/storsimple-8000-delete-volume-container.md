---
title: zahrnout soubor
description: zahrnout soubor
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 30bbd06e36ed1e03caa391165a8abc275f1899a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102550"
---
Pokud chcete odstranit kontejner svazků, musíte
 - Odstraňte svazky v kontejneru svazků. Pokud má kontejner svazků přidružené svazky, nejprve tyto svazky převeďte do režimu offline. Postupujte podle kroků v části [převedení svazku do režimu offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Až budou svazky v režimu offline, můžete je odstranit. 
 - Odstraňte přidružené zásady zálohování a cloudové snímky. Ověřte, jestli má kontejner svazků přidružené zásady zálohování a cloudové snímky. Pokud ano, [odstraňte zásady zálohování](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Tím se také odstraní snímky v cloudu. 
 
Pokud kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, můžete ho odstranit. Chcete-li odstranit kontejner svazků, proveďte následující postup.

#### <a name="to-delete-a-volume-container"></a>Odstranění kontejneru svazků
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Vyberte zařízení a klikněte na něj a potom přejděte na **nastavení > spravovat > kontejnery svazků**.

    ![Okno kontejnery svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. V tabulkovém seznamu kontejnerů svazků vyberte kontejner svazků, který chcete odstranit, klikněte pravým tlačítkem na **...** a pak vyberte **Odstranit**.

    ![Odstranit kontejner svazků](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Pokud kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, můžete ho odstranit. Po zobrazení výzvy k potvrzení zkontrolujte a zaškrtněte políčko s informacemi o dopadu odstranění kontejneru svazků. Kliknutím na **Odstranit** odstraňte kontejner svazků.

    ![Potvrzení odstranění](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Seznam kontejnerů svazků se aktualizuje tak, aby odrážel odstraněný kontejner svazků.

![Snímek obrazovky se stránkou kontejneru svazků. Tabulkový seznam kontejnerů svazků už neobsahuje odstraněný kontejner.](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


