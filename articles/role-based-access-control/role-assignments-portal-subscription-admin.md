---
title: Přiřazení uživatele jako správce předplatného Azure – RBAC pro Azure
description: Naučte se, jak nastavit uživatele jako správce předplatného Azure pomocí Azure Portal a řízení přístupu na základě role Azure (RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556839"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Přiřazení role správce předplatného Azure uživateli

Pokud chcete, aby uživatel měl správce předplatného Azure, přiřaďte mu roli [vlastníka](built-in-roles.md#owner) v oboru předplatného. Role vlastníka přidělí uživateli úplný přístup ke všem prostředkům v rámci předplatného, včetně oprávnění pro udělení přístupu jiným uživatelům. Tento postup je stejný jako u jakéhokoli jiného přiřazení role.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Krok 1: otevření předplatného

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole v horní části vyhledejte předplatná.

    ![Azure Portal Hledat skupinu prostředků](./media/shared/sub-portal-search.png)

1. Klikněte na předplatné, které chcete použít.

    Níže je uveden příklad předplatného.

    ![Přehled skupiny prostředků](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Krok 2: otevřete podokno přidat přiřazení role.

**Řízení přístupu (IAM)** je stránka, kterou obvykle používáte k přiřazování rolí pro udělení přístupu k prostředkům Azure. Označuje se taky jako Správa identit a přístupu (IAM) a zobrazuje se v několika umístěních v Azure Portal.

1. Klikněte na **Řízení přístupu (IAM)**.

    Níže vidíte příklad stránky řízení přístupu (IAM) pro předplatné.

    ![Stránka řízení přístupu (IAM) pro skupinu prostředků](./media/shared/sub-access-control.png)

1. Kliknutím na kartu **přiřazení rolí** zobrazíte přiřazení rolí v tomto oboru.

1. Klikněte na **Přidat**  >  **přiřazení role přidat**.
   Pokud nemáte oprávnění k přiřazování rolí, bude možnost přidat přiřazení role zakázaná.

   ![Přidat nabídku přiřazení role](./media/shared/add-role-assignment-menu.png)

    Otevře se podokno Přidat přiřazení role.

   ![Přidat podokno přiřazení role](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Krok 3: vyberte roli vlastníka.

Role [vlastníka](built-in-roles.md#owner) uděluje úplný přístup ke správě všech prostředků, včetně možnosti přiřadit role v Azure RBAC. Měli byste mít maximálně 3 vlastníky předplatného, aby bylo možné omezit riziko narušení napadeného vlastníka.

- V seznamu **role** vyberte roli **vlastníka** .

   ![Vybrat roli vlastníka v podokně Přidat přiřazení role](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Krok 4: vyberte, kdo potřebuje přístup.

1. V seznamu **přiřadit přístup k** vyberte možnost **uživatel, skupina nebo instanční objekt**.

1. V části **Vybrat** vyhledejte uživatele zadáním řetězce nebo posouváním v seznamu.

   ![Vybrat uživatele v přiřazení role přidat](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Jakmile zjistíte uživatele, klikněte na něj a vyberte ho.

## <a name="step-5-assign-role"></a>Krok 5: přiřazení role

1. Chcete-li přiřadit roli, klikněte na tlačítko **Uložit**.

   Po chvíli se uživateli přiřadí role ve vybraném oboru.

1. Na kartě **přiřazení rolí** ověřte, že se v seznamu zobrazuje přiřazení role.

    ![Přidání přiřazení role se uložilo.](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md)
- [Vypsání přiřazení rolí Azure pomocí Azure Portal](role-assignments-list-portal.md)
- [Uspořádání prostředků s využitím skupin pro správu Azure](../governance/management-groups/overview.md)
