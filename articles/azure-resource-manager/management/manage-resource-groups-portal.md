---
title: Správa skupin prostředků – portál Azure
description: Pomocí portálu Azure můžete spravovat skupiny prostředků prostřednictvím Azure Resource Manageru. Ukazuje, jak vytvořit, vypsat a odstranit skupiny prostředků.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 8087baf7595bfd2497f97fbff4822b356cd1b146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274564"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Správa skupin prostředků Azure Resource Managerpomocí portálu Azure

Přečtěte si, jak pomocí Azure [Resource](https://portal.azure.com) [Manageru](overview.md) používat ke správě skupin prostředků Azure. Správa prostředků Azure najdete v [tématu Správa prostředků Azure pomocí portálu Azure](manage-resources-portal.md).

Další články o správě skupin zdrojů:

- [Správa skupin prostředků Azure pomocí azure cli](manage-resources-cli.md)
- [Správa skupin prostředků Azure pomocí Azure PowerShellu](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně přidejte prostředky, které sdílejí stejný životní cyklus, do stejné skupiny prostředků, abyste je mohli snadno nasadit, aktualizovat a odstranit jako skupinu.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určujete, kde jsou tato metadata uložena.

## <a name="create-resource-groups"></a>Vytvořit skupiny prostředků

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vybrat **skupiny zdrojů**

    ![přidat skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Vyberte **Přidat**.
4. Zadejte následující hodnoty:

   - **Předplatné**: Vyberte své předplatné Azure. 
   - **Skupina prostředků**: Zadejte nový název skupiny prostředků. 
   - **Oblast**: Vyberte umístění Azure, například **centrální USA**.

     ![vytvořit skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Vybrat **recenzi + vytvořit**
6. Vyberte **Vytvořit**. Vytvoření skupiny prostředků trvá několik sekund.
7. V horní nabídce vyberte **Aktualizovat,** chcete-li aktualizovat seznam skupin prostředků, a pak vyberte nově vytvořenou skupinu prostředků, kterou chcete otevřít. Nebo vyberte **Oznámení**(ikona zvonku) shora a pak vyberte **Přejít do skupiny prostředků,** chcete-li otevřít nově vytvořenou skupinu prostředků.

    ![přejít do skupiny prostředků](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Seznam skupin prostředků

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Chcete-li vypsat skupiny prostředků, vyberte **skupiny zdrojů.**

    ![procházení skupin prostředků](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Chcete-li přizpůsobit informace zobrazené pro skupiny prostředků, vyberte **možnost Upravit sloupce**. Následující snímek obrazovky zobrazuje sloupce přidání, které byste mohli přidat na displej:

## <a name="open-resource-groups"></a>Otevření skupin prostředků

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **Skupiny prostředků**.
3. Vyberte skupinu prostředků, kterou chcete otevřít.

## <a name="delete-resource-groups"></a>Odstranění skupin prostředků

1. Otevřete skupinu prostředků, kterou chcete odstranit.  Viz [Otevřít skupiny prostředků](#open-resource-groups).
2. Vyberte **Odstranit skupinu prostředků**.

    ![odstranění skupiny prostředků Azure](./media/manage-resource-groups-portal/delete-group.png)

Další informace o tom, jak Azure Resource Manager objednávky odstranění prostředků, najdete v [tématu Odstranění skupiny prostředků Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření šablony Správce prostředků můžete použít portál Azure k nasazení prostředků Azure. Vytvoření šablony najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](../templates/quickstart-create-templates-use-the-portal.md). Informace o nasazení šablony pomocí portálu najdete v tématu [Nasazení prostředků pomocí šablon Správce prostředků a portálu Azure](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přechod na jinou skupinu prostředků nebo předplatné

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Uzamčení skupin prostředků

Uzamčení zabrání ostatním uživatelům ve vaší organizaci v náhodném odstranění nebo úpravě kritických prostředků, jako je předplatné Azure, skupina prostředků nebo prostředek. 

1. Otevřete skupinu prostředků, kterou chcete odstranit.  Viz [Otevřít skupiny prostředků](#open-resource-groups).
2. V levém podokně vyberte **Zamknout**.
3. Pokud chcete přidat zámek do skupiny prostředků, vyberte **Přidat**.
4. Zadejte **název zámku**, **typ zámku**a **poznámky**. Typy zámků zahrnují **jen pro čtení**a **Delete**.

    ![uzamčení skupiny prostředků Azure](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Další informace naleznete v tématu [Uzamčení prostředků, abyste zabránili neočekávaným změnám](lock-resources.md).

## <a name="tag-resource-groups"></a>Označit skupiny prostředků

Značky můžete použít na skupiny prostředků a prostředky, které logicky uspořádají prostředky. Další informace najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Export skupin prostředků do šablon

Informace o exportu šablon naleznete v [tématu Export s jedním a více prostředky do šablony – portál](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/overview.md) je způsob, jakým spravujete přístup k prostředkům v Azure. Další informace najdete v tématu [Správa přístupu pomocí RBAC a webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky

- Pokud se chcete naučit Správce prostředků Azure, přečtěte si [přehled správce prostředků Azure](overview.md).
- Syntaxe syntaxe šablony Správce prostředků najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](../templates/template-syntax.md).
- Chcete-li se dozvědět, jak vyvíjet šablony, podívejte se na [podrobné výukové programy](/azure/azure-resource-manager/).
- Pokud chcete zobrazit schémata šablon Azure Resource Manager, přečtěte si [odkaz na šablonu](/azure/templates/).