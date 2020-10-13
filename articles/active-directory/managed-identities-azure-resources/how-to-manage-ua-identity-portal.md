---
title: Správa spravované identity přiřazené uživatelem v Azure Portal – Azure AD
description: Podrobné pokyny, jak vytvořit, vypsat, odstranit a přiřadit roli k spravované identitě přiřazené uživatelem.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ad91c916a6134f2507e74df6e87478421a00f43
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977421"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Vytvoření, vypsání, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal

Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se naučíte, jak pomocí Azure Portal vytvořit, vypsat, odstranit nebo přiřadit roli k spravované identitě přiřazené uživateli.

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a vytvořte spravovanou identitu přiřazenou uživatelem.
2. Do vyhledávacího pole zadejte *spravované identity*a v části **služby**klikněte na **spravované identity**.
3. Klikněte na tlačítko **Přidat** a zadejte hodnoty do následujících polí v části **vytvořit uživatelem spravovanou identitu přiřazenou uživateli** :
    - **Předplatné**: vyberte předplatné, ve kterém chcete vytvořit spravovanou identitu přiřazenou uživatelem.
    - **Skupina prostředků**: vyberte skupinu prostředků, ve které chcete vytvořit spravovanou identitu přiřazenou uživatelem, nebo klikněte na **vytvořit nový** a vytvořte novou skupinu prostředků.
    - **Oblast**: Vyberte oblast pro nasazení spravované identity přiřazené uživatelem, například **západní USA**.
    - **Název**: Jedná se o název vaší uživatelsky přiřazené spravované identity, například UAI1.
    ![Vytvoření spravované identity přiřazené uživatelem](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Změny Projděte kliknutím na tlačítko **zkontrolovat + vytvořit** .
5. Klikněte na **Vytvořit**.

## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a seznamte se se spravovanými identitami přiřazenými uživatelem.
2. Do vyhledávacího pole zadejte *spravované identity*a v části služby klikněte na **spravované identity**.
3. Vrátí se seznam spravovaných identit přiřazených uživateli pro vaše předplatné.  Chcete-li zobrazit podrobnosti o spravované identitě přiřazené uživatelem, klikněte na její název.

![Výpis spravované identity přiřazené uživatelem](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Odstranění identity přiřazené uživatelem ji neodebere z virtuálního počítače nebo prostředku, ke kterému byl přiřazen.  Pokud chcete odebrat identitu přiřazenou uživatelem z virtuálního počítače, přečtěte si článek [Odebrání spravované identity přiřazené uživatelem z virtuálního počítače](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a odstraňte spravovanou identitu přiřazenou uživatelem.
2. Vyberte spravovanou identitu přiřazenou uživatelem a klikněte na **Odstranit**.
3. V poli potvrzení vyberte **Ano**.

![Odstranit spravovanou identitu přiřazenou uživatelem](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Přiřazení role k spravované identitě přiřazené uživatelem 

Pokud chcete přiřadit roli k spravované identitě přiřazené uživateli, váš účet potřebuje přiřazení role [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a seznamte se se spravovanými identitami přiřazenými uživatelem.
2. Do vyhledávacího pole zadejte *spravované identity*a v části služby klikněte na **spravované identity**.
3. Vrátí se seznam spravovaných identit přiřazených uživateli pro vaše předplatné.  Vyberte spravovanou identitu přiřazenou uživatelem, kterému chcete přiřadit roli.
4. Vyberte **řízení přístupu (IAM)** a pak vyberte **Přidat přiřazení role**.

   ![Spuštění uživatelsky přiřazené spravované identity](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. V okně Přidat přiřazení role nakonfigurujte následující hodnoty a potom klikněte na **Uložit**:
   - **Role** – role, která se má přiřadit
   - **Přiřazení přístupu k**  prostředku k přiřazení spravované identity přiřazené uživatelem
   - **Vybrat** – člen pro přiřazení přístupu
   
   ![Uživatelsky přiřazená spravovaná identita IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)
