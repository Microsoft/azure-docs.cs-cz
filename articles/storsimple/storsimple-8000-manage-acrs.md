---
title: Spravovat záznamy řízení přístupu v zařízení StorSimple | Microsoft Docs
description: Popisuje, jak používat záznamy řízení přístupu (ACRs) k určení, které hostitele může připojit k svazek v zařízení StorSimple.
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874903"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Použít službu StorSimple Manager ke správě záznamy řízení přístupu

## <a name="overview"></a>Přehled
Záznamy řízení přístupu (ACRs) umožňují určit, které hostitele může připojit k svazek v zařízení StorSimple. ACRs jsou nastaveny na konkrétním svazku a obsahovat kvalifikované názvy iSCSI (IQN) hostitele. Když hostitel pokusí o připojení ke svazku, zařízení se kontroluje ACR přidružený tento svazek pro název IQN a pokud je nalezena shoda, pak připojení. Zaznamenává řízení přístupu **konfigurace** části okně vaší služby StorSimple Manager zařízení zobrazí všechny záznamy řízení přístupu s odpovídající IQN hostitelů.

Tento kurz vysvětluje následující běžné úlohy související s ACR:

* Přidání záznamu o řízení přístupu
* Upravit záznam řízení přístupu
* Odstranit záznam řízení přístupu

> [!IMPORTANT]
> * Při přiřazování ACR svazku, vezměte v potaz, že svazek není přístup souběžně více než jednomu hostiteli neclusterované protože to může způsobit poškození svazku.
> * Při odstraňování ACR ze svazku, ujistěte se, že odpovídající hostitele není přístup k svazku vzhledem k tomu, že odstranění může mít za následek přerušení pro čtení a zápis.

## <a name="get-the-iqn"></a>Získání názvu IQN

Proveďte následující kroky k získání názvu IQN hostitele Windows se systémem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Přidání záznamu o řízení přístupu
Můžete použít **konfigurace** část v okně service Manager zařízení StorSimple přidat ACRs. Obvykle přidružíte jednu ACR jeden svazek.

Proveďte následující postup pro přidání ACR.

#### <a name="to-add-an-acr"></a>Chcete-li přidat ACR

1. Přejděte do služby StorSimple Manager zařízení, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.
2. V **záznamy řízení přístupu** okně klikněte na tlačítko **+ přidat ACR**.

    ![Klikněte na tlačítko Přidat ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. V **přidat ACR** okno, proveďte následující kroky:

    1. Zadáte název acr.
    
    2. Zadejte název IQN hostitele vašeho systému Windows Server v části **iSCSI Initiator název IQN ()**.

    3. Klikněte na tlačítko **přidat** vytvořit ACR.

        ![Klikněte na tlačítko Přidat ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Nově přidaný ACR se zobrazí v tabulkovém seznam ACRs.

    ![Klikněte na tlačítko Přidat ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Upravit záznam řízení přístupu
Můžete použít **konfigurace** část v okně service Manager zařízení StorSimple upravit ACRs.

> [!NOTE]
> Doporučujeme proto upravovat pouze ACRs, které nejsou aktuálně používá. Chcete-li upravit ACR přidružené k svazku, který je aktuálně používán, musíte nejdřív udělat svazek offline.

Proveďte následující kroky, chcete-li upravit ACR.

#### <a name="to-edit-an-access-control-record"></a>Chcete-li upravit záznam řízení přístupu
1.  Přejděte do služby StorSimple Manager zařízení, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.

    ![Přejděte na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém seznam záznamy řízení přístupu, klikněte na tlačítko a vyberte ACR, který chcete upravit.

    ![Úpravy záznamů o řízení přístupu](./media/storsimple-8000-manage-acrs/editacr1.png)

3. V **záznam řízení přístupu upravit** okno, zadejte jiný IQN odpovídající do jiného hostitele.

    ![Úpravy záznamů o řízení přístupu](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klikněte na **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**. 

    ![Úpravy záznamů o řízení přístupu](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Budete upozorněni, když se aktualizuje ACR. Tabulkové se také seznam aktualizuje, aby odrážely změny.

   
## <a name="delete-an-access-control-record"></a>Odstranit záznam řízení přístupu
Můžete použít **konfigurace** část v okně služby StorSimple Manager zařízení odstranit ACRs.

> [!NOTE]
> Lze odstranit pouze ACRs, které nejsou aktuálně používá. Pokud chcete odstranit ACR přidružené k svazku, který je aktuálně používán, musíte nejdřív udělat svazek offline.

Proveďte následující kroky a odstraňte záznam řízení přístupu.

#### <a name="to-delete-an-access-control-record"></a>Odstranit záznam řízení přístupu
1.  Přejděte do služby StorSimple Manager zařízení, klikněte dvakrát na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.

    ![Přejděte na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/createacr1.png)

2. V tabulkovém seznam záznamy řízení přístupu, klikněte na tlačítko a vyberte ACR, který chcete odstranit.

    ![Přejděte na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Klikněte pravým tlačítkem a vyvolání v místní nabídce vyberte **odstranit**.

    ![Přejděte na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Po zobrazení výzvy k potvrzení, zkontrolujte informace a pak klikněte na tlačítko **odstranit**.

    ![Přejděte na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Upozornění se zobrazí po dokončení odstranění. Tabulkový výčet je aktualizována tak, aby odrážela odstranění.

    ![Přejděte na záznamy řízení přístupu](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Další kroky
* Další informace o [správu svazků zařízení StorSimple](storsimple-8000-manage-volumes-u2.md).
* Další informace o [pomocí služby StorSimple Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

