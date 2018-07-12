---
title: Spravovat záznamy řízení přístupu v StorSimple | Dokumentace Microsoftu
description: Popisuje způsob použití záznamy řízení přístupu (záznamů Acr) k určení, které hostitele může připojit ke svazku na zařízení StorSimple.
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
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597493"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Použití služby StorSimple Manager pro správu záznamy řízení přístupu

## <a name="overview"></a>Přehled
Záznamy řízení přístupu (záznamů Acr) umožňují určit, které hostitele může připojit k svazek v zařízení StorSimple. Záznamů Acr jsou nastaveny na určitý svazek a obsahovat kvalifikované názvy iSCSI (IQN) hostitele. Když hostitel se pokusí připojit ke svazku, zařízení zkontroluje ACR přidružené k tento svazek pro název IQN a pokud se zjistí shoda, bude připojení navázáno. Zaznamenává řízení přístupu **konfigurace** části okna služby Správce zařízení StorSimple zobrazit všechny záznamy řízení přístupu s odpovídající IQN hostitele.

Tento kurz vysvětluje následující běžné úkoly související s ACR:

* Přidat záznam řízení přístupu
* Upravit záznam řízení přístupu
* Odstranit záznam řízení přístupu

> [!IMPORTANT]
> * Při přiřazování ACR svazku, zajistíme, že svazek není přistupovat souběžně více než jeden neclusterovaného hostitele protože to může způsobit poškození svazku.
> * Při odstraňování ACR ze svazku, ujistěte se, že daný hostitel není přístup ke svazku protože odstranění by mohlo způsobit přerušení čtení i zápis.

## <a name="get-the-iqn"></a>Získání názvu IQN

Proveďte následující kroky k získání názvu IQN hostitele Windows, na kterém běží Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Přidat záznam řízení přístupu
Můžete použít **konfigurace** části v okně služby Správce zařízení StorSimple pro přidání záznamů Acr. Obvykle bude přidružíte jednu ACR jeden svazek.

Proveďte následující kroky pro přidání ACR.

#### <a name="to-add-an-acr"></a>Chcete-li přidat službu ACR

1. Přejděte do služby Správce zařízení StorSimple, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.
2. V **záznamy řízení přístupu** okna, klikněte na tlačítko **+ přidat ACR**.

    ![Klikněte na tlačítko Přidat ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. V **přidat ACR** okno, proveďte následující kroky:

    1. Zadejte název vaší služby ACR.
    
    2. Zadejte název IQN hostitele Windows serveru v části **iSCSI Initiator název (IQN)**.

    3. Klikněte na tlačítko **přidat** vytvořit ACR.

        ![Klikněte na tlačítko Přidat ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nově přidané ACR se zobrazí v tabulkovém výpisu záznamů Acr.

    ![Klikněte na tlačítko Přidat ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Upravit záznam řízení přístupu
Můžete použít **konfigurace** části v okně služby Správce zařízení StorSimple k úpravě záznamů Acr.

> [!NOTE]
> Doporučujeme proto upravovat pouze ty záznamů Acr, které nejsou aktuálně používá. Chcete-li upravit ACR přidružené k svazku, který se právě používá, je nutné nejprve provést svazku do režimu offline.

Proveďte následující kroky a upravovat ACR.

#### <a name="to-edit-an-access-control-record"></a>Chcete-li upravit záznam řízení přístupu
1.  Přejděte do služby Správce zařízení StorSimple, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém výpisu záznamy řízení přístupu, klikněte na tlačítko a vyberte ACR, kterou chcete upravit.

    ![Upravit záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. V **upravit záznam řízení přístupu** okno, zadejte jiný název IQN odpovídající na jiného hostitele.

    ![Upravit záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klikněte na **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Upravit záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Při aktualizaci ACR, se zobrazí oznámení. Tabulkovém výpisu také aktualizuje tak, aby odrážely změny.

   
## <a name="delete-an-access-control-record"></a>Odstranit záznam řízení přístupu
Můžete použít **konfigurace** části v okně služby Správce zařízení StorSimple k odstranění záznamů Acr.

> [!NOTE]
> Můžete odstranit pouze těchto záznamů Acr, které nejsou aktuálně používá. Pokud chcete odstranit službu ACR přidružené k svazku, který se právě používá, je nutné nejprve provést svazku do režimu offline.

Proveďte následující kroky, chcete-li odstranit záznam řízení přístupu.

#### <a name="to-delete-an-access-control-record"></a>Chcete-li odstranit záznam řízení přístupu
1.  Přejděte do služby Správce zařízení StorSimple, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém výpisu záznamy řízení přístupu, klikněte na tlačítko a vyberte ACR, kterou chcete odstranit.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Klepněte pravým tlačítkem myši vyvolejte místní nabídku a vyberte **odstranit**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po zobrazení výzvy k potvrzení, přečtěte si informace a pak klikněte na tlačítko **odstranit**.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Po dokončení odstranění, se zobrazí oznámení. Tabulkovém výpisu se aktualizuje tak, aby odrážely odstranění.

    ![Přejít na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Další postup
* Další informace o [Správa svazky zařízení StorSimple](storsimple-8000-manage-volumes-u2.md).
* Další informace o [ke správě zařízení StorSimple pomocí služby StorSimple Manager](storsimple-8000-manager-service-administration.md).

