---
title: Rychlý start – Udělení přístupu pro uživatele pomocí RBAC a webu Azure Portal | Microsoft Docs
description: Použijte řízení přístupu na základě role (RBAC) a udělte uživateli oprávnění prostřednictvím přiřazení role na webu Azure Portal.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 74ecca671409b6e163bc0db29d66167d240b645c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092518"
---
# <a name="quickstart-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Rychlý start: Udělení přístupu pro uživatele pomocí RBAC a webu Azure Portal

Řízení přístupu na základě role (RBAC) je způsob správy přístupu k prostředkům v Azure. V tomto rychlém startu udělíte uživateli přístup pro vytváření a správu virtuálních počítačů ve skupině prostředků.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Vyberte **Přidat** a otevřete okno **Skupina prostředků**.

   ![Přidání nové skupiny prostředků](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Pro **Název skupiny prostředků** zadejte **rbac-quickstart-resource-group**.

1. Vyberte předplatné a umístění.

1. Vyberte **Vytvořit** a vytvořte skupinu prostředků.

1. Vyberte **Aktualizovat** a aktualizujte seznam skupin prostředků.

   Nová skupina prostředků se zobrazí v seznamu skupin prostředků.

   ![Seznam skupin prostředků](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udělení přístupu

V RBAC se přístup uděluje vytvořením přiřazení role.

1. V seznamu **Skupiny prostředků** vyberte novou skupinu prostředků **rbac-quickstart-resource-group**.

1. Vyberte **Řízení přístupu (IAM)** a zobrazte aktuální seznam přiřazení rolí.

   ![Okno Řízení přístupu (IAM) pro skupinu prostředků](./media/quickstart-assign-role-user-portal/access-control.png)

1. Zvolte **Přidat** a otevřete podokno **Přidat oprávnění**.

   Pokud nemáte oprávnění přiřazovat role, možnost **Přidat** se nezobrazí.

   ![Podokno Přidat oprávnění](./media/quickstart-assign-role-user-portal/add-permissions.png)

1. V rozevíracím seznamu **Role** vyberte **Přispěvatel virtuálních počítačů**.

1. V seznamu **Vybrat** vyberte sebe nebo jiného uživatele.

1. Zvolte **Uložit** a vytvořte přiřazení role.

   Za několik okamžiků se uživateli přiřadí role Přispěvatel virtuálních počítačů v oboru skupiny prostředků rbac-quickstart-resource-group.

   ![Přiřazení role Přispěvatel virtuálních počítačů](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Odebrání přístupu

V RBAC se přístup odebírá odebrání přiřazení role.

1. V seznamu přiřazení rolí zaškrtněte políčko vedle uživatele s rolí Přispěvatel virtuálních počítačů.

1. Zvolte **Odebrat**.

   ![Zpráva Odebrání přiřazení role](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Ve zprávě Odebrání přiřazení role, která se zobrazí, zvolte **Ano**.

## <a name="clean-up"></a>Vyčištění

1. V navigačním seznamu zvolte **Skupiny prostředků**.

1. Vyberte **rbac-quickstart-resource-group** a otevřete skupinu prostředků.

1. Vyberte **Odstranit skupinu prostředků** a skupinu prostředků odstraňte.

   ![Odstranění skupiny prostředků](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. V okně **Opravdu chcete odstranit** zadejte název skupiny prostředků: **rbac-quickstart-resource-group**.

1. Vyberte **Odstranit** a skupinu prostředků odstraňte.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu pro uživatele pomocí RBAC a PowerShellu](tutorial-role-assignments-user-powershell.md)

