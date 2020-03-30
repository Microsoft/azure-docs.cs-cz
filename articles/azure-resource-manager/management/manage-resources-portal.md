---
title: Správa prostředků – portál Azure
description: Ke správě prostředků použijte portál Azure a Správce prostředků Azure. Ukazuje, jak nasadit a odstranit prostředky.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248343"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Správa prostředků Azure pomocí portálu Azure

Přečtěte si, jak pomocí Azure [Resource](https://portal.azure.com) [Manageru](overview.md) používat ke správě prostředků Azure. Správa skupin prostředků najdete v [tématu Správa skupin prostředků Azure pomocí portálu Azure](manage-resource-groups-portal.md).

Další články o správě zdrojů:

- [Správa prostředků Azure pomocí Azure CLI](manage-resources-cli.md)
- [Správa prostředků Azure pomocí Azure PowerShellu](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření šablony Správce prostředků můžete použít portál Azure k nasazení prostředků Azure. Vytvoření šablony najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](../templates/quickstart-create-templates-use-the-portal.md). Informace o nasazení šablony pomocí portálu najdete v tématu [Nasazení prostředků pomocí šablon Správce prostředků a portálu Azure](../templates/deploy-portal.md).

## <a name="open-resources"></a>Otevřít zdroje

Prostředky Azure jsou uspořádané podle služeb Azure a podle skupin prostředků. Následující postupy ukazují, jak otevřít účet úložiště s názvem **mystorage0207**. Virtuální počítač je umístěn ve skupině prostředků s názvem **mystorage0207rg**.

Otevření prostředku podle typu služby:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levém podokně vyberte službu Azure. V tomto případě **účty úložiště**.  Pokud službu v seznamu nevidíte, vyberte **Všechny služby**a pak vyberte typ služby.

    ![otevření prostředků Azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Vyberte zdroj, který chcete otevřít.

    ![otevření prostředků Azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Účet úložiště vypadá takto:

    ![otevření prostředků Azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Otevření prostředku podle skupiny prostředků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levém podokně vyberte **skupiny prostředků,** chcete-li uvést zdroj v rámci skupiny.
3. Vyberte zdroj, který chcete otevřít. 

## <a name="manage-resources"></a>Správa prostředků

Při zobrazení prostředku na portálu se zobrazí možnosti správy daného prostředku.

![správa prostředků Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Snímek obrazovky zobrazuje možnosti správy pro virtuální počítač Azure. Můžete provádět operace, jako je například spuštění, restartování a zastavení virtuálního počítače.

## <a name="delete-resources"></a>Odstranění prostředků

1. Otevřete prostředek na portálu. Postup naleznete v tématu [Otevření prostředků](#open-resources).
2. Vyberte **Odstranit**. Následující snímek obrazovky ukazuje možnosti správy pro virtuální počítač.

    ![odstranění prostředku Azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Zadejte název prostředku, který odstranění potvrdí, a pak vyberte **Odstranit**.

Další informace o tom, jak Azure Resource Manager objednávky odstranění prostředků, najdete v [tématu Odstranění skupiny prostředků Azure Resource Manager](delete-resource-group.md).

## <a name="move-resources"></a>Přesunutí prostředků

1. Otevřete prostředek na portálu. Postup naleznete v tématu [Otevření prostředků](#open-resources).
2. Vyberte **Přesunout**. Následující snímek obrazovky ukazuje možnosti správy pro účet úložiště.

    ![přesunutí prostředků Azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Vyberte **Přesunout do jiné skupiny prostředků** nebo **Přesunout do jiného předplatného** v závislosti na vašich potřebách.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Uzamčení zabrání ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků, jako je předplatné Azure, skupina prostředků nebo prostředek. 

1. Otevřete prostředek na portálu. Postup naleznete v tématu [Otevření prostředků](#open-resources).
2. Vyberte **možnost Zámky**. Následující snímek obrazovky ukazuje možnosti správy pro účet úložiště.

    ![uzamčení prostředku Azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Vyberte **Přidat**a zadejte vlastnosti zámku.

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označování pomáhá logicky organizovat skupinu prostředků a prostředky. 

1. Otevřete prostředek na portálu. Postup naleznete v tématu [Otevření prostředků](#open-resources).
2. Vyberte **Značky**. Následující snímek obrazovky ukazuje možnosti správy pro účet úložiště.

    ![označit prostředek Azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Zadejte vlastnosti tagu a pak vyberte **Uložit**.

Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md#portal).

## <a name="monitor-resources"></a>Sledování prostředků

Při otevření prostředku portál zobrazí výchozí grafy a tabulky pro sledování tohoto typu prostředku. Následující snímek obrazovky ukazuje grafy pro virtuální počítač:

![monitorování prostředků Azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Chcete-li graf připnout k řídicímu panelu, můžete vybrat ikonu špendlíku v pravém horním rohu grafů. Informace o práci s řídicími panely najdete [v tématu Vytváření a sdílení řídicích panelů na webu Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) je způsob, jakým spravujete přístup k prostředkům v Azure. Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit Správce prostředků Azure, přečtěte si [přehled správce prostředků Azure](overview.md).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](../templates/template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).
