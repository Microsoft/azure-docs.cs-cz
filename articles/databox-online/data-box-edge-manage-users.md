---
title: Azure Data Box Edge spravují uživatele | Dokumenty společnosti Microsoft
description: Popisuje, jak používat portál Azure ke správě uživatelů na Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946127"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Správa uživatelů na Azure Data Box Edge pomocí portálu Azure Portal

Tento článek popisuje, jak spravovat uživatele na Azure Data Box Edge. Azure Data Box Edge můžete spravovat prostřednictvím portálu Azure nebo prostřednictvím místního webového uživatelského prostředí. Azure Portal můžete použít k přidání, úpravě nebo odstranění uživatelů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání uživatele
> * Úprava uživatele
> * Odstranění uživatele

## <a name="about-users"></a>Informace o uživatelích

Uživatelé můžou být typu jen pro čtení, nebo s úplnými oprávněními. Jak názvy naznačují, uživatelé jen pro čtení můžou jenom zobrazit data ve sdílené složce. Uživatelé s úplnými oprávněními mohou číst data ve sdílené složce, zapisovat do těchto sdílených složek a upravovat nebo odstraňovat data ve sdílené složce.

 - **Uživatel s úplnými oprávněními** – místní uživatele s úplným přístupem.
 - **Uživatel jen pro čtení** –místní uživatel s přístupem jen pro čtení. Tito uživatelé jsou přidruženi ke sdíleným složkám, které umožňují operace jen pro čtení.

Oprávnění uživatele jsou definována při vytváření uživatele během vytváření sdílené složky. Úprava oprávnění na úrovni sdílené složky není aktuálně podporována.

## <a name="add-a-user"></a>Přidání uživatele

Pokud chcete přidat uživatele, proveďte na webu Azure Portal následující kroky.

1. Na webu Azure Portal přejděte na prostředek Data Box Edge a potom na **přehled > uživatelé**. Vyberte **+ Přidat uživatele** na panelu příkazů.

    ![Vybrat přidat uživatele](media/data-box-edge-manage-users/add-user-1.png)

2. Zadejte uživatelské jméno a heslo pro uživatele, kterého chcete přidat. Potvrďte heslo a vyberte **Přidat**.

    ![Zadání uživatelského jména a hesla](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Tito uživatelé jsou rezervováni systémem a není možné je používat: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Při zahájení a dokončení vytvoření uživatele se zobrazí oznámení. Po vytvoření uživatele zvolte z panelu příkazů možnost **Aktualizovat** a zobrazte aktualizovaný seznam uživatelů.


## <a name="modify-user"></a>Úprava uživatele

Po vytvoření uživatele můžete změnit heslo přidružené k uživateli. Vyberte ze seznamu uživatelů. Zadejte a potvrďte nové heslo. Uložte změny.
 
![Úprava uživatele](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Odstranění uživatele

Pokud chcete uživatele odstranit, proveďte na webu Azure Portal následující kroky.


1. Na webu Azure Portal přejděte na prostředek Data Box Edge a potom na **přehled > uživatelé**.

    ![Vyberte uživatele, který má být odstraněn.](media/data-box-edge-manage-users/delete-user-1.png)

2. Vyberte uživatele ze seznamu uživatelů a pak vyberte **Odstranit**.  

   ![Vybrat Odstranit](media/data-box-edge-manage-users/delete-user-2.png)

3. Po zobrazení výzvy potvrďte odstranění. 

   ![Potvrzení odstranění](media/data-box-edge-manage-users/delete-user-3.png)

Seznam uživatelů se aktualizuje a odstraněný uživatel z něj zmizí.

![Aktualizovaný seznam uživatelů](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Další kroky

- Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).
