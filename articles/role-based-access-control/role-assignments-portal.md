---
title: Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure Portal
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě role (RBAC) v Azure a Azure Portal.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707871"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] tomto článku se dozvíte, jak přiřadit role pomocí Azure Portal.

Pokud potřebujete přiřadit role správce v Azure Active Directory, přečtěte si téma [zobrazení a přiřazení rolí správce v Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- oprávnění `Microsoft.Authorization/roleAssignments/write` a `Microsoft.Authorization/roleAssignments/delete`, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="overview-of-access-control-iam"></a>Přehled řízení přístupu (IAM)

**Řízení přístupu (IAM)** je okno, které slouží k přiřazení rolí. Označuje se taky jako Správa identit a přístupu a zobrazuje se v několika umístěních v Azure Portal. V následujícím příkladu vidíte okno řízení přístupu (IAM) pro předplatné.

![Okno řízení přístupu (IAM) pro předplatné](./media/role-assignments-portal/access-control-subscription.png)

Aby bylo v okně řízení přístupu (IAM) nejúčinnější, pomůže vám při pokusu o přiřazení role odpovědět na následující tři otázky:

1. **Kdo potřebuje přístup?**

    Kdo odkazuje na uživatele, skupinu, instanční objekt nebo spravovanou identitu. Označuje se taky jako *objekt zabezpečení*.

1. **Jakou roli potřebují?**

    Oprávnění se seskupují do rolí. Můžete vybrat ze seznamu několika [předdefinovaných rolí](built-in-roles.md) nebo můžete použít vlastní role.

1. **Kde potřebují přístup?**

    Kde se odkazuje na sadu prostředků, na které se vztahuje přístup. Kde může být skupina pro správu, předplatné, skupina prostředků nebo jeden prostředek, jako je například účet úložiště. To se označuje jako *obor*.

## <a name="add-a-role-assignment"></a>Přidání přiřazení role

Pomocí těchto kroků přiřaďte roli v různých oborech.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. Kliknutím na **přidat** > **Přidat přiřazení role** otevřete podokno přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se objektu zabezpečení přiřadí role ve vybraném oboru.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřazení uživatele jako správce předplatného

Pokud chcete, aby uživatel měl správce předplatného Azure, přiřaďte mu roli [vlastníka](built-in-roles.md#owner) v oboru předplatného. Role vlastníka přidělí uživateli úplný přístup ke všem prostředkům v rámci předplatného, včetně oprávnění pro udělení přístupu jiným uživatelům. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na předplatné, ke kterému chcete přidat přiřazení role.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. Kliknutím na **přidat** > **Přidat přiřazení role** otevřete podokno přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

1. Kliknutím na **Uložit** roli přiřaďte.

   Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

V RBAC se přístup odebírá odebrání přiřazení role. Pomocí těchto kroků odeberte přiřazení role.

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete odebrat přístup.

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

    Zděděná přiřazení rolí nejde odebrat. Pokud potřebujete odebrat zděděné přiřazení role, musíte to provést v oboru, ve kterém bylo přiřazení role vytvořené. Ve sloupci **Scope (obor** ) vedle **(Zděděno)** existuje odkaz, který vás přesměruje do rozsahu, ve kterém byla tato role přiřazena. Přejděte k oboru, který je zde uvedený, a odeberte přiřazení role.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a Azure Portal](role-assignments-list-portal.md)
- [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí RBAC a Azure Portal](quickstart-assign-role-user-portal.md)
- [Řešení potíží s RBAC pro prostředky Azure](troubleshooting.md)
- [Uspořádání prostředků s využitím skupin pro správu Azure](../governance/management-groups/overview.md)
