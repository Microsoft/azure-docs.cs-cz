---
title: Spravovat záznamy řízení přístupu pro pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje, jak spravovat záznamy řízení přístupu (ACRs) k určení, které hostitele může připojit k svazek v poli virtuální zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875939"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple Spravovat záznamy řízení přístupu pro pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Záznamy řízení přístupu (ACRs) umožňují určit, které hostitele může připojit k svazek v poli virtuální zařízení StorSimple (také označované jako místní virtuální zařízení StorSimple). ACRs jsou nastaveny na konkrétním svazku a obsahovat kvalifikované názvy iSCSI (IQN) hostitele. Když hostitel pokusí o připojení ke svazku, zařízení ověří ACR přidružený tento svazek pro název IQN, a pokud je nalezena shoda, pak připojení. **Záznamy řízení přístupu** okno v rámci **konfigurace** části služby Správce zařízení zobrazí všechny záznamy řízení přístupu s odpovídající IQN hostitelů.

![Spravovat přístup k záznamům v ovládací prvek](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Tento kurz vysvětluje následující běžné úlohy související s ACR:

* Získání názvu IQN
* Přidání záznamu o řízení přístupu
* Upravit záznam řízení přístupu
* Odstranit záznam řízení přístupu

> [!IMPORTANT]
> 
> * Při přiřazování ACR svazku, vezměte v potaz, že svazek není přístup souběžně více než jednomu hostiteli neclusterované protože to může způsobit poškození svazku.
> * Při odstraňování ACR ze svazku, ujistěte se, že odpovídající hostitele není přístup k svazku vzhledem k tomu, že odstranění může mít za následek přerušení pro čtení a zápis.


## <a name="get-the-iqn"></a>Získání názvu IQN

Proveďte následující kroky k získání názvu IQN hostitele Windows se systémem Windows Server 2012.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Přidat ACR

Používáte **záznamy řízení přístupu** okno v rámci **konfigurace** části služby StorSimple Manager zařízení přidat ACRs. Obvykle přidružíte jednu ACR jeden svazek.

Další informace o přiřazení ACR svazku [přidat svazek](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Při přiřazování ACR svazku, vezměte v potaz, že svazek není přístup souběžně více než jednomu hostiteli neclusterované protože to může způsobit poškození svazku.


Proveďte následující postup pro přidání ACR.

#### <a name="to-add-an-acr"></a>Chcete-li přidat ACR

1. Na stránce cílové služby, vyberte svoji službu, dvakrát klikněte na název služby a pak v rámci **konfigurace** klikněte na tlačítko **záznamy řízení přístupu**.
2. V **záznamy řízení přístupu** okně klikněte na tlačítko **přidat**.
3. V **přidat ACR** okno, postupujte takto:
   
    1. Zadejte **Název** záznamu ACR.
    
    2. V části **název iniciátoru iSCSI**, zadejte název IQN hostitele s Windows. Získání názvu IQN hostitele vašeho systému Windows Server, postupujte takto:
   
    3. Na hostiteli s Windows spusťte iniciátor iSCSI od Microsoftu. V okně Vlastnosti iniciátoru iSCSI na **konfigurace** , vyberte a zkopírujte řetězec z **název iniciátoru** pole.
    Vložte tento řetězec v **IQN** pole **přidat ACR** okno.
   
    6. Klikněte na tlačítko **přidat** přidat ACR.  
   
        ![Přidat záznamy řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Tabulkový výčet je aktualizována tak, aby odrážela přidání.

## <a name="edit-an-acr"></a>Upravit ACR

Můžete použít **záznamy řízení přístupu** okno v rámci **konfigurace** části služby Správce zařízení na portálu Azure, chcete-li upravit ACRs.

> [!NOTE]
> ACR, který je aktuálně používán, byste neměli upravovat. Upravit ACR přidružené k svazku, který je aktuálně používán, byste měli nejdřív vzít svazek offline.


Proveďte následující kroky, chcete-li upravit ACR.

#### <a name="to-edit-an-acr"></a>Chcete-li upravit ACR

1. Na stránce cílové služby, vyberte svoji službu, dvakrát klikněte na název služby a pak v rámci **konfigurace** části **záznamy řízení přístupu**.
2. V **záznamy řízení přístupu** okno z tabulkové seznam záznamů, řízení přístupu, klikněte dvakrát na ACR, který chcete upravit.
3. V **upravit záznamy řízení přístupu** okno, postupujte takto:
   
    1. Zadáním názvu IQN pro ACR.
   
    2. Klikněte na tlačítko **Uložit** v horní části okna uložit upravené ACR. Zobrazí následující potvrzující zpráva:
   
        ![Úpravy záznamů o řízení přístupu](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Odstranit záznam řízení přístupu

Můžete použít **konfigurace** na portálu Azure odstranit ACRs.

> [!NOTE]
> 
> * Nedoporučuje se mazat ACR, který je aktuálně používán. Pokud chcete odstranit ACR přidružené k svazku, který je aktuálně používán, byste měli nejdřív vzít svazek offline.
> * Při odstraňování ACR ze svazku, ujistěte se, že odpovídající hostitele není přístup k svazku vzhledem k tomu, že odstranění může mít za následek přerušení pro čtení a zápis.


Proveďte následující kroky a odstraňte záznam řízení přístupu.

#### <a name="to-delete-an-access-control-record"></a>Odstranit záznam řízení přístupu

1. Na stránce cílové služby, vyberte svoji službu, dvakrát klikněte na název služby a pak v rámci **konfigurace** části **záznamy řízení přístupu**.

2. V **záznamy řízení přístupu** okno z tabulkové seznam záznamů, řízení přístupu, klikněte dvakrát na ACR, který chcete odstranit.

3. V okně Upravit přístup řízení záznamy klikněte na tlačítko **odstranit**.
   
    ![Odstranit ACRS](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Po zobrazení výzvy k potvrzení, klikněte na tlačítko **odstranit** Chcete-li pokračovat v odstraňování. Tabulkový výčet je aktualizována tak, aby odrážela odstranění.
   
   ![Zpráva upozornění](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [přidání svazků a konfigurace ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

