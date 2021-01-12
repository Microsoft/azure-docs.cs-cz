---
title: Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal – Azure RBAC
description: Přečtěte si, jak udělit přístup k prostředkům Azure pro uživatele, skupiny, instanční objekty nebo spravované identity pomocí Azure Portal a řízení přístupu na základě role Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117416"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Přidání nebo odebrání přiřazení rolí Azure pomocí portálu Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Tento článek popisuje, jak přiřadit role pomocí Azure Portal.

Pokud potřebujete přiřadit role správce v Azure Active Directory, přečtěte si téma [zobrazení a přiřazení rolí správce v Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Přidat přiřazení role

Když ve službě Azure RBAC udělíte přístup k prostředku Azure, přidáte přiřazení role. Pomocí těchto kroků přiřaďte roli. Podrobný přehled kroků najdete v tématu [Postup přidání přiřazení role](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Krok 1: určení potřebného oboru

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Další informace o rozsahu najdete v tématu [pochopení oboru](scope-overview.md).

![Úrovně rozsahu pro Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole v horní části vyhledejte obor, kterému chcete udělit přístup. Můžete například vyhledat skupiny pro **správu**, **předplatná**, **skupiny prostředků** nebo konkrétní prostředek.

    ![Azure Portal Hledat skupinu prostředků](./media/shared/rg-portal-search.png)

1. Klikněte na konkrétní prostředek pro daný obor.

    Níže je uveden příklad skupiny prostředků.

    ![Přehled skupiny prostředků](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Krok 2: otevřete podokno přidat přiřazení role.

**Řízení přístupu (IAM)** je stránka, kterou obvykle používáte k přiřazování rolí pro udělení přístupu k prostředkům Azure. Označuje se taky jako Správa identit a přístupu (IAM) a zobrazuje se v několika umístěních v Azure Portal.

1. Klikněte na **Řízení přístupu (IAM)**.

    Níže vidíte příklad stránky řízení přístupu (IAM) pro skupinu prostředků.

    ![Stránka řízení přístupu (IAM) pro skupinu prostředků](./media/shared/rg-access-control.png)

1. Kliknutím na kartu **přiřazení rolí** zobrazíte přiřazení rolí v tomto oboru.

1. Klikněte na **Přidat**  >  **přiřazení role přidat**.
   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku přiřazení role](./media/shared/add-role-assignment-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Přidat podokno přiřazení role](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>Krok 3: vyberte příslušnou roli.

1. V seznamu **role** vyhledejte nebo Najděte roli, kterou chcete přiřadit.

    K určení příslušné role vám pomůžete najetí myší na ikonu informace a zobrazit tak popis role. Další informace najdete v článku [předdefinované role Azure](built-in-roles.md) .

   ![Vybrat roli v přiřazení přidat roli](./media/role-assignments-portal/add-role-assignment-role.png)

1. Kliknutím vyberte roli.

### <a name="step-4-select-who-needs-access"></a>Krok 4: vyberte, kdo potřebuje přístup.

1. V seznamu **přiřadit přístup k** vyberte typ objektu zabezpečení, ke kterému chcete přiřadit přístup.

    | Typ | Description |
    | --- | --- |
    | **Uživatel, skupina nebo instanční objekt** | Pokud chcete přiřadit roli uživateli, skupině nebo objektu služby (aplikace), vyberte tento typ. |
    | **Spravovaná identita přiřazená uživatelem** | Pokud chcete přiřadit roli k [spravované identitě přiřazené uživateli](../active-directory/managed-identities-azure-resources/overview.md), vyberte tento typ. |
    | *Spravovaná identita přiřazená systémem* | Pokud chcete přiřadit roli k [spravované identitě přiřazené systémem](../active-directory/managed-identities-azure-resources/overview.md), vyberte instanci služby Azure, kde se nachází spravovaná identita. |

   ![Vybrat typ objektu zabezpečení v přiřazení přidat roli](./media/role-assignments-portal/add-role-assignment-type.png)

1. Pokud jste vybrali spravovanou identitu přiřazenou uživatelem nebo spravovanou identitu přiřazenou systémem, vyberte **předplatné** , ve kterém se nachází spravovaná identita.

1. V části **Vybrat** vyhledejte objekt zabezpečení zadáním řetězce nebo posouváním v seznamu.

   ![Vybrat uživatele v přiřazení role přidat](./media/role-assignments-portal/add-role-assignment-user.png)

1. Jakmile zjistíte objekt zabezpečení, klikněte na něj a vyberte ho.

### <a name="step-5-assign-role"></a>Krok 5: přiřazení role

1. Chcete-li přiřadit roli, klikněte na tlačítko **Uložit**.

   Po chvíli se objektu zabezpečení přiřadí role ve vybraném oboru.

1. Na kartě **přiřazení rolí** ověřte, že se v seznamu zobrazuje přiřazení role.

    ![Přidání přiřazení role se uložilo.](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Odebrání přiřazení role

Pokud v Azure RBAC odeberete přístup z prostředku Azure, odeberete přiřazení role. Pomocí těchto kroků odeberte přiřazení role.

1. Otevřete **řízení přístupu (IAM)** v oboru, jako je například skupina pro správu, předplatné, skupina prostředků nebo prostředek, kde chcete odebrat přístup.

1. Kliknutím na kartu **přiřazení rolí** zobrazíte všechna přiřazení rolí v tomto oboru.

1. V seznamu přiřazení rolí přidejte značku zaškrtnutí vedle objektu zabezpečení s přiřazením role, které chcete odebrat.

   ![Přiřazení role vybrané k odebrání](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/role-assignments-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klikněte na **Ano**.

    Pokud se zobrazí zpráva, že se zděděná přiřazení rolí nedají odebrat, pokoušíte se odebrat přiřazení role v podřízeném oboru. Měli byste otevřít řízení přístupu (IAM) v oboru, kde byla role přiřazena, a akci opakovat. Rychlý způsob, jak otevřít řízení přístupu (IAM) ve správném oboru, je podívat se na sloupec **Rozsah** a kliknout na odkaz vedle **(Zděděno)**.

   ![Odebrat zprávu přiřazení role pro zděděná přiřazení rolí](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení role správce předplatného Azure uživateli](role-assignments-portal-subscription-admin.md)
- [Přidání přiřazení role pro spravovanou identitu](role-assignments-portal-managed-identity.md)
- [Řešení potíží s Azure RBAC](troubleshooting.md)
