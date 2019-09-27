---
title: Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal | Microsoft Docs
description: Naučte se spravovat přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty a spravované identity pomocí řízení přístupu na základě role (RBAC) a Azure Portal. To zahrnuje výpis přístupu, udělení přístupu a odebrání přístupu.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337622"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. Tento článek popisuje, jak spravovat přístup pomocí Azure Portal. Pokud potřebujete spravovat přístup k Azure Active Directory, přečtěte si téma [zobrazení a přiřazení rolí správce v Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat a odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="overview-of-access-control-iam"></a>Přehled řízení přístupu (IAM)

**Řízení přístupu (IAM)** je okno, které slouží ke správě přístupu k prostředkům Azure. Označuje se taky jako Správa identit a přístupu a zobrazuje se v několika umístěních v Azure Portal. V následujícím příkladu vidíte okno řízení přístupu (IAM) pro předplatné.

![Okno řízení přístupu (IAM) pro předplatné](./media/role-assignments-portal/access-control-numbers.png)

Následující tabulka popisuje, k čemu některé prvky slouží:

| # | Prvek | K čemu se používá |
| --- | --- | --- |
| 1 | Prostředek, ve kterém je otevřeno řízení přístupu (IAM) | Identifikujte rozsah (předplatné v tomto příkladu) |
| 2 | Tlačítko **Přidat** | Přidat přiřazení rolí |
| 3 | **Zaškrtávací karta pro kontrolu přístupu** | Zobrazit přiřazení rolí pro jednoho uživatele |
| 4 | Karta **přiřazení rolí** | Zobrazit přiřazení rolí v aktuálním oboru |
| 5 | Karta **role** | Zobrazit všechny role a oprávnění |

Aby bylo v okně řízení přístupu (IAM) nejúčinnější, pomůže vám při pokusu o správu přístupu odpovědět na následující tři otázky:

1. **Kdo potřebuje přístup?**

    Kdo odkazuje na uživatele, skupinu, instanční objekt nebo spravovanou identitu. Označuje se taky jako *objekt zabezpečení*.

1. **Jaká oprávnění potřebují?**

    Oprávnění se seskupují do rolí. Můžete vybrat ze seznamu několika předdefinovaných rolí.

1. **Kde potřebují přístup?**

    Kde se odkazuje na sadu prostředků, na které se vztahuje přístup. Kde může být skupina pro správu, předplatné, skupina prostředků nebo jeden prostředek, jako je například účet úložiště. To se označuje jako *obor*.

## <a name="open-access-control-iam"></a>Otevřené řízení přístupu (IAM)

První věc, kterou je potřeba určit, je místo, kde otevřete okno řízení přístupu (IAM). Záleží na tom, k jakým prostředkům chcete spravovat přístup. Chcete spravovat přístup pro všechno ve skupině pro správu, vše v rámci předplatného, všechno ve skupině prostředků nebo jeden prostředek?

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor. Můžete například vybrat **skupiny pro správu**, předplatná, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek.

