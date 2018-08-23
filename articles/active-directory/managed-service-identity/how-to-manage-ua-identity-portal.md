---
title: Správa uživatel přiřazené spravovanou identitu pomocí webu Azure portal
description: Spravované identity přiřazené podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatele.
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
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060750"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Vytvoření seznamu nebo odstranění uživatele přiřazeny identity pomocí webu Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak k vytváření, výpisu a odstranění uživatele přiřazeny identity pomocí webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření, čtení (list), aktualizace a odstranění identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) role ke čtení vlastnosti identity přiřazené uživateli (seznam).

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživateli

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu přidružených k předplatnému Azure pro vytvoření uživatele přiřazeny spravovaná identita.
2. Do vyhledávacího pole zadejte *identit spravovaných*a v části **služby**, klikněte na tlačítko **identit spravovaných**.
3. Klikněte na tlačítko **přidat** a zadejte hodnoty do následujících polí v rámci **přiřazené uživateli vytvořit spravované** podokně identity:
   - **Název prostředku**: Jedná se o název pro vaše uživatele přiřazeny spravované identity, například UAI1.
   - **Předplatné**: Vyberte předplatné, k vytvoření spravované identity v části přiřazené uživateli
   - **Skupina prostředků**: Vytvořte novou skupinu prostředků a obsahovat vaše identity přiřazené uživateli nebo zvolte **použít existující** pro vytvoření uživatele přiřazeny spravovanou identitu ve stávající skupině prostředků.
   - **Umístění**: Vyberte umístění, kam chcete nasadit uživatel s přiřazenou spravovanou identitu, třeba **USA – západ**.
4. Klikněte na možnost **Vytvořit**.

![Vytvoření spravované identity přiřazené uživateli](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>Uživatel seznamu identit přiřazených

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure do seznamu uživateli přiřadit spravovaných identit.
2. Do vyhledávacího pole zadejte *identit spravovaných*a v rámci služeb, klikněte na tlačítko **identit spravovaných**.
3. Seznam uživatelů, přiřazené spravovaných identit pro vaše předplatné se vrátí.  Zobrazit podrobnosti o uživateli přiřadit klikněte na jeho název.

![Seznam spravovaných identity přiřazené uživateli](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>Odstranit identity přiřazené uživateli

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure k odstranění uživatele přiřazeny spravovaná identita.
2. Vyberte identity přiřazené uživateli a klikněte na tlačítko **odstranit**.
3. V potvrzovacím okně vyberete, **Ano**.

![Odstranit spravovanou identitu přiřazenou uživatelem](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)