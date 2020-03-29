---
title: Správa záznamů řízení přístupu v aplikaci StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí záznamů řízení přístupu (AVR) určit, kteří hostitelé se mohou připojit ke svazku na zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64693239"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Správa záznamů řízení přístupu pomocí služby StorSimple Manager

## <a name="overview"></a>Přehled
Záznamy řízení přístupu (ARS) umožňují určit, kteří hostitelé se mohou připojit ke svazku na zařízení StorSimple. ACC jsou nastaveny na určitý svazek a obsahují kvalifikované názvy iSCSI (IQN) hostitelů. Když se hostitel pokusí připojit ke svazku, zařízení zkontroluje ACR přidružené k tomuto svazku pro název IQN a pokud je shoda, pak je připojení navázáno. Záznamy řízení přístupu v části **Konfigurace** okna služby Správce zařízení StorSimple zobrazí všechny záznamy řízení přístupu s odpovídajícími sítěmi IQN hostitelů.

Tento kurz vysvětluje následující běžné úkoly související s ACR:

* Přidání záznamu řízení přístupu
* Úprava záznamu řízení přístupu
* Odstranění záznamu řízení přístupu

> [!IMPORTANT]
> * Při přiřazování ACR ke svazku dbát na to, aby ke svazku současně nepřistupovalo více než jeden neclusterovaný hostitel, protože by to mohlo svazek poškodit.
> * Při odstraňování ACR ze svazku se ujistěte, že odpovídající hostitel nemá přístup ke svazku, protože odstranění může vést k přerušení čtení a zápisu.

## <a name="get-the-iqn"></a>Získejte IQN

Chcete-li získat iqn hostitele systému Windows se systémem Windows Server 2012, postupujte podle následujících kroků.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Přidání záznamu řízení přístupu
Část **Konfigurace** v okně služby Správce zařízení StorSimple slouží k přidání acrů. Obvykle přidružíte jeden ACR k jednomu svazku.

Chcete-li přidat acr, proveďte následující kroky.

#### <a name="to-add-an-acr"></a>Přidání ACR

1. Přejděte na službu Správce zařízení StorSimple, poklepejte na název služby a v části **Konfigurace** klepněte na **položku Záznamy řízení přístupu**.
2. V okně **Záznamy řízení přístupu** klepněte na tlačítko **+ Přidat acr**.

    ![Klikněte na Přidat ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. V okně **Přidat acr** proveďte následující kroky:

    1. Zadejte název acr.
    
    2. Pod **názvem iniciátoru iSCSI (IQN)** zadejte název IQN.

    3. Chcete-li vytvořit acr, klepněte na **tlačítko Přidat.**

        ![Klikněte na Přidat ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nově přidaný ACR se zobrazí v tabulkovém výpisu ACR.

    ![Klikněte na Přidat ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Úprava záznamu řízení přístupu
Část **Konfigurace** v okně služby Správce zařízení StorSimple slouží k úpravám akrony.

> [!NOTE]
> Doporučujeme upravit pouze ty akrové, které se aktuálně nepoužívají. Chcete-li upravit acr přidružený ke svazku, který je právě používán, je nutné jej nejprve převést do funkce offline.

Chcete-li upravit acr, proveďte následující kroky.

#### <a name="to-edit-an-access-control-record"></a>Úprava záznamu řízení přístupu
1.  Přejděte na službu Správce zařízení StorSimple, poklepejte na název služby a v části **Konfigurace** klepněte na **položku Záznamy řízení přístupu**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém výpisu záznamů řízení přístupu klikněte a vyberte aCR, který chcete upravit.

    ![Úprava záznamů řízení přístupu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. V okně **Upravit záznam řízení přístupu** zadejte jiný IQN odpovídající jinému hostiteli.

    ![Úprava záznamů řízení přístupu](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klikněte na **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Úprava záznamů řízení přístupu](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Budete upozorněni při aktualizaci ACR. Tabulkový výpis se také aktualizuje tak, aby odrážel změnu.

   
## <a name="delete-an-access-control-record"></a>Odstranění záznamu řízení přístupu
Část **Konfigurace** v okně služby Správce zařízení StorSimple slouží k odstranění akrony.

> [!NOTE]
> Můžete odstranit pouze ty akrové, které nejsou aktuálně používány. Chcete-li odstranit acr přidružený ke svazku, který je právě používán, je nutné nejprve převést svazek do offline.

Chcete-li odstranit záznam řízení přístupu, proveďte následující kroky.

#### <a name="to-delete-an-access-control-record"></a>Odstranění záznamu řízení přístupu
1.  Přejděte na službu Správce zařízení StorSimple, poklepejte na název služby a v části **Konfigurace** klepněte na **položku Záznamy řízení přístupu**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém výpisu záznamů řízení přístupu klikněte a vyberte aCR, který chcete odstranit.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Klepnutím pravým tlačítkem myši vyvoláte místní nabídku a vyberte **příkaz Odstranit**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po zobrazení výzvy k potvrzení zkontrolujte informace a klepněte na tlačítko **Odstranit**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Po dokončení odstranění budete upozorněni. Tabulkový výpis je aktualizován tak, aby odrážel odstranění.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Další kroky
* Další informace o [správě svazků StorSimple](storsimple-8000-manage-volumes-u2.md).
* Další informace o [použití služby StorSimple Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

