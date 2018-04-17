---
title: Azure na základě rolí řízení přístupu (RBAC) do ovládacího prvku přístupová práva k vytváření a správě žádosti o podporu | Microsoft Docs
description: Azure na základě rolí řízení přístupu (RBAC) do ovládacího prvku přístupová práva k vytváření a správě žádosti o podporu
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: c3557d54b9fb3fd0173eec1e327050ac7cc80fc2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure na základě rolí řízení přístupu (RBAC) do ovládacího prvku přístupová práva k vytváření a správě žádosti o podporu

[Řízení přístupu na základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) umožňuje vyladění správy přístupu pro Azure.
Při vytváření žádosti o podporu na portálu Azure [portal.azure.com](https://portal.azure.com), používá Azure RBAC model můžete definovat, kdo může vytvářet a spravovat žádosti o podporu.
Přístup uděluje přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím na určité obor, což může být předplatné, skupinu prostředků nebo prostředek.

Podívejme příklad: jako vlastník skupiny prostředků s oprávněními ke čtení v oboru předplatné, můžete spravovat všechny prostředky ve skupině prostředků, jako jsou weby, virtuální počítače a podsítě.
Ale když se pokusíte vytvořit žádost o podporu pro prostředek virtuálního počítače, dojde k následující chybě

![Chyba odběru](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Ve správě žádosti o podporu třeba zápisu oprávnění nebo role, kterou má akce podporu Microsoft.Support/* na obor předplatného, abyste mohli vytvářet a spravovat žádosti o podporu.

V následujícím článku vysvětluje, jak můžete vytvořit a spravovat žádosti o podporu na portálu Azure pomocí Azure vlastní na základě rolí řízení přístupu (RBAC).

## <a name="getting-started"></a>Začínáme

Pomocí výše uvedeném příkladu, bude moci vytvořit žádost o podporu pro prostředek, pokud vlastní role RBAC u předplatného se přiřadila vlastník předplatného.
[Vlastní role RBAC](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) lze vytvořit pomocí Azure PowerShell, rozhraní příkazového řádku Azure (CLI) a rozhraní REST API.

Vlastnost akce vlastní role určuje činnosti Azure, ke kterým roli udělí přístup.
Pokud chcete vytvořit vlastní role pro správu žádosti o podporu, musí mít roli akce Microsoft.Support/*

Tady je příklad vlastní roli, které můžete použít k vytvoření a správa žádostí o podporu.
Jsme jste s názvem "Přispěvatel žádosti o podporu" této role, kterým je, jak jsme odkazují na vlastní role v tomto článku.

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

Postupujte podle kroků uvedených v [toto video](https://www.youtube.com/watch?v=-PaBaDmfwKI) se dozvíte, jak vytvářet vlastní role pro vaše předplatné.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Vytvoření a správa žádostí o podporu na portálu Azure

Podívejme příklad – jste vlastníkem předplatného "Visual Studio předplatné MSDN."
Jan je vaše sdílená, kdo je vlastníkem prostředku pro některé skupiny prostředků v tomto předplatném a má oprávnění ke čtení pro předplatné.
Chcete poskytnout přístup k vaší sdílené, Jan, umožňuje vytvářet a spravovat lístky žádostí o podporu pro prostředky pod tímto předplatným.

1. Prvním krokem je přejděte na předplatné a v části "Nastavení" zobrazí seznam uživatelů. Klikněte na uživatele Jan, kdo má přístup čtečky u předplatného a umožňuje přiřadit novou vlastní roli mu.

    ![Přidání role](./media/create-manage-support-requests-using-access-control/add-role.png)

2. V části "Uživatelé" okna klikněte na tlačítko "Přidat". Vyberte vlastní roli "Přispěvatel žádosti o podporu" ze seznamu rolí

    ![Přidání role Přispěvatel podpory](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Po výběru název role, klikněte na tlačítko "Přidat uživatele" a zadejte přihlašovací údaje Michalův e-mailu. Klikněte na tlačítko "Vyberte"

    ![Přidání uživatelů](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klikněte na tlačítko "Ok", aby bylo možné pokračovat

    ![Přidat přístup](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Nyní se zobrazí uživatele s nově přidané vlastní role "Podporu požadavků Přispěvatel" v rámci předplatného, pro který jste vlastníkem

    ![Přidat uživatele](./media/create-manage-support-requests-using-access-control/user-added.png)

    Jan přihlásí na portálu, zjistí předplatné, do které byl přidán.

7. Jan klikne na možnost "Nová žádost o podporu" v okně "Nápověda a podpora" a můžete vytvořit žádosti o podporu pro "Visual Studio Ultimate s MSDN"

    ![Nová žádost o podporu](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Kliknutím na tlačítko "Podporují všechny požadavky" Jan můžete zobrazit seznam žádostí o podporu pro toto předplatné vytvořit ![případ zobrazení podrobností](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Odebrat přístup žádosti o podporu na portálu Azure

Stejně, jako je možné k udělení přístupu k uživatelům vytvářet a spravovat žádosti o podporu, je možné k odebrání přístupu pro uživatele i.
Chcete-li odebrat možnost vytvořit a spravovat žádosti o podporu, přejděte na předplatné, klikněte na tlačítko "Nastavení" a klikněte na uživateli (v tomto případě Jan).
Klikněte pravým tlačítkem na název role, "Přispěvatel žádosti o podporu" a klikněte na tlačítko "Odebrat"

![Odebrat přístup žádosti o podporu](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Když Jan přihlásí k portálu a pokusí se vytvořit žádost o podporu, mohl dojde k následující chybě

![Předplatné chyba-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Jan nelze zobrazit žádné podporovat požadavky, pokud klikne "Podporují všechny požadavky"

![zobrazení podrobností případu-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
