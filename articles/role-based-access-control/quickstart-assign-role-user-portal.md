---
title: Kurz – poskytnutí uživatel přístup k prostředkům Azure pomocí RBAC a webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak udělit přístup uživatelů k prostředkům Azure pomocí řízení přístupu na základě role (RBAC) na webu Azure Portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: 9352130eeeb68a87947450cb5616aa59e8e99ae9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807367"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a webu Azure portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům Azure. V tomto kurzu je udělit uživatelům přístup k vytváření a správa virtuálních počítačů ve skupině prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro uživatele v oboru skupiny prostředků
> * Odebrání přístupu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. V navigačním seznamu klikněte na tlačítko **skupiny prostředků**.

1. Klikněte na tlačítko **přidat** otevřít **skupiny prostředků** okno.

   ![Přidání nové skupiny prostředků](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Pro **název skupiny prostředků**, zadejte **rbac-resource-group**.

1. Vyberte předplatné a umístění.

1. Klikněte na tlačítko **vytvořit** a vytvořte skupinu prostředků.

1. Klikněte na tlačítko **aktualizovat** aktualizovat seznam skupin prostředků.

   Nová skupina prostředků se zobrazí v seznamu skupin prostředků.

   ![Seznam skupin prostředků](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

1. V seznamu **skupiny prostředků**, klikněte na nový **rbac-resource-group** skupinu prostředků.

1. Klikněte na tlačítko **řízení přístupu (IAM)**.

1. Klikněte na tlačítko **přiřazení rolí** kartu pro zobrazení aktuálního seznamu přiřazení rolí.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/quickstart-assign-role-user-portal/access-control.png)

1. Klikněte na tlačítko **přidat** > **přidat přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, možnost Přidat přiřazení role se deaktivuje.

   ![Přidat nabídku](./media/role-assignments-portal/add-menu.png)

   ![Přidání podokna přiřazení role](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte sebe nebo jiného uživatele.

1. Klikněte na tlačítko **Uložit** k vytvoření tohoto přiřazení role.

   Po chvíli se má uživatel přiřazenou roli Přispěvatel virtuálních počítačů v oboru skupiny prostředků rbac-resource-group.

   ![Přiřazení role Přispěvatel virtuálních počítačů](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

1. V seznamu přiřazení rolí přidejte zaškrtnutí vedle uživatele k roli Přispěvatel virtuálních počítačů.

1. Klikněte na tlačítko **odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Ve zprávě přiřazení role odebrat, která se zobrazí, klikněte na tlačítko **Ano**.

## <a name="clean-up"></a>Vyčištění

1. V navigačním seznamu klikněte na tlačítko **skupiny prostředků**.

1. Klikněte na tlačítko **rbac-resource-group** otevřete skupinu prostředků.

1. Klikněte na tlačítko **odstranit skupinu prostředků** odstranit skupinu prostředků.

   ![Odstranění skupiny prostředků](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Na **Opravdu chcete odstranit** okně zadejte název skupiny prostředků: **rbac-resource-group**.

1. Klikněte na tlačítko **odstranit** odstranit skupinu prostředků.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a Azure Powershellu](tutorial-role-assignments-user-powershell.md)

