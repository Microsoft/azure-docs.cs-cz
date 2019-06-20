---
title: Správa skupin v Azure Resource Manageru pomocí webu Azure portal | Dokumentace Microsoftu
description: Pomocí webu Azure portal ke správě skupin Azure Resource Manageru.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 9d87a395d35cdaefb1764a516e96165a251484ab
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206576"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Správa skupin prostředků Azure Resource Manageru pomocí webu Azure portal

Další informace o použití [webu Azure portal](https://portal.azure.com) s [Azure Resource Manageru](resource-group-overview.md) ke správě skupin prostředků Azure. Správa prostředků Azure, najdete v části [Správa prostředků Azure pomocí webu Azure portal](./manage-resources-portal.md).

Další články o správě skupin prostředků:

- [Správa skupin prostředků Azure pomocí Azure CLI](./manage-resources-cli.md)
- [Správa skupin prostředků Azure pomocí Azure Powershellu](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Co je skupina prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně platí přidejte prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, takže můžete snadno nasadit, aktualizovat a odstranit jako skupina.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků, určujete, kde se tato metadata ukládají.

## <a name="create-resource-groups"></a>Vytvoření skupiny prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **skupiny prostředků**

    ![Přidat skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Vyberte **Přidat**.
4. Zadejte následující hodnoty:

   - **Předplatné**: Vyberte své předplatné Azure. 
   - **Skupina prostředků**: Zadejte název nové skupiny prostředků. 
   - **Oblast**: Vyberte umístění Azure, jako je například **USA (střed)** .

     ![Vytvořte skupinu prostředků](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Vyberte **zkontrolovat a vytvořit**
6. Vyberte **Vytvořit**. Trvá několik sekund, vytvořte skupinu prostředků.
7. Vyberte **aktualizovat** z hlavní nabídky aktualizovat seznam skupin prostředků, a potom vyberte nově vytvořený prostředek skupiny a otevřete ho. Nebo vyberte **oznámení**(ikona zvonku) z horní části a pak vyberte **přejít ke skupině prostředků** otevřete nově vytvořený prostředek skupiny

    ![Přejděte do skupiny prostředků](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Seznam skupin prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Seznam skupin prostředků, vyberte **skupiny prostředků**

    ![Procházet skupiny prostředků](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Chcete-li přizpůsobit informace zobrazené pro skupiny prostředků, vyberte **upravit sloupce**. Následující snímek obrazovky ukazuje přidání sloupce, které že můžete přidat na obrazovce:

## <a name="open-resource-groups"></a>Otevřít prostředek skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Skupiny prostředků**.
3. Vyberte skupinu prostředků, kterou chcete otevřít.

## <a name="delete-resource-groups"></a>Odstranění skupiny prostředků

1. Otevřete skupinu prostředků, kterou chcete odstranit.  Zobrazit [skupiny prostředků otevřete](#open-resource-groups).
2. Vyberte **Odstranit skupinu prostředků**.

    ![Odstranit skupinu prostředků azure](./media/manage-resource-groups-portal/delete-group.png)

Další informace o tom, jak Azure Resource Manageru orders odstranění prostředků najdete v tématu [odstranění skupiny prostředků Azure Resource Manageru](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Nasazení prostředků do skupiny prostředků

Po vytvoření šablony Resource Manageru, můžete na webu Azure portal k nasazení prostředků Azure. Vytvoření šablony najdete v části [rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Nasazení šablony pomocí portálu, najdete v části [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Přesunout do jiné skupiny prostředků nebo předplatného

Prostředky ve skupině můžete přesunout do jiné skupiny prostředků. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Skupiny prostředků zámku

Zamknutí zabrání ostatním uživatelům ve vaší organizaci omylem odstranit nebo upravit důležité prostředky, jako je předplatné Azure, skupinu prostředků nebo prostředek. 

1. Otevřete skupinu prostředků, kterou chcete odstranit.  Zobrazit [skupiny prostředků otevřete](#open-resource-groups).
2. V levém podokně vyberte **uzamkne**.
3. Chcete-li přidat zámek proti do skupiny prostředků, vyberte **přidat**.
4. Zadejte **název zámku**, **zamknout typ**, a **poznámky**. Zámek typy zahrnují **jen pro čtení**, a **odstranit**.

    ![Skupina prostředků azure zámku](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Další informace najdete v tématu [zamknutí prostředků, aby se zabránilo neočekávaným změnám](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Značka skupiny prostředků

Můžete provést značky u prostředků a skupin prostředků logicky tak uspořádat vaše prostředky. Informace najdete v tématu [použití značek k uspořádání prostředků Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Export skupiny prostředků do šablon

Informace o exportování šablony najdete v tématu [export jednoho a víc prostředků šablony - Portal](export-template-portal.md).

### <a name="fix-export-issues"></a>Oprava problémů s exportem

Ne všechny typy prostředků podporují funkci exportu šablony. Zobrazí jenom export problémy při exportu ze skupiny prostředků, ne z historie nasazení. Pokud vaše poslední nasazení přesně reprezentuje aktuální stav skupiny prostředků, měli byste šablonu exportovat z historie nasazení, ne ze skupiny zdrojů. Exportujte jenom ze skupiny prostředků po provedení změny do skupiny prostředků, které nejsou definovány v jediné šabloně.

K vyřešení problémů s exportem ručně přidáním chybějících prostředků zpět do šablony. Chybová zpráva obsahuje typy prostředků, které nelze exportovat. Vyhledejte tyto typy prostředků v [referenčních informacích k šablonám](/azure/templates/). Pokud například chcete ručně přidat bránu virtuální sítě, přečtěte si [referenční informace k šablonám o prostředku Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). Referenční dokumentace šablony vám ve formátu JSON, bude příslušný materiál přidán do šablony.

Po získání formátu JSON pro prostředek, potřebujete získat hodnoty prostředků. Hodnoty prostředku můžete zobrazit pomocí operace GET v rozhraní REST API pro typ prostředku. Třeba, abyste získali hodnoty pro příslušnou bránu virtuální sítě, přečtěte si část [brány virtuální sítě – získání](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Správa přístupu ke skupinám prostředků

[Řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) je způsob správy přístupu k prostředkům v Azure. Další informace najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup

- Další Azure Resource Manageru najdete v tématu [přehled Azure Resource Manageru](./resource-group-overview.md).
- Seznamte se se syntaxí šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](./resource-group-authoring-templates.md).
- Zjistěte, jak vyvíjet šablony, najdete v článku [podrobné kurzy](/azure/azure-resource-manager/).
- Schémata šablon Azure Resource Manageru najdete v tématu [referenčními informacemi k šablonám](/azure/templates/).