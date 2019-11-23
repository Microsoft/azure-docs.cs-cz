---
title: Řízení přístupu k vytváření žádostí o podporu v Azure
description: Azure na základě rolí Access Control (RBAC) pro řízení přístupových práv k vytváření a správě žádostí o podporu
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure-supportability
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 328f4928aff6a892849ce3e08fe794578b5e03a4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299255"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure na základě rolí Access Control (RBAC) pro řízení přístupových práv k vytváření a správě žádostí o podporu

> [!IMPORTANT]
> Tento ruční přístup k řízení přístupových práv k žádosti o podporu v Azure byl nahrazen integrovanou rolí. Další informace najdete v tématu [Podpora – Přispěvatel žádostí](../role-based-access-control/built-in-roles.md#support-request-contributor). 

Správa [na Access Control základě rolí (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) pro Azure umožňuje jemně odstupňovanou správu přístupu.
Support request vytváření v Azure Portal [Portal.Azure.com](https://portal.azure.com)využívá model RBAC Azure k definování, kdo může vytvářet a spravovat žádosti o podporu.
Přístup je udělen přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím v určitém oboru, což může být předplatné, skupina prostředků nebo prostředek.

Pojďme si příklad: jako vlastníka skupiny prostředků s oprávněním ke čtení v oboru předplatného můžete spravovat všechny prostředky ve skupině prostředků, jako jsou weby, virtuální počítače a podsítě.
Když se ale pokusíte vytvořit žádost o podporu u prostředku virtuálního počítače, narazíte na následující chybu.

![Chyba předplatného](./media/create-manage-support-requests-using-access-control/subscription-error.png)

V části Podpora správy žádostí potřebujete oprávnění k zápisu nebo roli, která má akci podpory Microsoft. support/* v oboru předplatného, aby bylo možné vytvářet a spravovat žádosti o podporu.

Následující článek vysvětluje, jak můžete pomocí vlastního Access Control založeného na rolích (RBAC) Azure vytvářet a spravovat žádosti o podporu v Azure Portal.

## <a name="getting-started"></a>Začínáme

Pomocí výše uvedeného příkladu byste mohli vytvořit žádost o podporu pro váš prostředek, pokud jste vlastníkem předplatného přiřadili vlastní roli RBAC.
[Vlastní role RBAC](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) se dají vytvořit pomocí Azure PowerShell, rozhraní příkazového řádku Azure (CLI) a REST API.

Vlastnost Actions vlastní role určuje operace Azure, do kterých role uděluje přístup.
Chcete-li vytvořit vlastní roli pro správu žádostí o podporu, role musí mít akci Microsoft. support/*.

Tady je příklad vlastní role, kterou můžete použít k vytváření a správě žádostí o podporu.
Pojmenovali jsme tuto roli Přispěvatel žádostí o podporu a to je způsob, jak odkazovat na vlastní roli v tomto článku.

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

Podle kroků uvedených v [tomto videu](https://www.youtube.com/watch?v=-PaBaDmfwKI) se dozvíte, jak vytvořit vlastní roli pro vaše předplatné.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Vytváření a správa žádostí o podporu v Azure Portal

Pojďme si příklad – jste vlastníkem předplatného sady Visual Studio MSDN.
Jana je váš partner, který je vlastníkem prostředku u některých skupin prostředků v tomto předplatném a má oprávnění číst k tomuto předplatnému.
Chcete udělit přístup k vašemu partnerovi, Jana, možnost vytvářet a spravovat lístky podpory pro prostředky v rámci tohoto předplatného.

1. Prvním krokem je přejít k předplatnému. V části **Nastavení**se zobrazí seznam uživatelů. Vyberte uživatele Jana, který má v předplatném přístup čtenářů. Pojďme k Jana přiřadit novou vlastní roli.

    ![Přidat roli](./media/create-manage-support-requests-using-access-control/add-role.png)

2. V okně Uživatelé klikněte na Přidat. V seznamu rolí vyberte vlastní roli Přispěvatel žádostí o podporu.

    ![Přidat roli Přispěvatel podpory](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Po výběru názvu role klikněte na Přidat uživatele a zadejte e-mailové přihlašovací údaje Jana. Klikněte na Vybrat.

    ![Přidání uživatelů](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Pokračujte kliknutím na OK.

    ![Přidat přístup](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Nyní se zobrazí uživatel s nově přidanou vlastní rolí "Přispěvatel žádostí o podporu" v rámci předplatného, pro které jste vlastníkem.

    ![Přidáno uživatelem](./media/create-manage-support-requests-using-access-control/user-added.png)

    Když Jana přihlašujete na portál, Jana uvidí předplatné, ke kterému bylo přidáno Jana.

7. Jana klikne na nový Support request v okně pomoc a podpora a může vytvořit žádosti o podporu pro Visual Studio Ultimate with MSDN.

    ![Nová žádost o podporu](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Kliknutím na položku všechny žádosti o podporu se zobrazí seznam žádostí o podporu vytvořených pro toto předplatné ![zobrazení podrobností případu](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Odebrat přístup k žádosti o podporu v Azure Portal

Stejně jako je možné udělit uživateli přístup k vytváření a správě žádostí o podporu, je také možné odebrat přístup pro uživatele.

Pokud chcete odebrat možnost vytvářet a spravovat žádosti o podporu, přejděte na předplatné, klikněte na nastavení a klikněte na uživatele (v tomto případě Jana). Klikněte pravým tlačítkem na název role, na "Přispěvatel žádostí o podporu" a klikněte na odebrat.

![Odebrat přístup k žádosti o podporu](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Když se Jana přihlásí k portálu a pokusí se vytvořit žádost o podporu, narazí na tuto chybu:

![Chyba odběru – 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Jana nemůže zobrazit žádné žádosti o podporu, když Jana vybere možnost všechny žádosti o podporu.

![zobrazení podrobností případu – 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
