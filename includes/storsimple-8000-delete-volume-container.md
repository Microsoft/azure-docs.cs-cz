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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156084"
---
Pokud chcete odstranit kontejner svazků, je potřeba
 - Odstraňte svazky v kontejneru svazků. Pokud kontejner svazků obsahuje přidružené svazky, přepněte tyto svazky do režimu offline nejprve. Postupujte podle kroků v [svazek převést do režimu offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Jakmile svazků jsou offline, můžete je odstranit. 
 - Odstraňte přidružené zásady zálohování a cloudových snímků. Zaškrtněte, pokud kontejner svazků má přidružené zásady zálohování a cloudové snímky. Pokud ano, pak [odstranit zásady zálohování](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Tato akce také odstraní cloudové snímky. 
 
Když kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, můžete je odstranit. Proveďte následující postup se odstranit kontejner svazků.

#### <a name="to-delete-a-volume-container"></a>Chcete-li odstranit kontejner svazků
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Vyberte a klikněte na zařízení a potom přejděte k **Nastavení > Správa > kontejnery svazků**.

    ![Okno kontejnery svazků](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Tabulkový seznam kontejnerů svazků, vyberte kontejner svazků, které chcete odstranit, klikněte pravým tlačítkem na **...**  a pak vyberte **odstranit**.

    ![Odstraňování kontejneru svazků](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Pokud kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudové snímky, pak je možné odstranit. Po zobrazení výzvy k potvrzení, přečtěte si a zaškrtněte políčko s informacemi o tom následky odstranění kontejneru svazků. Klikněte na tlačítko **odstranit** potom odstranit kontejner svazků.

    ![Potvrzení odstranění](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Seznam kontejnerů svazků se aktualizuje tak, aby odrážely kontejneru odstraněných svazků.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


