---
title: Správa uživatelsky přiřazené spravovanou identitu pomocí webu Azure portal
description: Identita spravované podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatel přiřazenou.
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
ms.openlocfilehash: 6729bee9bfebd8e80ae3b791dc2a8a480ac8b525
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158718"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-portal"></a>Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovanou identitu pomocí webu Azure portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, seznamu a odstraňovat uživatelsky přiřazené spravovanou identitu pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření, čtení (list), aktualizovat a odstranit spravované identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) role ke čtení vlastnosti uživatelsky přiřazené spravovanou identitu (seznam).

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření uživatelsky přiřazené identity spravované

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure k vytvoření uživatelsky přiřazené spravovaná identita.
2. Do vyhledávacího pole zadejte *identit spravovaných*a v části **služby**, klikněte na tlačítko **identit spravovaných**.
3. Klikněte na tlačítko **přidat** a zadejte hodnoty do následujících polí v rámci **přiřazené uživateli vytvořit spravované** podokně identity:
   - **Název prostředku**: Toto je název pro váš uživatel přiřazenou spravovanou identitu, třeba UAI1.
   - **Předplatné**: Vyberte předplatné, pro vytvoření uživatelsky přiřazené spravované identity v části
   - **Skupina prostředků**: Vytvořte novou skupinu prostředků a obsahovat vaše spravovaná identita uživatelsky přiřazené nebo zvolte **použít existující** vytvoření uživatelsky přiřazené spravovanou identitu ve stávající skupině prostředků.
   - **Umístění**: Vyberte umístění, kam chcete nasadit uživatelsky přiřazené spravovanou identitu, třeba **USA – západ**.
4. Klikněte na možnost **Vytvořit**.

![Vytvoření uživatelsky přiřazené identity spravované](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

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