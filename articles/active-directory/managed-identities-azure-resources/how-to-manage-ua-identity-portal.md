---
title: Správa spravované identity přiřazené uživatelem v Azure Portal – Azure AD
description: Podrobné pokyny, jak vytvořit, vypsat, odstranit a přiřadit roli k spravované identitě přiřazené uživatelem.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244131"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Vytvoření, vypsání, odstranění nebo přiřazení role k spravované identitě přiřazené uživatelem pomocí Azure Portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se naučíte, jak pomocí webu Azure Portal vytvořit, vypsat, odstranit nebo přiřadit roli spravované identitě přiřazené uživateli.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a vytvořte spravovanou identitu přiřazenou uživatelem.
2. Do vyhledávacího pole zadejte *spravované identity*a v části **služby**klikněte na **spravované identity**.
3. Klikněte na tlačítko **Přidat** a zadejte hodnoty do následujících polí v části **vytvořit uživatelem spravovanou identitu přiřazenou uživateli** :
   - **Název prostředku**: Jedná se o název vaší uživatelsky přiřazené spravované identity, například UAI1.
   - **Předplatné**: vyberte předplatné, ve kterém chcete vytvořit spravovanou identitu přiřazenou uživatelem.
   - **Skupina prostředků**: Vytvořte novou skupinu prostředků, která bude obsahovat vaši uživatelem přiřazenou spravovanou identitu, nebo vyberte **použít existující** a vytvořte spravovanou identitu přiřazenou uživatelem v existující skupině prostředků.
   - **Umístění**: vyberte umístění pro nasazení spravované identity přiřazené uživatelem, například **západní USA**.
4. Klikněte na možnost **Vytvořit**.

![Vytvoření spravované identity přiřazené uživatelem](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a seznamte se se spravovanými identitami přiřazenými uživatelem.
2. Do vyhledávacího pole zadejte *spravované identity*a v části služby klikněte na **spravované identity**.
3. Vrátí se seznam spravovaných identit přiřazených uživateli pro vaše předplatné.  Chcete-li zobrazit podrobnosti o spravované identitě přiřazené uživatelem, klikněte na její název.

![Výpis spravované identity přiřazené uživatelem](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Odstranění identity přiřazené uživatelem ji neodebere z virtuálního počítače nebo prostředku, ke kterému byl přiřazen.  Pokud chcete odebrat identitu přiřazenou uživatelem z virtuálního počítače, přečtěte si článek [Odebrání spravované identity přiřazené uživatelem z virtuálního počítače](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a odstraňte spravovanou identitu přiřazenou uživatelem.
2. Vyberte spravovanou identitu přiřazenou uživatelem a klikněte na **Odstranit**.
3. V poli potvrzení vyberte **Ano**.

![Odstranit spravovanou identitu přiřazenou uživatelem](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Přiřazení role k spravované identitě přiřazené uživatelem 

Pokud chcete přiřadit roli k spravované identitě přiřazené uživateli, váš účet potřebuje přiřazení role [Správce přístupu uživatele](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a seznamte se se spravovanými identitami přiřazenými uživatelem.
2. Do vyhledávacího pole zadejte *spravované identity*a v části služby klikněte na **spravované identity**.
3. Vrátí se seznam spravovaných identit přiřazených uživateli pro vaše předplatné.  Vyberte spravovanou identitu přiřazenou uživatelem, kterému chcete přiřadit roli.
4. Vyberte **řízení přístupu (IAM)** a pak vyberte **Přidat přiřazení role**.

   ![Spuštění uživatelsky přiřazené spravované identity](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. V okně Přidat přiřazení role nakonfigurujte následující hodnoty a potom klikněte na **Uložit**:
   - **Role** – role, která se má přiřadit
   - **Přiřazení přístupu k** prostředku k přiřazení spravované identity přiřazené uživatelem
   - **Vybrat** – člen pro přiřazení přístupu
   
   ![Uživatelsky přiřazená spravovaná identita IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
