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
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175054"
---
Chcete-li odstranit kontejner svazku, musíte
 - odstraňte svazky v kontejneru svazků. Pokud kontejner svazků obsahuje přidružené svazky, přenesete tyto svazky nejprve do úpájení. Postupujte podle pokynů v [části Převedení svazku do funkce offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Po offline svazky, můžete je odstranit. 
 - odstraňte přidružené zásady zálohování a snímky cloudu. Zkontrolujte, jestli kontejner svazku má přidružené zásady zálohování a cloudové snímky. Pokud ano, [odstraňte zásady zálohování](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Tím se také odstraní snímky cloudu. 
 
Pokud kontejner svazku nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, můžete ho odstranit. Chcete-li odstranit kontejner svazku, proveďte následující postup.

#### <a name="to-delete-a-volume-container"></a>Odstranění kontejneru svazku
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Vyberte zařízení a klepněte na něj a přejděte do **části Nastavení > Správa kontejnerů > svazků**.

    ![Objemkontejnery čepel](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. V tabulkovém seznamu kontejnerů svazků vyberte kontejner svazku, který chcete odstranit, klikněte pravým tlačítkem myši **...** a pak vyberte **Odstranit**.

    ![Odstranit kontejner svazku](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Pokud kontejner svazku nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, pak jej lze odstranit. Po zobrazení výzvy k potvrzení zkontrolujte a zaškrtněte políčko s uvedením dopadu odstranění kontejneru svazku. Chcete-li odstranit kontejner svazku, klepněte na **tlačítko Odstranit.**

    ![Potvrzení odstranění](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Seznam kontejnerů svazků je aktualizován tak, aby odrážel odstraněný kontejner svazku.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


