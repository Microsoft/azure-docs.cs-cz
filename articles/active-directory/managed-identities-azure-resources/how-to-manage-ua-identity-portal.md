---
title: Správa spravované identity přiřazené uživateli na webu Azure Portal – Azure AD
description: Podrobné pokyny k vytvoření, vypsat, odstranit a přiřadit roli spravované identitě přiřazené uživateli.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244131"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Vytvoření, vypsat, odstranit nebo přiřadit roli spravované identitě přiřazené uživateli pomocí webu Azure Portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služeb, které podporují ověřování Azure AD, bez nutnosti pověření ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, seznam, odstranit nebo přiřadit roli uživatelem přiřazené spravované identity pomocí portálu Azure Portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Chcete-li vytvořit spravovanou identitu přiřazenou uživateli, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure k vytvoření spravované identity přiřazené uživateli.
2. Do vyhledávacího pole zadejte *spravované identity*a v části **Služby**klikněte na **položku Spravované identity**.
3. Klikněte na **Přidat** a zadejte hodnoty do následujících polí v podokně **Vytvořit uživatele přiřazenou spravovanou** identitu:
   - **Název prostředku**: Toto je název spravované identity přiřazené uživateli, například UAI1.
   - **Předplatné**: Zvolte předplatné pro vytvoření uživatelem přiřazené spravované identity v části
   - **Skupina prostředků**: Vytvořte novou skupinu prostředků, která bude obsahovat spravovanou identitu přiřazenou uživatelem, nebo zvolte **Použít existující** k vytvoření spravované identity přiřazené uživateli v existující skupině prostředků.
   - **Umístění**: Zvolte umístění pro nasazení uživatelem přiřazené spravované identity, například **západní USA**.
4. Klikněte na **Vytvořit**.

![Vytvoření spravované identity přiřazené uživatelem](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Seznam spravovaných identit přiřazených uživatelem

Chcete-li vypsat/přečíst spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Operátor spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přiřazení přispěvatele spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a seznamte spravované identity přiřazené uživateli.
2. Do vyhledávacího pole zadejte *spravované identity*a v části Služby klikněte na **Spravované identity**.
3. Je vrácen seznam uživatelem přiřazených spravovaných identit pro vaše předplatné.  Chcete-li zobrazit podrobnosti o spravované identitě přiřazené uživateli, klikněte na její název.

![Seznam spravované identity přiřazené uživateli](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Chcete-li odstranit spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Odstraněním uživatele přiřazené identity ji neodeberete z virtuálního aplikace nebo prostředku, ke kterým byl přiřazen.  Pokud chcete odebrat uživatele přiřazenou identitu z virtuálního uživatele, [přečtěte si, že odeberete spravovanou identitu přiřazenou uživateli z virtuálního virtuálního uživatele](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure k odstranění spravované identity přiřazené uživateli.
2. Vyberte spravovanou identitu přiřazenou uživatelem a klepněte na tlačítko **Odstranit**.
3. Pod potvrzovacím polem zvolte **Ano**.

![Odstranění spravované identity přiřazené uživateli](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Přiřazení role spravované identitě přiřazené uživateli 

Chcete-li přiřadit roli spravované identitě přiřazené uživateli, potřebuje váš účet přiřazení role [Správce přístupu uživatelů.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure a seznamte spravované identity přiřazené uživateli.
2. Do vyhledávacího pole zadejte *spravované identity*a v části Služby klikněte na **Spravované identity**.
3. Je vrácen seznam uživatelem přiřazených spravovaných identit pro vaše předplatné.  Vyberte uživatelem přiřazenou spravovanou identitu, kterou chcete přiřadit roli.
4. Vyberte **Řízení přístupu (IAM)** a pak vyberte **Přidat přiřazení role**.

   ![Začátek spravované identity přiřazený uživateli](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. V okně Přidat přiřazení role nakonfigurujte následující hodnoty a klepněte na tlačítko **Uložit**:
   - **Role** - role, kterou chcete přiřadit
   - **Přiřazení přístupu k** prostředku, který přiřadí uživatelem přiřazenou spravovanou identitu
   - **Vybrat** - člen pro přiřazení přístupu
   
   ![IAM přiřazená uživatelem](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
