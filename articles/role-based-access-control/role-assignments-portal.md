---
title: Přidání nebo odebrání přiřazení rolí pomocí RBAC a portálu Azure
description: Zjistěte, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí řízení přístupu na základě rolí Azure (RBAC) a portálu Azure.
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
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246354"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a portálu Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Tento článek popisuje, jak přiřadit role pomocí portálu Azure.

Pokud potřebujete přiřadit role správce ve službě Azure Active Directory, přečtěte si část [Zobrazení a přiřazení rolí správce ve službě Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, musíte mít:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, například [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) nebo [Vlastník](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Řízení přístupu (IAM)

**Řízení přístupu (IAM)** je okno, které slouží k přiřazení rolí k udělení přístupu k prostředkům Azure. Je také označován jako správa identit a přístupu a zobrazí se na několika místech na webu Azure Portal. Následující ukazuje příklad okna řízení přístupu (IAM) pro předplatné.

![Okno řízení přístupu (IAM) pro předplatné](./media/role-assignments-portal/access-control-subscription.png)

Chcete-li být nejúčinnější s okno řízení přístupu (IAM), pomáhá, pokud můžete odpovědět na následující tři otázky, když se pokoušíte přiřadit roli:

1. **Kdo potřebuje přístup?**

    Kdo odkazuje na uživatele, skupinu, instanční objekt nebo spravovanou identitu. To se také nazývá *zaregistrovaný objekt zabezpečení*.

1. **Jakou roli potřebují?**

    Oprávnění jsou seskupena do rolí. Můžete si vybrat ze seznamu několika [předdefinovaných rolí](built-in-roles.md) nebo můžete použít vlastní role.

1. **Kde potřebují přístup?**

    Kde odkazuje na sadu prostředků, které se vztahuje na přístup. Kde může být skupina pro správu, předplatné, skupina prostředků nebo jeden prostředek, jako je například účet úložiště. Tento obor se nazývá *obor*.

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

V Azure RBAC, chcete-li udělit přístup k prostředku Azure, přidáte přiřazení role. Chcete-li přiřadit roli, postupujte takto.

1. Na webu Azure Portal klikněte na **Všechny služby** a vyberte obor, ke kterému chcete udělit přístup. Můžete například vybrat **skupiny pro správu**, **Odběry**, **Skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek pro daný obor.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Přiřazení rolí** zobrazíte přiřazení rolí v tomto oboru.

    ![Karta Řízení přístupu (IAM) a přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

1. Klikněte na **Přidat** > **přiřazení role**.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost Přidat přiřazení role zakázána.

   ![Nabídka Přidat](./media/role-assignments-portal/add-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Kliknutím na **Uložit** roli přiřaďte.

   Po několika okamžicích je objektu zabezpečení přiřazena role ve vybraném oboru.

    ![Přidání uloženého přiřazení role](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřazení uživatele jako správce předplatného

Chcete-li z uživatele udělat správce předplatného Azure, přiřaďte mu roli [vlastníka](built-in-roles.md#owner) v oboru předplatného. Role Vlastník poskytuje uživateli úplný přístup ke všem prostředkům v předplatném, včetně oprávnění udělit přístup ostatním. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

1. Na webu Azure Portal klikněte na **Všechny služby** a potom na **Předplatná**.

1. Klikněte na předplatné, ke kterému chcete udělit přístup.

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Přiřazení rolí** zobrazíte přiřazení rolí pro toto předplatné.

    ![Karta Řízení přístupu (IAM) a přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

1. Klikněte na **Přidat** > **přiřazení role**.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost Přidat přiřazení role zakázána.

   ![Nabídka Přidat](./media/role-assignments-portal/add-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

1. Kliknutím na **Uložit** roli přiřaďte.

   Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

V Azure RBAC, chcete-li odebrat přístup z prostředku Azure, odeberete přiřazení role. Chcete-li odebrat přiřazení role, postupujte takto.

1. Otevřít **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete odebrat přístup.

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klepněte na tlačítko **Ano**.

    Pokud se zobrazí zpráva, že zděděná přiřazení rolí nelze odebrat, pokoušíte se odebrat přiřazení role v podřízeném oboru. Ovládací prvek přístupu (IAM) byste měli otevřít v oboru, kde byla role přiřazena, a akci opakujte. Rychlý způsob, jak otevřít řízení přístupu (IAM) ve správném oboru, je podívat se na sloupec **Obor** a kliknout na odkaz vedle **(Zděděné)**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další kroky

- [Seznam přiřazení rolí pomocí Azure RBAC a portálu Azure](role-assignments-list-portal.md)
- [Kurz: Udělení přístupu uživateli k prostředkům Azure pomocí RBAC a portálu Azure](quickstart-assign-role-user-portal.md)
- [Poradce při potížích s RBAC pro prostředky Azure](troubleshooting.md)
- [Uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md)
