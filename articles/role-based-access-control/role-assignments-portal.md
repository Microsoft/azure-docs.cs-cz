---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal – Azure RBAC
description: Přečtěte si, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure Portal a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4e5c13c635091988f299d31c67795916e709d51a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597622"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Tento článek popisuje, jak přiřadit role pomocí Azure Portal.

Pokud potřebujete přiřadit role správce v Azure Active Directory, přečtěte si téma [zobrazení a přiřazení rolí správce v Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat nebo odebrat přiřazení rolí, je nutné mít následující:

- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="access-control-iam"></a>Řízení přístupu (IAM)

**Řízení přístupu (IAM)** je stránka, kterou obvykle používáte k přiřazování rolí pro udělení přístupu k prostředkům Azure. Označuje se taky jako Správa identit a přístupu a zobrazuje se v několika umístěních v Azure Portal. Níže vidíte příklad stránky řízení přístupu (IAM) pro předplatné.

![Stránka řízení přístupu (IAM) pro předplatné](./media/role-assignments-portal/access-control-subscription.png)

Aby byla na stránce řízení přístupu (IAM) nejúčinnější, pomůže vám přiřadit roli pomocí těchto kroků.

1. Určete, kdo potřebuje přístup. Roli můžete přiřadit uživateli, skupině, objektu služby nebo spravované identitě.

1. Najděte příslušnou roli. Oprávnění se seskupují do rolí. Můžete vybrat ze seznamu několika [předdefinovaných rolí Azure](built-in-roles.md) nebo můžete použít vlastní role.

1. Identifikujte potřebný rozsah. Azure poskytuje čtyři úrovně rozsahu: [skupina pro správu](../governance/management-groups/overview.md), předplatné, [Skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups)a prostředek. Další informace o rozsahu najdete v tématu [pochopení oboru](scope-overview.md).

1. Provedením kroků v jednom z následujících částí přiřadíte roli.

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Když ve službě Azure RBAC udělíte přístup k prostředku Azure, přidáte přiřazení role. Pomocí těchto kroků přiřaďte roli.

1. V Azure Portal klikněte na **všechny služby** a pak vyberte obor, pro který chcete udělit přístup. Můžete například vybrat **skupiny pro správu**, **předplatná**, **skupiny prostředků**nebo prostředek.

1. Klikněte na konkrétní prostředek pro daný obor.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **přiřazení rolí** zobrazíte přiřazení rolí v tomto oboru.

    ![Karta pro řízení přístupu (IAM) a přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

1. Klikněte na **Přidat**  >  **přiřazení role přidat**.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku přiřazení role](./media/shared/add-role-assignment-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Přidat podokno přiřazení role](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte uživatele, skupinu, instanční objekt nebo spravovanou identitu. Pokud se objekt zabezpečení v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy, e-mailové adresy a identifikátory objektů.

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se objektu zabezpečení přiřadí role ve vybraném oboru.

    ![Přidání přiřazení role se uložilo.](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Přiřazení role správce předplatného uživateli

Pokud chcete, aby uživatel měl správce předplatného Azure, přiřaďte mu roli [vlastníka](built-in-roles.md#owner) v oboru předplatného. Role vlastníka přidělí uživateli úplný přístup ke všem prostředkům v rámci předplatného, včetně oprávnění pro udělení přístupu jiným uživatelům. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

1. V Azure Portal klikněte na **všechny služby** a pak na **předplatná**.

1. Klikněte na předplatné, ke kterému chcete udělit přístup.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Kliknutím na kartu **přiřazení rolí** zobrazíte přiřazení rolí pro toto předplatné.

    ![Karta pro řízení přístupu (IAM) a přiřazení rolí](./media/role-assignments-portal/role-assignments.png)

1. Klikněte na **Přidat**  >  **přiřazení role přidat**.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidání nabídky přiřazení role pro předplatné](./media/shared/add-role-assignment-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Přidat podokno přiřazení role pro předplatné](./media/role-assignments-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte roli **Vlastník**.

1. V seznamu **Vybrat** vyberte uživatele. Pokud se uživatel v seznamu nezobrazí, pomocí pole **Vybrat** můžete v adresáři prohledat zobrazované názvy a e-mailové adresy.

1. Kliknutím na **Uložit** roli přiřaďte.

   Za chvíli se uživateli přiřadí role Vlastník v oboru předplatného.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Přidání přiřazení role pro spravovanou identitu (Preview)

Přiřazení rolí pro spravovanou identitu můžete přidat pomocí stránky **řízení přístupu (IAM)** , jak je popsáno výše v tomto článku. Když použijete stránku řízení přístupu (IAM), začnete s oborem a pak vyberete spravovanou identitu a roli. Tato část popisuje alternativní způsob přidání přiřazení rolí pro spravovanou identitu. Pomocí těchto kroků zahájíte spravovanou identitu a pak vyberete obor a roli.

> [!IMPORTANT]
> Přidání přiřazení role pro spravovanou identitu pomocí těchto alternativních kroků je aktuálně ve verzi Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

Pomocí těchto kroků přiřaďte roli spravované identitě přiřazené systémem, a to od spravované identity.

1. V Azure Portal otevřete spravovanou identitu přiřazenou systémem.

1. V nabídce vlevo klikněte na možnost **Identita**.

    ![Spravovaná identita přiřazená systémem](./media/shared/identity-system-assigned.png)

1. V části **oprávnění**klikněte na **přiřazení rolí Azure**.

    Pokud jsou role už přiřazené k vybrané spravované identitě přiřazené systémem, zobrazí se seznam přiřazení rolí. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou systémem](./media/shared/role-assignments-system-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

1. Klikněte na **Přidat přiřazení role (Preview)**.

1. Pomocí rozevíracích seznamů vyberte sadu prostředků, na které se přiřazení role vztahuje, jako je například **předplatné**, **Skupina prostředků**nebo prostředek.

    Pokud nemáte oprávnění k zápisu do přiřazení role pro vybraný obor, zobrazí se vložená zpráva. 

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

   ![Přidat podokno přiřazení role pro spravovanou identitu přiřazenou systémem](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se spravované identitě přiřadí role ve vybraném oboru.

### <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

Pomocí těchto kroků přiřadíte roli spravované identitě přiřazené uživateli, a to od spravované identity.

1. V Azure Portal otevřete uživatelem přiřazenou spravovanou identitu.

1. V nabídce vlevo klikněte na **přiřazení rolí Azure**.

    Pokud jsou už role přiřazené k vybrané spravované identitě přiřazené uživateli, zobrazí se seznam přiřazení rolí. Tento seznam obsahuje všechna přiřazení rolí, ke kterým máte oprávnění číst.

    ![Přiřazení rolí pro spravovanou identitu přiřazenou uživatelem](./media/shared/role-assignments-user-assigned.png)

1. Chcete-li změnit předplatné, klikněte na seznam **předplatných** .

1. Klikněte na **Přidat přiřazení role (Preview)**.

1. Pomocí rozevíracích seznamů vyberte sadu prostředků, na které se přiřazení role vztahuje, jako je například **předplatné**, **Skupina prostředků**nebo prostředek.

    Pokud nemáte oprávnění k zápisu do přiřazení role pro vybraný obor, zobrazí se vložená zpráva. 

1. V rozevíracím seznamu **Role** vyberte roli, například **Přispěvatel virtuálních počítačů**.

   ![Přidat podokno přiřazení role pro uživatelem přiřazenou spravovanou identitu](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Kliknutím na **Uložit** roli přiřaďte.

   Po chvíli se spravované identitě přiřadí role ve vybraném oboru.

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Pokud v Azure RBAC odeberete přístup z prostředku Azure, odeberete přiřazení role. Pomocí těchto kroků odeberte přiřazení role.

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete odebrat přístup.

1. Kliknutím na kartu **Přiřazení rolí** zobrazte všechna přiřazení rolí pro toto předplatné.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Přiřazení role vybrané k odebrání](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

    Pokud se zobrazí zpráva, že se zděděná přiřazení rolí nedají odebrat, pokoušíte se odebrat přiřazení role v podřízeném oboru. Měli byste otevřít řízení přístupu (IAM) v oboru, kde byla role přiřazena, a akci opakovat. Rychlý způsob, jak otevřít řízení přístupu (IAM) ve správném oboru, je podívat se na sloupec **Rozsah** a kliknout na odkaz vedle **(Zděděno)**.

   ![Odebrat zprávu přiřazení role pro zděděná přiřazení rolí](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další kroky

- [Vypsání přiřazení rolí Azure pomocí Azure Portal](role-assignments-list-portal.md)
- [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí Azure Portal](quickstart-assign-role-user-portal.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
- [Uspořádání prostředků s využitím skupin pro správu Azure](../governance/management-groups/overview.md)
