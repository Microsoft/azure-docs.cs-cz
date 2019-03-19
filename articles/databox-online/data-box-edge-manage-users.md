---
title: Správa Azure Data Box Edge uživatelů | Dokumentace Microsoftu
description: Popisuje, jak pomocí webu Azure portal ke správě uživatelů na hranici vaší Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 5642f0c1bcb7d7d8dcb17885aa6d3e9197889685
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57887656"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Pomocí webu Azure portal ke správě uživatelů na hranici vaší Azure Data Box

Tento článek popisuje, jak spravovat uživatele na hranici vaší Azure Data Box. Můžete spravovat Azure Data Box Edge prostřednictvím webu Azure portal nebo přes místní webové uživatelské rozhraní. Azure Portal můžete použít k přidání, úpravě nebo odstranění uživatelů.

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **přehled > Uživatelé**. Vyberte **+ přidat uživatele** na panelu příkazů.

    ![Výběr možnosti Přidat uživatele](media/data-box-edge-manage-users/add-user-1.png)

2. Zadejte uživatelské jméno a heslo pro uživatele, kterého chcete přidat. Potvrďte heslo a vyberte **přidat**.

    ![Zadejte uživatelské jméno a heslo](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Tito uživatelé jsou rezervované systémem a neměl by se používat: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Oznámení se zobrazí při vytvoření uživatele spustí a je dokončena. Po vytvoření uživatele, na panelu příkazů vyberte **aktualizovat** chcete zobrazit aktualizovaný seznam uživatelů.


## <a name="modify-user"></a>Úprava uživatele

Po vytvoření uživatele můžete změnit heslo přidružené k uživateli. Vyberte ze seznamu uživatelů. Zadejte a potvrďte nové heslo. Uložte změny.
 
![Úprava uživatele](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Odstranění uživatele

Pokud chcete uživatele odstranit, proveďte na webu Azure Portal následující kroky.


1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **přehled > Uživatelé**.

    ![Vyberte uživatele odstranit](media/data-box-edge-manage-users/delete-user-1.png)

2. Vyberte uživatele ze seznamu uživatelů a potom vyberte **odstranit**.  

   ![Vyberte odstranit](media/data-box-edge-manage-users/delete-user-2.png)

3. Po zobrazení výzvy potvrďte odstranění. 

   ![Potvrzení odstranění](media/data-box-edge-manage-users/delete-user-3.png)

Seznam uživatelů se aktualizuje a odstraněný uživatel z něj zmizí.

![Aktualizovaný seznam uživatelů](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Další postup

- Další informace o [správě šířky pásma](data-box-edge-manage-bandwidth-schedules.md).
