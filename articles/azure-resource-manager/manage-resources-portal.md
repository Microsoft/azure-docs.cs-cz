---
title: Správa prostředků Azure pomocí webu Azure portal | Dokumentace Microsoftu
description: Ke správě prostředků pomocí webu Azure portal a Azure Resource Manageru.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550639"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Správa prostředků Azure pomocí webu Azure portal

Další informace o použití [webu Azure portal](https://portal.azure.com) s [Azure Resource Manageru](resource-group-overview.md) ke správě vašich prostředků Azure. Skupiny prostředků, přečtěte si téma [skupin prostředků Azure spravovat pomocí webu Azure portal](./manage-resource-groups-portal.md).

Další články o správě prostředků:

- [Správa prostředků Azure pomocí Azure CLI](./manage-resources-cli.md)
- [Správa prostředků Azure pomocí Azure Powershellu](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření šablony Resource Manageru, můžete na webu Azure portal k nasazení prostředků Azure. Vytvoření šablony najdete v části [rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Nasazení šablony pomocí portálu, najdete v části [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Otevřít prostředky

Prostředky Azure jsou uspořádané podle služeb Azure a skupiny prostředků. Následující postup ukazuje, jak otevřít účet úložiště s názvem **mystorage0207**. Virtuální počítač se nachází ve skupině prostředků s názvem **mystorage0207rg**.

Chcete-li otevřít prostředek podle typu služby:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém podokně vyberte službu Azure. V takovém případě **účty úložiště**.  Pokud se má služba uvedená, vyberte **všechny služby**a pak vyberte typ služby.

    ![Otevřete prostředek azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Vyberte prostředek, který chcete otevřít.

    ![Otevřete prostředek azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Účet úložiště bude vypadat jako:

    ![Otevřete prostředek azure na portálu](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Chcete-li otevřít skupinu prostředků podle prostředků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V levém podokně vyberte **skupiny prostředků** do seznamu prostředků v rámci skupiny.
3. Vyberte prostředek, který chcete otevřít. 

## <a name="manage-resources"></a>Správa prostředků

Při zobrazení prostředku na portálu, se zobrazí možnosti pro správu příslušného prostředku.

![Správa prostředků Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Snímek obrazovky ukazuje možnosti správy pro virtuální počítač Azure. Můžete provádět operace, jako je spuštění, restartování a zastavuje se virtuální počítač.

## <a name="delete-resources"></a>Odstranění prostředků

1. Otevřete prostředek na portálu. Pokyny najdete v tématu [otevřete prostředky](#open-resources).
2. Vyberte **Odstranit**. Následující snímek obrazovky ukazuje možnosti správy pro virtuální počítač.

    ![odstranění prostředků azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Zadejte název prostředku k potvrzení odstranění a pak vyberte **odstranit**.

Další informace o tom, jak Azure Resource Manageru orders odstranění prostředků najdete v tématu [odstranění skupiny prostředků Azure Resource Manageru](./resource-group-delete.md).

## <a name="move-resources"></a>Přesunutí prostředků

1. Otevřete prostředek na portálu. Pokyny najdete v tématu [otevřete prostředky](#open-resources).
2. Vyberte **přesunout**. Následující snímek obrazovky ukazuje možnosti správy účtu úložiště.

    ![Přesun prostředků azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Vyberte **přesunout do jiné skupiny prostředků** nebo **Moeve do jiného předplatného** podle svých potřeb.

Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

## <a name="lock-resources"></a>Uzamčení prostředků

Zamknutí zabrání ostatním uživatelům ve vaší organizaci omylem odstranit nebo upravit důležité prostředky, jako je předplatné Azure, skupinu prostředků nebo prostředek. 

1. Otevřete prostředek na portálu. Pokyny najdete v tématu [otevřete prostředky](#open-resources).
2. Vyberte **uzamkne**. Následující snímek obrazovky ukazuje možnosti správy účtu úložiště.

    ![zámku azure prostředek](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Vyberte **přidat**a pak zadejte vlastnosti zámku.

Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Označení prostředků

Označování pomáhá logicky uspořádání skupinu prostředků a prostředky. 

1. Otevřete prostředek na portálu. Pokyny najdete v tématu [otevřete prostředky](#open-resources).
2. Vyberte **Značky**. Následující snímek obrazovky ukazuje možnosti správy účtu úložiště.

    ![značky azure resource](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Zadejte vlastnosti značky a pak vyberte **Uložit**.

Informace najdete v tématu [použití značek k uspořádání prostředků Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Sledování prostředků

Při otevření prostředku na portálu zobrazí výchozích grafů a tabulek pro monitorování tohoto typu prostředku. Na následujícím snímku obrazovky se zobrazí v grafech pro virtuální počítač:

![monitorování azure resource](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Můžete vybrat ikonu připínáčku v pravém horním rohu grafů a připnout graf na řídicí panel. Další informace o práci s řídicími panely, najdete v článku [vytváření a sdílení řídicích panelů na webu Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Správa přístupu k prostředkům

[Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) je způsob správy přístupu k prostředkům v Azure. Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup

- Další Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](./resource-group-overview.md).
- Seznamte se se syntaxí šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](./resource-group-authoring-templates.md).
- Zjistěte, jak vyvíjet šablony, najdete v článku [podrobné kurzy](/azure/azure-resource-manager/).
- Schémata šablon Azure Resource Manageru najdete v tématu [referenčními informacemi k šablonám](/azure/templates/).