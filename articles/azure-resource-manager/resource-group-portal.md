---
title: Pomocí webu Azure portal ke správě prostředků Azure | Dokumentace Microsoftu
description: Ke správě prostředků pomocí Azure Resource Manageru a webu Azure portal. Ukazuje, jak pracovat s řídicími panely pro monitorování prostředků.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: 7398e01a46b5d296f26905e2063acdb98383f567
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38600357"
---
# <a name="manage-azure-resources-through-portal"></a>Správa prostředků Azure prostřednictvím portálu

Tento článek popisuje, jak používat [webu Azure portal](https://portal.azure.com) s [Azure Resource Manageru](resource-group-overview.md) ke správě vašich prostředků Azure. Další informace o nasazení prostředků prostřednictvím portálu najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](resource-group-template-deploy-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>Správa skupin prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně platí přidejte prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, takže můžete snadno nasadit, aktualizovat a odstranit jako skupina. 

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

1. Pokud chcete zobrazit všechny skupiny prostředků ve vašem předplatném, vyberte **skupiny prostředků**.
   
    ![Procházet skupiny prostředků](./media/resource-group-portal/browse-groups.png)
2. Chcete-li vytvořit prázdné skupiny prostředků, vyberte **přidat**.
   
    ![Přidat skupinu prostředků](./media/resource-group-portal/add-resource-group.png)
3. Zadejte název a umístění pro novou skupinu prostředků. Vyberte **Vytvořit**.
   
    ![Vytvořte skupinu prostředků](./media/resource-group-portal/create-empty-group.png)
4. Budete muset vybrat **aktualizovat** zobrazíte skupinu nedávno vytvořeného prostředku.
   
    ![Aktualizace skupiny prostředků](./media/resource-group-portal/refresh-resource-groups.png)
5. Chcete-li přizpůsobit informace zobrazené pro vaší skupiny prostředků, vyberte **sloupce**.
   
    ![Upravit sloupce](./media/resource-group-portal/select-columns.png)
6. Vyberte sloupce, které chcete přidat a pak vyberte **aktualizace**.
   
    ![Přidání sloupců](./media/resource-group-portal/add-columns.png)
7. Další informace o nasazování prostředků do nové skupiny prostředků najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](resource-group-template-deploy-portal.md).
8. Pro rychlý přístup do skupiny prostředků můžete připnout skupinu prostředků na řídicí panel.
   
    ![Skupina prostředků PIN kódu](./media/resource-group-portal/pin-group.png)
9. Na řídicím panelu zobrazí skupina prostředků a její prostředky. Můžete vybrat skupiny prostředků nebo některé z jejích prostředků pro navigaci k položce.
   
    ![Skupina prostředků PIN kódu](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Označení prostředků
Můžete provést značky u prostředků a skupin prostředků logicky tak uspořádat vaše prostředky. Informace o práci se značkami, naleznete v tématu [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Sledování prostředků
Při výběru prostředku na portálu zobrazí výchozích grafů a tabulek pro monitorování tohoto typu prostředku.

1. Vyberte prostředek a Všimněte si, že **monitorování** oddílu. Její součástí jsou grafy, které jsou relevantní pro typ prostředku. Následující obrázek ukazuje výchozí údaje pro účet úložiště.
   
    ![zobrazení monitorování](./media/resource-group-portal/show-monitoring.png)
2. Oddíl můžete připnout na řídicí panel tak, že vyberete tři tečky (...) výše v části. Můžete také přizpůsobit velikost oddílu nebo ho úplně odeberte. Následující obrázek ukazuje, jak připnout, upravit nebo odebrat oddíl procesoru a paměti.
   
    ![část kódu PIN](./media/resource-group-portal/pin-cpu-section.png)
3. Po Připnutí na řídicí panel v části, se zobrazí souhrn na řídicím panelu. A výběrem okamžitě přejde na další podrobnosti o datech.
   
    ![Zobrazit řídicí panel](./media/resource-group-portal/view-startboard.png)
4. Zcela přizpůsobit data monitorování prostřednictvím portálu, přejděte na výchozí řídicí panel a vyberte **nový řídicí panel**.
   
    ![řídicí panel](./media/resource-group-portal/dashboard.png)
5. Pojmenujte nový řídicí panel a přetáhněte dlaždice na řídicím panelu. Dlaždice se filtrují podle různých možností.
   
    ![řídicí panel](./media/resource-group-portal/create-dashboard.png)
   
     Další informace o práci s řídicími panely, najdete v článku [vytváření a sdílení řídicích panelů na webu Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Správa prostředků
Při zobrazení prostředku na portálu, se zobrazí možnosti pro správu příslušného prostředku.

![Správa prostředků](./media/resource-group-portal/manage-resources.png)

Z těchto možností můžete provádět operace, jako je spuštění a zastavení virtuálního počítače nebo opětovná konfigurace vlastností virtuálního počítače.

## <a name="move-resources"></a>Přesunutí prostředků
Pokud potřebujete prostředky přesunout do jiné skupiny prostředků nebo jiného předplatného, přečtěte si téma [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

## <a name="lock-resources"></a>Uzamčení prostředků
Předplatné, skupinu prostředků nebo prostředek Neumožnit dalším uživatelům ve vaší organizaci omylem odstranit nebo upravit důležitých prostředků můžete uzamknout. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Umožňuje zobrazit vaše předplatné a náklady
Zobrazí se informace o vaše předplatné a souhrnné náklady pro všechny vaše prostředky. Vyberte **předplatná** a předplatné, které chcete zobrazit. Může mít jenom jedno předplatné a vyberte.

![předplatné](./media/resource-group-portal/select-subscription.png)

Zobrazí se pracovní tempo.

![pracovní tempo](./media/resource-group-portal/burn-rate.png)

A rozdělení nákladů podle typu prostředku.

![náklady na prostředek](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Export šablony
Po nastavení skupiny prostředků, můžete zobrazit šablony Resource Manageru pro skupinu prostředků. Export šablony nabízí dvě výhody:

1. Vzhledem k tomu, že šablona obsahuje kompletní infrastrukturu můžete snadno automatizovat budoucí nasazení řešení.
2. Můžete seznámit se syntaxí šablony pohledem na zápisu JSON (JavaScript Object), který představuje vaše řešení.

Podrobné pokyny najdete v tématu [šablony exportovat Azure Resource Manageru z existujících prostředků](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Odstranit skupinu prostředků nebo prostředky
Odstranění skupiny prostředků odstraní všechny prostředky, které jsou v něm obsaženy. Můžete také odstranit jednotlivé prostředky v rámci skupiny prostředků. Buďte opatrní při odstranění skupiny prostředků. Této skupiny prostředků může obsahovat prostředky, které závisí na prostředky v jiných skupinách prostředků.

![Odstranit skupinu](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Další postup
* Zobrazení protokolů aktivit najdete [Audit operací pomocí Resource Manageru](resource-group-audit.md).
* Chcete-li zobrazit podrobnosti o nasazení, najdete v článku [zobrazení operací nasazení](resource-manager-deployment-operations.md).
* Nasazení prostředků prostřednictvím portálu najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a webu Azure portal](resource-group-template-deploy-portal.md).
* Pokud chcete spravovat přístup k prostředkům, naleznete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

