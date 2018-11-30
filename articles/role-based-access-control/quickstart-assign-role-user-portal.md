---
title: Kurz – poskytnutí přístupu pro uživatele pomocí RBAC a webu Azure portal | Dokumentace Microsoftu
description: Použijte řízení přístupu na základě role (RBAC) a udělte uživateli oprávnění prostřednictvím přiřazení role na webu Azure Portal.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634468"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Kurz: Udělení přístupu pro uživatele pomocí RBAC a webu Azure portal

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob správy přístupu k prostředkům v Azure. V tomto kurzu je udělit uživatelům přístup k vytváření a správa virtuálních počítačů ve skupině prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro uživatele v oboru skupiny prostředků
> * Odebrání přístupu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Vyberte **Přidat** a otevřete okno **Skupina prostředků**.

   ![Přidání nové skupiny prostředků](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Pro **název skupiny prostředků**, zadejte **rbac-resource-group**.

1. Vyberte předplatné a umístění.

1. Vyberte **Vytvořit** a vytvořte skupinu prostředků.

1. Vyberte **Aktualizovat** a aktualizujte seznam skupin prostředků.

   Nová skupina prostředků se zobrazí v seznamu skupin prostředků.

   ![Seznam skupin prostředků](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

1. V seznamu **skupiny prostředků**, zvolte Nový **rbac-resource-group** skupinu prostředků.

1. Zvolte **Řízení přístupu (IAM)**.

1. Zvolte **přiřazení rolí** kartu pro zobrazení aktuálního seznamu přiřazení rolí.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/quickstart-assign-role-user-portal/access-control.png)

1. Zvolte **přidat přiřazení role** otevřete podokno Přidat přiřazení role.

   Pokud nemáte oprávnění k přiřazování rolí, možnost Přidat přiřazení role se deaktivuje.

   ![Přidání podokna přiřazení role](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte sebe nebo jiného uživatele.

1. Zvolte **Uložit** a vytvořte přiřazení role.

   Po chvíli se má uživatel přiřazenou roli Přispěvatel virtuálních počítačů v oboru skupiny prostředků rbac-resource-group.

   ![Přiřazení role Přispěvatel virtuálních počítačů](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

1. V seznamu přiřazení rolí přidejte zaškrtnutí vedle uživatele k roli Přispěvatel virtuálních počítačů.

1. Zvolte **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Ve zprávě Odebrání přiřazení role, která se zobrazí, zvolte **Ano**.

## <a name="clean-up"></a>Vyčištění

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Zvolte **rbac-resource-group** otevřete skupinu prostředků.

1. Vyberte **Odstranit skupinu prostředků** a skupinu prostředků odstraňte.

   ![Odstranění skupiny prostředků](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Na **Opravdu chcete odstranit** okně zadejte název skupiny prostředků: **rbac-resource-group**.

1. Vyberte **Odstranit** a skupinu prostředků odstraňte.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu pro uživatele pomocí RBAC a PowerShellu](tutorial-role-assignments-user-powershell.md)

