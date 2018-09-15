---
title: Správa uživatelsky přiřazené spravovanou identitu pomocí webu Azure portal
description: Podrobné pokyny o tom, jak vytvořit seznam, odstranit a spravované identity přiřazené uživateli přiřadit roli.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 180d4092a2570b719e77d98319ab9b329f2e48c5
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637426"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Vytváření, výpisu, odstranit nebo přiřadit roli uživatele přiřazeny spravovanou identitu pomocí webu Azure portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, výpis, odstranění nebo přiřazení role přiřazené uživatele spravované identity pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření, čtení (list), aktualizovat a odstranit spravované identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) role ke čtení vlastnosti uživatelsky přiřazené spravovanou identitu (seznam).

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure k vytvoření uživatelsky přiřazené spravovaná identita.
2. Do vyhledávacího pole zadejte *identit spravovaných*a v části **služby**, klikněte na tlačítko **identit spravovaných**.
3. Klikněte na tlačítko **přidat** a zadejte hodnoty do následujících polí v rámci **přiřazené uživateli vytvořit spravované** podokně identity:
   - **Název prostředku**: Toto je název pro váš uživatel přiřazenou spravovanou identitu, třeba UAI1.
   - **Předplatné**: Vyberte předplatné, pro vytvoření uživatelsky přiřazené spravované identity v části
   - **Skupina prostředků**: Vytvořte novou skupinu prostředků a obsahovat vaše spravovaná identita uživatelsky přiřazené nebo zvolte **použít existující** vytvoření uživatelsky přiřazené spravovanou identitu ve stávající skupině prostředků.
   - **Umístění**: Vyberte umístění, kam chcete nasadit uživatelsky přiřazené spravovanou identitu, třeba **USA – západ**.
4. Klikněte na možnost **Vytvořit**.

![Vytvoření spravované identity přiřazené uživatelem](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Seznam uživatelsky přiřazené spravované identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) seznam uživatelsky přiřazené identity spravované pomocí účtu přidružených k předplatnému Azure.
2. Do vyhledávacího pole zadejte *identit spravovaných*a v rámci služeb, klikněte na tlačítko **identit spravovaných**.
3. Seznam uživatelsky přiřazené spravované identity pro vaše předplatné se vrátí.  Pokud chcete zobrazit podrobnosti uživatelsky přiřazené identity spravované, klikněte na jeho název.

![Seznam uživatelsky přiřazené identity spravované](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použití účet spojený s předplatným Azure pro odstranění spravované identity přiřazené uživateli.
2. Vyberte spravované uživatelsky přiřazené identity a klikněte na tlačítko **odstranit**.
3. V potvrzovacím okně vyberete, **Ano**.

![Odstranit uživatelsky přiřazené identity spravované](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Přiřazení role uživatel přiřazenou spravované identity 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) seznam uživatelsky přiřazené identity spravované pomocí účtu přidružených k předplatnému Azure.
2. Do vyhledávacího pole zadejte *identit spravovaných*a v rámci služeb, klikněte na tlačítko **identit spravovaných**.
3. Seznam uživatelsky přiřazené spravované identity pro vaše předplatné se vrátí.  Vyberte uživatelsky přiřazené spravovanou identitu, kterou chcete přiřadit roli.
4. Vyberte **řízení přístupu (IAM)** a pak vyberte **přidat**.

   ![Uživatelsky přiřazené identity spravované spuštění](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. V okně Přidat oprávnění nakonfigurujte následující hodnoty a pak klikněte na tlačítko **Uložit**:
   - **Role** -roli, kterou chcete přiřadit
   - **Přiřazení přístupu k** -prostředek, který chcete přiřadit uživateli přiřazena spravované identity
   - **Vyberte** – člen, který chcete přiřadit přístup
   
   ![Uživatelsky přiřazené identity spravované IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  