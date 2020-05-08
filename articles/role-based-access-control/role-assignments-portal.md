---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal – Azure RBAC
description: Přečtěte si, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure Portal a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 88cb1e9893e0ca058e57ce12f00747a59248fb19
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735687"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Tento článek popisuje, jak přiřadit role pomocí Azure Portal.

Pokud potřebujete přiřadit role správce v Azure Active Directory, přečtěte si téma [zobrazení a přiřazení rolí správce v Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="access-control-iam"></a>Řízení přístupu (IAM)

**Řízení přístupu (IAM)** je okno, pomocí kterého přiřadíte role pro udělení přístupu k prostředkům Azure. Označuje se taky jako Správa identit a přístupu a zobrazuje se v několika umístěních v Azure Portal. V následujícím příkladu vidíte okno řízení přístupu (IAM) pro předplatné.

![Okno řízení přístupu (IAM) pro předplatné](./media/role-assignments-portal/access-control-subscription.png)

Aby bylo v okně řízení přístupu (IAM) nejúčinnější, pomůže vám při pokusu o přiřazení role odpovědět na následující tři otázky:

1. **Kdo potřebuje přístup?**

    Kdo odkazuje na uživatele, skupinu, instanční objekt nebo spravovanou identitu. Označuje se taky jako *objekt zabezpečení*.

1. **Jakou roli potřebují?**

    Oprávnění se seskupují do rolí. Můžete vybrat ze seznamu několika [předdefinovaných rolí](built-in-roles.md) nebo můžete použít vlastní role.

1. **Kde potřebují přístup?**

    Kde se odkazuje na sadu prostředků, na které se vztahuje přístup. Kde může být skupina pro správu, předplatné, skupina prostředků nebo jeden prostředek, jako je například účet úložiště. To se označuje jako *obor*.

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Když ve službě Azure RBAC udělíte přístup k prostředku Azure, přidáte přiřazení role. Pomocí těchto kroků přiřaďte roli.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor, pro který chcete udělit přístup. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek pro daný obor.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte přiřazení rolí v tomto oboru.

    ![Karta pro řízení přístupu (IAM) a přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

1. Klikněte na **Přidat** > **přiřazení role přidat**.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se objektu zabezpečení přiřadí role ve vybraném oboru.

    ![Přidání přiřazení role se uložilo.](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřazení uživatele jako správce předplatného

Pokud chcete, aby uživatel měl správce předplatného Azure, přiřaďte mu roli [vlastníka](built-in-roles.md#owner) v oboru předplatného. Role vlastníka přidělí uživateli úplný přístup ke všem prostředkům v rámci předplatného, včetně oprávnění pro udělení přístupu jiným uživatelům. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na předplatné, ke kterému chcete udělit přístup.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte přiřazení rolí pro toto předplatné.

    ![Karta pro řízení přístupu (IAM) a přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

1. Klikněte na **Přidat** > **přiřazení role přidat**.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

1. Kliknutím na **Uložit** roli přiřaďte.

   Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Pokud v Azure RBAC odeberete přístup z prostředku Azure, odeberete přiřazení role. Pomocí těchto kroků odeberte přiřazení role.

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete odebrat přístup.

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

    Pokud se zobrazí zpráva, že se zděděná přiřazení rolí nedají odebrat, pokoušíte se odebrat přiřazení role v podřízeném oboru. Měli byste otevřít řízení přístupu (IAM) v oboru, kde byla role přiřazena, a akci opakovat. Rychlý způsob, jak otevřít řízení přístupu (IAM) ve správném oboru, je podívat se na sloupec **Rozsah** a kliknout na odkaz vedle **(Zděděno)**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí Azure Portal](role-assignments-list-portal.md)
- [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí Azure Portal](quickstart-assign-role-user-portal.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
- [Uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md)
