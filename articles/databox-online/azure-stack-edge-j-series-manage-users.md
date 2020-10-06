---
title: Správa uživatelů v grafickém procesoru Azure Stack Edge pro Microsoft Docs
description: Popisuje, jak používat Azure Portal ke správě uživatelů na procesorovém GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 66967c1fa8f6ca42459559caf46bffb0e5afe925
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743586"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Použití Azure Portal ke správě uživatelů v Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak spravovat uživatele na Azure Stack Edge pro. Azure Stack Edge pro můžete spravovat přes Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní. Azure Portal můžete použít k přidání, úpravě nebo odstranění uživatelů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání uživatele
> * Úprava uživatele
> * Odstranění uživatele

## <a name="about-users"></a>Informace o uživatelích

Uživatelé můžou být typu jen pro čtení, nebo s úplnými oprávněními. Jak názvy naznačují, uživatelé jen pro čtení můžou jenom zobrazit data ve sdílené složce. Uživatelé s úplnými oprávněními mohou číst data ve sdílené složce, zapisovat do těchto sdílených složek a upravovat nebo odstraňovat data ve sdílené složce.

 - **Uživatel s úplnými oprávněními** – místní uživatele s úplným přístupem.
 - **Uživatel jen pro čtení** –místní uživatel s přístupem jen pro čtení. Tito uživatelé jsou přidruženi ke sdíleným složkám, které umožňují operace jen pro čtení.

Oprávnění uživatele jsou definována při vytváření uživatele během vytváření sdílené složky. Po definování oprávnění spojených s uživatelem je možné později tato oprávnění upravit pomocí Průzkumníka souborů.


## <a name="add-a-user"></a>Přidání uživatele

Pokud chcete přidat uživatele, proveďte na webu Azure Portal následující kroky.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **přehled > uživatelé**. Na panelu příkazů vyberte **+ Přidat uživatele** .

    ![Vyberte Přidat uživatele.](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. Zadejte uživatelské jméno a heslo pro uživatele, kterého chcete přidat. Potvrďte heslo a vyberte **Přidat**.

    ![Zadat uživatelské jméno a heslo](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Tito uživatelé jsou rezervováni systémem a není možné je používat: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Po zahájení a dokončení vytvoření uživatele se zobrazí oznámení. Po vytvoření uživatele klikněte na panelu příkazů na **aktualizovat** , aby se zobrazil aktualizovaný seznam uživatelů.


## <a name="modify-user"></a>Úprava uživatele

Po vytvoření uživatele můžete změnit heslo přidružené k uživateli. Vyberte ze seznamu uživatelů. Zadejte a potvrďte nové heslo. Uložte změny.
 
![Úprava uživatele](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Odstranění uživatele

Pokud chcete uživatele odstranit, proveďte na webu Azure Portal následující kroky.


1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **přehled > uživatelé**.

    ![Vyberte uživatele, kterého chcete odstranit.](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Vyberte uživatele ze seznamu uživatelů a pak vyberte **Odstranit**. Po zobrazení výzvy potvrďte odstranění.

    ![Vyberte uživatele, který chcete odstranit 2.](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

Seznam uživatelů se aktualizuje a odstraněný uživatel z něj zmizí.

![Aktualizovaný seznam uživatelů](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [správě šířky pásma](azure-stack-edge-j-series-manage-bandwidth-schedules.md).
