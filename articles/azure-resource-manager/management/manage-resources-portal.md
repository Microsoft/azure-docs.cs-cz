---
title: Správa prostředků – Azure Portal
description: Ke správě prostředků použijte Azure Portal a Azure Resource Manager. Ukazuje, jak nasadit a odstranit prostředky.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 12d704b0a3d92aa0585fb120f969000def282396
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092201"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Správa prostředků Azure pomocí Azure Portal

Naučte se používat [Azure Portal](https://portal.azure.com) s [Azure Resource Manager](overview.md) ke správě prostředků Azure. Informace o správě skupin prostředků najdete v tématu [Správa skupin prostředků Azure pomocí Azure Portal](manage-resource-groups-portal.md).

Další články o správě prostředků:

- [Správa prostředků Azure pomocí Azure CLI](manage-resources-cli.md)
- [Správa prostředků Azure pomocí Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření šablony Správce prostředků můžete použít Azure Portal k nasazení prostředků Azure. Informace o vytváření šablon naleznete v tématu [rychlý Start: vytvoření a nasazení šablon Azure Resource Manager pomocí Azure Portal](../templates/quickstart-create-templates-use-the-portal.md). Informace o nasazení šablony pomocí portálu najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure Portal](../templates/deploy-portal.md).

## <a name="open-resources"></a>Otevřené prostředky

Prostředky Azure se uspořádávají podle služeb Azure a skupin prostředků. Následující postupy ukazují, jak otevřít účet úložiště s názvem **mystorage0207**. Virtuální počítač se nachází ve skupině prostředků s názvem **mystorage0207rg**.

Postup otevření prostředku podle typu služby:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V levém podokně vyberte službu Azure. V tomto případě **účty úložiště**.  Pokud uvedenou službu nevidíte, vyberte **všechny služby** a potom vyberte typ služby.

    ![otevřít prostředek Azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Vyberte prostředek, který chcete otevřít.

    ![Snímek obrazovky, který zvýrazní vybraný prostředek.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Účet úložiště vypadá takto:

    ![Snímek obrazovky, který ukazuje, co vypadá na účtu úložiště.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Postup otevření prostředku podle skupiny prostředků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V levém podokně vyberte **skupiny prostředků** a seznam prostředků v rámci skupiny.
3. Vyberte prostředek, který chcete otevřít. 

## <a name="manage-resources"></a>Správa prostředků

Při zobrazení prostředku na portálu se zobrazí možnosti správy daného konkrétního prostředku.

![Správa prostředků Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Snímek obrazovky ukazuje možnosti správy pro virtuální počítač Azure. Můžete provádět operace, jako je spuštění, restartování a zastavení virtuálního počítače.

## <a name="delete-resources"></a>Odstranění prostředků

1. Otevřete prostředek na portálu. Postup najdete v tématu [Open Resources](#open-resources).
2. Vyberte **Odstranit**. Následující snímek obrazovky ukazuje možnosti správy pro virtuální počítač.

    ![odstranit prostředek Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Zadáním názvu prostředku potvrďte odstranění a pak vyberte **Odstranit**.

Další informace o tom, jak Azure Resource Manager objednává odstranění prostředků, najdete v tématu [Azure Resource Manager odstranění skupiny prostředků](delete-resource-group.md).

## <a name="move-resources"></a>Přesunutí prostředků

1. Otevřete prostředek na portálu. Postup najdete v tématu [Open Resources](#open-resources).
2. Vyberte **přesunout**. Následující snímek obrazovky ukazuje možnosti správy pro účet úložiště.

    ![přesunout prostředek Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Vyberte **přesunout do jiné skupiny prostředků** nebo **Přejít na jiné předplatné** v závislosti na vašich potřebách.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků, jako je například předplatné Azure, skupina prostředků nebo prostředek. 

1. Otevřete prostředek na portálu. Postup najdete v tématu [Open Resources](#open-resources).
2. Vyberte **zámky**. Následující snímek obrazovky ukazuje možnosti správy pro účet úložiště.

    ![Uzamknout prostředek Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Vyberte **Přidat** a pak zadejte vlastnosti zámku.

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označení pomáhá organizovat skupinu prostředků a prostředky logicky. 

1. Otevřete prostředek na portálu. Postup najdete v tématu [Open Resources](#open-resources).
2. Vyberte **Značky**. Následující snímek obrazovky ukazuje možnosti správy pro účet úložiště.

    ![označení prostředku Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Zadejte vlastnosti značky a potom vyberte **Uložit**.

Informace najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md#portal).

## <a name="monitor-resources"></a>Sledování prostředků

Když otevřete prostředek, portál prezentuje výchozí grafy a tabulky pro monitorování tohoto typu prostředku. Následující snímek obrazovky ukazuje grafy pro virtuální počítač:

![monitorovat prostředek Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Můžete vybrat ikonu připnutí v pravém horním rohu grafů a připnout graf na řídicí panel. Další informace o práci s řídicími panely najdete [v tématu vytváření a sdílení řídicích panelů v Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) je způsob, jakým můžete spravovat přístup k prostředkům v Azure. Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

- Informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](overview.md).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](../templates/template-syntax.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](../index.yml).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).
