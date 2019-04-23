---
title: Správa uživatelů Azure Data Box Gateway | Microsoftu Docs
description: Popisuje používání webu Azure Portal ke správě uživatelů ve službě Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 9ee5eec5a5c17e443fe71f0093f09c96cf81ece0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998630"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Použití webu Azure Portal ke správě uživatelů ve službě Azure Data Box Gateway 

Tento článek popisuje postup správy uživatelů ve službě Azure Data Box Gateway. Azure Data Box Gateway můžete spravovat přes web Azure Portal nebo v místním webovém uživatelském rozhraní. Azure Portal můžete použít k přidání, úpravě nebo odstranění uživatelů.

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

1. Na webu Azure Portal přejděte k prostředku Data Box Gateway a pak přejděte na **Přehled**. Klikněte na tlačítko **+ Přidat uživatele** na panelu příkazů.

    ![Kliknutí na Přidat uživatele](media/data-box-gateway-manage-users/add-user-1.png)

2. Zadejte uživatelské jméno a heslo pro uživatele, kterého chcete přidat. Potvrďte heslo a klikněte na **Přidat**.

    ![Klikněte na Přidat uživatele](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Tito uživatelé jsou rezervované systémem a neměl by se používat: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Budete informováni o zahájení vytváření uživatele a jeho dokončení. Po vytvoření uživatele klikněte na panelu příkazů na **Aktualizovat**, abyste zobrazili aktualizovaný seznam uživatelů.


## <a name="modify-user"></a>Úprava uživatele

Po vytvoření uživatele můžete změnit heslo přidružené k uživateli. Vyberte a klikněte na uživatele v seznamu. Zadejte a potvrďte nové heslo. Uložte změny.
 
![Úprava uživatele](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Odstranění uživatele

Pokud chcete uživatele odstranit, proveďte na webu Azure Portal následující kroky.

1. Vyberte a klikněte na uživatele v seznamu uživatelů a potom klikněte na **Odstranit**.  

   ![Odstranění uživatele](media/data-box-gateway-manage-users/delete-user-1.png)

2. Po zobrazení výzvy potvrďte odstranění. 

   ![Odstranění uživatele](media/data-box-gateway-manage-users/delete-user-2.png)

Seznam uživatelů se aktualizuje a odstraněný uživatel z něj zmizí.

![Odstranění uživatele](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Další postup

- Další informace o [správě šířky pásma](data-box-gateway-manage-bandwidth-schedules.md).
