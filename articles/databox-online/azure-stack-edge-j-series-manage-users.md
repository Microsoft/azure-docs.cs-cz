---
title: Správa uživatelů v grafickém procesoru Azure Stack Edge pro Microsoft Docs
description: Popisuje, jak používat Azure Portal ke správě uživatelů na procesorovém GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: e19c2b8a6785eb29426e150e7aa29097974765a1
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436511"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Použití Azure Portal ke správě uživatelů v Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak spravovat uživatele na Azure Stack Edge pro. Azure Stack Edge pro můžete spravovat přes Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní. Azure Portal můžete použít k přidání, úpravě nebo odstranění uživatelů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Přidání uživatele
> * Úprava uživatele
> * Odstranění uživatele

## <a name="about-users"></a>Informace o uživatelích

Uživatelé můžou být typu jen pro čtení, nebo s úplnými oprávněními. Uživatelé s oprávněním jen pro čtení mohou zobrazit pouze data o sdílení. Všichni uživatelé s oprávněními můžou číst sdílená data, zapisovat do těchto sdílených složek a upravovat nebo odstraňovat sdílená data.

 - **Uživatel s úplnými oprávněními** – místní uživatele s úplným přístupem.
 - **Uživatel jen pro čtení** –místní uživatel s přístupem jen pro čtení. Tito uživatelé jsou přidruženi ke sdíleným složkám, které umožňují operace jen pro čtení.

Oprávnění uživatele jsou definována při vytváření uživatele během vytváření sdílené složky. Můžete je upravit pomocí Průzkumníka souborů.


## <a name="add-a-user"></a>Přidání uživatele

Pokud chcete přidat uživatele, proveďte na webu Azure Portal následující kroky.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **Uživatelé**. Na panelu příkazů vyberte **+ Přidat uživatele** .

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


1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **Uživatelé**.

    ![Vyberte uživatele, kterého chcete odstranit.](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Vyberte uživatele ze seznamu uživatelů a pak vyberte **Odstranit**. Po zobrazení výzvy potvrďte odstranění.

    ![Vyberte uživatele, který chcete odstranit 2.](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

Seznam uživatelů se aktualizuje a odstraněný uživatel z něj zmizí.

![Aktualizovaný seznam uživatelů](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Další kroky

- Další informace o [správě šířky pásma](azure-stack-edge-j-series-manage-bandwidth-schedules.md).
