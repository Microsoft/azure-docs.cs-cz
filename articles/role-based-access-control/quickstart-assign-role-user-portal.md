---
title: 'Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a portálu Azure '
description: V tomto kurzu se dozvíte, jak udělit uživateli přístup k prostředkům Azure pomocí řízení přístupu na základě rolí (RBAC) na webu Azure Portal.
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
ms.openlocfilehash: 6281c9a1818a3b46ac4f7a62f8ae76668db56887
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138089"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Kurz: Udělení přístupu uživateli k prostředkům Azure pomocí RBAC a portálu Azure

[Řízení přístupu na základě rolí (RBAC)](overview.md) je způsob, jakým spravujete přístup k prostředkům Azure. V tomto kurzu udělíte uživateli přístup k vytváření a správě virtuálních počítačů ve skupině prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu uživateli v oboru skupiny prostředků
> * Odebrání přístupu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. V navigačním seznamu klikněte na **Skupiny prostředků**.

1. Klepnutím na tlačítko **Přidat** otevřete okno **Skupiny prostředků.**

   ![Přidání nové skupiny prostředků](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Do **pole Název skupiny prostředků**zadejte **rbac-resource-group**.

1. Vyberte předplatné a umístění.

1. Kliknutím na **Vytvořit** skupinu prostředků vytvořte.

1. Kliknutím na **Aktualizovat** aktualizujte seznam skupin prostředků.

   Nová skupina prostředků se zobrazí v seznamu skupin prostředků.

   ![Seznam skupin prostředků](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

1. V seznamu **skupin prostředků**klepněte na novou skupinu prostředků **rbac-resource-group.**

1. Klikněte na **Řízení přístupu (IAM)**.

1. Kliknutím na kartu **Přiřazení rolí** zobrazte aktuální seznam přiřazení rolí.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/quickstart-assign-role-user-portal/access-control.png)

1. Kliknutím na **Přidat** > **přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, bude možnost Přidat přiřazení role zakázána.

   ![Nabídka Přidat](./media/role-assignments-portal/add-menu.png)

   ![Podokno přiřazení role](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte sebe nebo jiného uživatele.

1. Kliknutím na **Uložit** vytvořte přiřazení role.

   Po několika okamžicích je uživateli přiřazena role přispěvatele virtuálního počítače v oboru skupiny prostředků rbac-resource-group.

   ![Přiřazení role Přispěvatel virtuálních počítačů](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

1. V seznamu přiřazení rolí přidejte vedle uživatele zaškrtávací políčko s rolí Přispěvatel virtuálního počítače.

1. Klikněte na **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Ve zprávě odebrat přiřazení role, která se zobrazí, klepněte na tlačítko **Ano**.

## <a name="clean-up"></a>Vyčištění

1. V navigačním seznamu klikněte na **Skupiny prostředků**.

1. Kliknutím na **skupinu prostředků rbac** otevřete skupinu prostředků.

1. Kliknutím na **Odstranit skupinu prostředků** odstraňte skupinu prostředků.

   ![Odstranění skupiny prostředků](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. V části **Opravdu chcete odstranit** okno, zadejte název skupiny prostředků: **rbac-resource-group**.

1. Chcete-li skupinu prostředků odstranit, klepněte na tlačítko **Odstranit.**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu uživateli k prostředkům Azure pomocí RBAC a Azure PowerShellu](tutorial-role-assignments-user-powershell.md)
