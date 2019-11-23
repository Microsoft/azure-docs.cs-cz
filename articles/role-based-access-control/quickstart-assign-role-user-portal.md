---
title: Tutorial - Grant a user access to Azure resources using RBAC and the Azure portal
description: In this tutorial, learn how to grant a user access to Azure resources using role-based access control (RBAC) in the Azure portal.
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
ms.openlocfilehash: f4dd3995df2a068824c4aa6bccca5606d250a165
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419661"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Tutorial: Grant a user access to Azure resources using RBAC and the Azure portal

[Role-based access control (RBAC)](overview.md) is the way that you manage access to Azure resources. In this tutorial, you grant a user access to create and manage virtual machines in a resource group.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Grant access for a user at a resource group scope
> * Odebrání přístupu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. In the navigation list, click **Resource groups**.

1. Click **Add** to open the **Resource group** blade.

   ![Přidání nové skupiny prostředků](./media/quickstart-assign-role-user-portal/resource-group.png)

1. For **Resource group name**, enter **rbac-resource-group**.

1. Vyberte předplatné a umístění.

1. Click **Create** to create the resource group.

1. Click **Refresh** to refresh the list of resource groups.

   Nová skupina prostředků se zobrazí v seznamu skupin prostředků.

   ![Seznam skupin prostředků](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

1. In the list of **Resource groups**, click the new **rbac-resource-group** resource group.

1. Klikněte na **Řízení přístupu (IAM)** .

1. Click the **Role assignments** tab to see the current list of role assignments.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/quickstart-assign-role-user-portal/access-control.png)

1. Click **Add** > **Add role assignment** to open the Add role assignment pane.

   If you don't have permissions to assign roles, the Add role assignment option will be disabled.

   ![Add menu](./media/role-assignments-portal/add-menu.png)

   ![Add role assignment pane](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. V rozevíracím seznamu **Role** vyberte **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte sebe nebo jiného uživatele.

1. Click **Save** to create the role assignment.

   After a few moments, the user is assigned the Virtual Machine Contributor role at the rbac-resource-group resource group scope.

   ![Přiřazení role Přispěvatel virtuálních počítačů](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

1. In the list of role assignments, add a checkmark next to the user with the Virtual Machine Contributor role.

1. Click **Remove**.

   ![Zpráva Odebrání přiřazení role](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. In the remove role assignment message that appears, click **Yes**.

## <a name="clean-up"></a>Vyčištění

1. In the navigation list, click **Resource groups**.

1. Click **rbac-resource-group** to open the resource group.

1. Click **Delete resource group** to delete the resource group.

   ![Odstranění skupiny prostředků](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. On the **Are you sure you want to delete** blade, type the resource group name: **rbac-resource-group**.

1. Click **Delete** to delete the resource group.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Tutorial: Grant a user access to Azure resources using RBAC and Azure PowerShell](tutorial-role-assignments-user-powershell.md)
