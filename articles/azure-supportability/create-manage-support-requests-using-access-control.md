---
title: Azure na základě rolí řízení přístupu (RBAC) k řízení přístupová práva k vytváření a správě žádostí o podporu | Dokumentace Microsoftu
description: Azure na základě rolí řízení přístupu (RBAC) k řízení přístupová práva k vytváření a správě žádostí o podporu
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809784"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure na základě rolí řízení přístupu (RBAC) k řízení přístupová práva k vytváření a správě žádostí o podporu

[Řízení přístupu na základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) umožňuje přesnou správu přístupu pro Azure.
Vytvoření žádosti o podporu na webu Azure Portal, [portal.azure.com](https://portal.azure.com), používá Azure RBAC model definovat, kdo může vytvářet a spravovat žádosti o podporu.
Přístup uděluje přiřazením příslušné role RBAC pro uživatele, skupiny nebo aplikace v určitém rozsahu, který může být předplatné, skupinu prostředků nebo prostředek.

Vezměme si příklad: Jako vlastníka skupiny prostředků s oprávněním pro čtení v oboru předplatného můžete spravovat všechny prostředky ve skupině prostředků, jako jsou weby, virtuální počítače a podsítě.
Ale při pokusu o vytvoření žádosti o podporu pro prostředek virtuálního počítače narazíte na následující chybu

![Chyba odběru](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Ve správě žádost o podporu třeba psát oprávnění nebo roli, která má akce podporu Microsoft.Support/* v oboru předplatného, abyste mohli vytvářet a spravovat žádosti o podporu.

V následujícím článku vysvětluje, jak můžete Azure vlastní Role-Based řízení přístupu (RBAC) můžete vytvářet a spravovat žádosti o podporu na webu Azure Portal.

## <a name="getting-started"></a>Začínáme

Použití výše uvedeného příkladu, bude moct vytvořit žádost o podporu pro prostředek, pokud byly přiřazeny vlastní roli RBAC pro předplatné vlastníkem předplatného.
[Vlastní role RBAC](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) lze vytvořit pomocí Azure Powershellu, rozhraní příkazového řádku Azure (CLI) a rozhraní REST API.

Vlastnost akcí vlastní role určuje operací Azure, do kterých role uděluje přístup.
Chcete-li vytvořit vlastní role pro správu žádost o podporu, musí mít roli akce Microsoft.Support/*

Tady je příklad vlastní roli, která vám pomůže vytvořit a spravovat žádosti o podporu.
Jsme Pojmenovali jste tuto roli "Podpory požádat o Přispěvatel" a to je, jak jsme odkazovat na vlastní roli, v tomto článku.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Postupujte podle kroků uvedených v [toto video](https://www.youtube.com/watch?v=-PaBaDmfwKI) se naučíte vytvářet vlastní role pro vaše předplatné.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Vytvořit a spravovat žádosti o podporu na webu Azure Portal

Vezměme si příklad – je vlastníkem předplatného "MSDN předplatné sady Visual Studio."
U Jana se uplatní vašeho partnera, který je vlastníkem prostředku do některých skupin prostředků v tomto předplatném a má oprávnění ke čtení pro předplatné.
Chcete poskytnout přístup k partnerské, Joe, možnost vytvářet a spravovat lístky podpory pro prostředky v rámci tohoto předplatného.

1. Prvním krokem je přejít na předplatné a v části "Nastavení" se zobrazí seznam uživatelů. Klikněte na uživatele Joe, kdo má přístup Čtenář pro předplatné a můžeme mu přiřadit novou vlastní roli.

    ![Přidat roli](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Klikněte na tlačítko "Přidat" pod oknem "Users". Vyberte vlastní roli "Podpory požádat o Přispěvatel" ze seznamu rolí

    ![Přidání podpory role přispěvatele](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Po výběru název role, klikněte na tlačítko "Přidat uživatele" a zadejte přihlašovací údaje Joe e-mailu. Klikněte na Vybrat.

    ![Přidání uživatelů](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klikněte na tlačítko "Ok" aby bylo možné pokračovat

    ![Přidat přístup](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Nyní vidíte uživatele s nově přidané "Podpory požádat o Přispěvatel" v rámci předplatného, u kterého jste vlastníkem vlastní role

    ![Uživatel přidal](./media/create-manage-support-requests-using-access-control/user-added.png)

    Joe protokoly na portálu, zjistí předplatné, do které byl přidán.

7. Jan klikne na tlačítko "Novou žádost o podporu" v okně "Nápověda a podpora" a můžete vytvořit žádosti o podporu pro "Visual Studio Ultimate s MSDN"

    ![Nová žádost o podporu](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Kliknutím na "Všechny žádosti o podporu" Joe můžete zobrazit seznam žádostí o podporu pro toto předplatné vytvořili ![případ zobrazení podrobností](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Odebrání podpory požádat o přístup na webu Azure Portal

Stejně, jako je možné udělit přístup k uživateli a vytvářet a spravovat žádosti o podporu, je možné odebrat přístup pro uživatele i.
Chcete-li odebrat možnost vytvářet a spravovat žádosti o podporu, přejděte na předplatné, klikněte na "Nastavení" a klikněte na uživatele (v tomto případě Joe).
Klikněte pravým tlačítkem na název role, "Podpory požádat o Přispěvatel" a klikněte na "Remove"

![Odebrání podpory požádat o přístup](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Joe přihlásí k portálu a pokusí se vytvořit žádost o podporu, má k následující chybě dochází

![Předplatné chyby-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe nelze zobrazit žádné žádosti o podporu, když se klikne na tlačítko "Všechny žádosti o podporu"

![Zobrazit podrobnosti o případu-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
