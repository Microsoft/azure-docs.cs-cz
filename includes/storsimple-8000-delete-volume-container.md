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
ms.openlocfilehash: e7f3f80c886f90a8bc3ae8c38e7d101c506439a6
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35250218"
---
Pokud chcete odstranit kontejner svazků, je potřeba
 - Odstraňte svazky v kontejneru svazků. Pokud kontejner svazků má přidružené svazky, offline režimu tyto svazky nejdřív. Postupujte podle kroků v [do offline režimu svazku](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Jakmile jsou tyto svazky v režimu offline, můžete je odstranit. 
 - Odstraňte přidružené zásady zálohování a cloudových snímků. Zkontrolujte, pokud kontejner svazků má přidružené zásady zálohování a cloudových snímků. Pokud ano, pak [odstranit zásady zálohování](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Tato akce také odstraní cloudových snímků. 
 
Po kontejneru svazků nemá žádné přidružené svazky, zásady zálohování a cloudových snímků, můžete ji odstranit. Proveďte následující postup se odstranit kontejner svazků.

#### <a name="to-delete-a-volume-container"></a>Chcete-li odstranit kontejner svazků
1. Přejděte do služby Správce zařízení StorSimple a klikněte na **Zařízení**. Vyberte a klikněte na zařízení a potom přejděte na **Nastavení > Správa > kontejnery svazků**.

    ![Okno kontejnery svazku](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Ze seznamu tabulkové kontejnery svazků vyberte kontejner svazků, které chcete odstranit, klikněte pravým tlačítkem na **...**  a pak vyberte **odstranit**.

    ![Odstranit kontejneru svazků](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Pokud kontejner svazků nemá žádné přidružené svazky, zásady zálohování a cloudových snímků, pak jej lze odstranit. Po zobrazení výzvy k potvrzení, zkontrolujte a zaškrtněte políčko s informacemi o tom důsledcích odstranění kontejneru svazků. Klikněte na tlačítko **odstranit** poté odstranit kontejner svazků.

    ![Potvrzení odstranění](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Seznam kontejnery svazků je aktualizovat tak, aby odrážela kontejneru odstraněné svazku.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