1. Klikněte na tlačítko **řízení přístupu (IAM)** .

    V následujícím příkladu vidíte okno řízení přístupu (IAM) pro předplatné. Pokud tady změníte nějaké změny řízení přístupu, vztahují se na celé předplatné.

    ![Okno řízení přístupu (IAM) pro předplatné](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Zobrazit role a oprávnění

Definice role je kolekce oprávnění, kterou používáte pro přiřazení rolí. Azure má víc než 70 [integrovaných rolí pro prostředky Azure](built-in-roles.md). Pomocí těchto kroků zobrazíte dostupné role a oprávnění.

1. Otevřete v jakémkoli oboru **řízení přístupu (IAM)** .

1. Kliknutím na kartu **role** zobrazíte seznam všech předdefinovaných a vlastních rolí.

   Můžete zobrazit počet uživatelů a skupin, které jsou přiřazeny k jednotlivým rolím v aktuálním oboru.

   ![Seznam Role](./media/role-assignments-portal/roles-list.png)

1. Kliknutím na jednotlivé role zobrazíte, komu byla tato role přiřazena, a také můžete zobrazit oprávnění pro danou roli.

   ![Přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

Při správě přístupu chcete zjistit, kdo má přístup, jaká jsou jejich oprávnění a v jakém oboru. Pokud chcete vypsat přístup pro uživatele, skupinu, instanční objekt nebo spravovanou identitu, můžete zobrazit jejich přiřazení rolí.

### <a name="view-role-assignments-for-a-single-user"></a>Zobrazení přiřazení rolí pro jednoho uživatele

Pomocí těchto kroků můžete zobrazit přístup pro jednoho uživatele, skupinu, instanční objekt nebo spravovanou identitu v konkrétním oboru.

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete zobrazit přístup.

1. Klikněte na kartu **kontrolovat přístup** .

    ![Řízení přístupu – karta přístup pro kontrolu](./media/role-assignments-portal/access-control-check-access.png)

1. V seznamu **Najít** vyberte typ objektu zabezpečení, pro který chcete ověřit přístup.

1. Do vyhledávacího pole zadejte řetězec, ve kterém budou v adresáři hledána zobrazovaná jména, e-mailové adresy nebo identifikátory objektů.

    ![Zaškrtněte seznam pro výběr přístupu.](./media/role-assignments-portal/check-access-select.png)

1. Kliknutím na objekt zabezpečení otevřete podokno **přiřazení** .

    ![podokno přiřazení](./media/role-assignments-portal/check-access-assignments.png)

    V tomto podokně můžete zobrazit role přiřazené k vybranému objektu zabezpečení a oboru. Pokud jsou v tomto oboru k dispozici nějaká přiřazení odepřít nebo zděděná do tohoto oboru, budou uvedena.

### <a name="view-all-role-assignments-at-a-scope"></a>Zobrazení všech přiřazení rolí v oboru

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete zobrazit přístup.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

   ![Řízení přístupu – karta přiřazení rolí](./media/role-assignments-portal/access-control-role-assignments.png)

   Na kartě přiřazení rolí vidíte, kdo má v tomto oboru přístup. Všimněte si, že některé role mají obor nastavený na **Tento prostředek**, zatímco jiné mají obor **(Zděděný)** z jiného oboru. Přístup je buď přiřazen konkrétně tomuto prostředku, nebo zděděn z přiřazení do nadřazeného oboru.

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Aby bylo možné udělit přístup, přiřadíte ve službě RBAC roli uživateli, skupině, instančnímu objektu nebo spravované identitě roli. Pomocí těchto kroků udělíte přístup v různých oborech.

### <a name="assign-a-role-at-a-scope"></a>Přiřazení role v oboru

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete udělit přístup.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se objektu zabezpečení přiřadí role ve vybraném oboru.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřazení uživatele jako správce předplatného

Pokud chcete, aby uživatel měl správce předplatného Azure, přiřaďte mu roli [vlastníka](built-in-roles.md#owner) v oboru předplatného. Role Vlastník poskytuje uživateli úplný přístup ke všem prostředkům v předplatném, včetně práva delegovat přístup na ostatní. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na předplatné, ke kterému chcete udělit přístup.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. Kliknutím na **Přidat** > **Přidat přiřazení role** otevřete podokno přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

1. Kliknutím na **Uložit** roli přiřaďte.

   Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="remove-role-assignments"></a>Odebrat přiřazení role

V RBAC se přístup odebírá odebrání přiřazení role. Pomocí těchto kroků odeberte přístup.

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

* [Kurz: Udělte uživateli přístup k prostředkům Azure pomocí RBAC a Azure Portal](quickstart-assign-role-user-portal.md)
* [Kurz: Udělení přístupu uživatelům k prostředkům Azure pomocí RBAC a Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Řešení potíží s RBAC pro prostředky Azure](troubleshooting.md)
* [Uspořádání prostředků se skupinami pro správu Azure](../governance/management-groups/overview.md)
