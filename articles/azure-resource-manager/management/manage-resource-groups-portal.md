---
title: Správa skupin prostředků – Azure Portal
description: Pomocí Azure Portal můžete spravovat skupiny prostředků přes Azure Resource Manager. Ukazuje, jak vytvořit, vypsat a odstranit skupiny prostředků.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093686"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Správa Azure Resource Manager skupin prostředků pomocí Azure Portal

Naučte se používat [Azure Portal](https://portal.azure.com) s [Azure Resource Manager](overview.md) ke správě skupin prostředků Azure. Informace o správě prostředků Azure najdete v tématu [Správa prostředků Azure pomocí Azure Portal](manage-resources-portal.md).

Další články o správě skupin prostředků:

- [Správa skupin prostředků Azure pomocí Azure CLI](manage-resources-cli.md)
- [Správa skupin prostředků Azure pomocí Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně můžete přidat prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, abyste je mohli snadno nasadit, aktualizovat a odstranit jako skupinu.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.


## <a name="create-resource-groups"></a>Vytvoření skupin prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Výběr **skupin prostředků**

    ![Přidat skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Vyberte **Přidat**.
4. Zadejte tyto hodnoty:

   - **Předplatné**: Vyberte své předplatné Azure. 
   - **Skupina prostředků**: zadejte nový název skupiny prostředků. 
   - **Oblast**: vyberte umístění Azure, například **střed USA**.

     ![vytvořit skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Vybrat **kontrolu + vytvořit**
6. Vyberte **Vytvořit**. Vytvoření skupiny prostředků trvá několik sekund.
7. V horní nabídce vyberte **aktualizovat** , aby se aktualizoval seznam skupin prostředků, a pak vyberte nově vytvořenou skupinu prostředků a otevřete ji. Nebo vyberte v horní části **oznámení**(ikona zvonku) a pak vyberte **Přejít do skupiny prostředků** a otevřete nově vytvořenou skupinu prostředků.

    ![Přejít na skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Seznam skupin prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Pokud chcete zobrazit seznam skupin prostředků, vyberte **skupiny prostředků** .

    ![Procházet skupiny prostředků](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Chcete-li upravit zobrazené informace pro skupiny prostředků, vyberte možnost **Upravit sloupce**. Na následujícím snímku obrazovky vidíte přidané sloupce, které byste mohli přidat do zobrazení:

## <a name="open-resource-groups"></a>Otevření skupin prostředků

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **Skupiny prostředků**.
3. Vyberte skupinu prostředků, kterou chcete otevřít.

## <a name="delete-resource-groups"></a>Odstranění skupin prostředků

1. Otevřete skupinu prostředků, kterou chcete odstranit.  Viz [otevřít skupiny prostředků](#open-resource-groups).
2. Vyberte **Odstranit skupinu prostředků**.

    ![Odstranit skupinu prostředků Azure](./media/manage-resource-groups-portal/delete-group.png)

Další informace o tom, jak Azure Resource Manager objednává odstranění prostředků, najdete v tématu [Azure Resource Manager odstranění skupiny prostředků](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření šablony Správce prostředků můžete použít Azure Portal k nasazení prostředků Azure. Informace o vytváření šablon naleznete v tématu [rychlý Start: vytvoření a nasazení šablon Azure Resource Manager pomocí Azure Portal](../templates/quickstart-create-templates-use-the-portal.md). Informace o nasazení šablony pomocí portálu najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure Portal](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přesunout do jiné skupiny prostředků nebo předplatného

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Uzamčení skupin prostředků

Uzamykání brání jiným uživatelům ve vaší organizaci v neúmyslném odstranění nebo úpravě důležitých prostředků, jako je například předplatné Azure, skupina prostředků nebo prostředek. 

1. Otevřete skupinu prostředků, kterou chcete zamknout.  Viz [otevřít skupiny prostředků](#open-resource-groups).
2. V levém podokně vyberte **zámky**.
3. Pokud chcete přidat zámek ke skupině prostředků, vyberte **Přidat**.
4. Zadejte **název zámku**, **Typ zámku** a **poznámky**. Typy zámků zahrnují **jen pro čtení** a **odstranění**.

    ![Uzamknout skupinu prostředků Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Další informace najdete v tématu [uzamčení prostředků, aby se zabránilo neočekávaným změnám](lock-resources.md).

## <a name="tag-resource-groups"></a>Označení skupin prostředků

Můžete použít značky pro skupiny prostředků a prostředky k logické organizaci vašich assetů. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Export skupin prostředků do šablon

Informace o exportu šablon najdete v tématu [Export jednoho a více prostředků do šablony-portál](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md) je způsob, jakým můžete spravovat přístup k prostředkům v Azure. Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

- Informace o Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](overview.md).
- Další informace o syntaxi šablon Správce prostředků naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](../templates/template-syntax.md).
- Další informace o vývoji šablon najdete v [podrobných kurzech](../index.yml).
- Chcete-li zobrazit schémata šablon Azure Resource Manager, přečtěte si téma [reference šablony](/azure/templates/).
