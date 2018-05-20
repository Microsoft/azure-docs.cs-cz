---
title: Použití portálu Azure ke správě prostředků Azure | Microsoft Docs
description: Ke správě prostředků pomocí portálu Azure a správě prostředků Azure. Ukazuje, jak pracovat s řídicí panely a sledujte prostředky.
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
ms.topic: article
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: 2c76e56d9b30ef11fe4f3b7863a1a1357fca291c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-azure-resources-through-portal"></a>Spravovat prostředky prostřednictvím portálu Azure

Tento článek ukazuje, jak používat [portál Azure](https://portal.azure.com) s [Azure Resource Manager](resource-group-overview.md) ke správě prostředků Azure. Další informace o nasazení prostředků prostřednictvím portálu najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](resource-group-template-deploy-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>Správa skupin prostředků

Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků může zahrnovat všechny prostředky pro řešení nebo pouze ty prostředky, které chcete spravovat jako skupinu. Na základě toho, co je pro vaši organizaci nejvhodnější, rozhodnete, jakým způsobem se mají prostředky přidělovat do skupin prostředků. Obecně platí přidejte prostředky, které sdílejí stejný životní cyklus do stejné skupiny prostředků, můžete snadno nasadit, aktualizovat a odstranit jejich jako skupina. 

Skupina prostředků ukládá metadata o prostředcích. Při zadávání umístění skupiny prostředků tedy určujete, kde se tato metadata ukládají. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

1. Pokud chcete zobrazit všechny skupiny prostředků v rámci vašeho předplatného, vyberte **skupiny prostředků**.
   
    ![Procházet skupiny prostředků](./media/resource-group-portal/browse-groups.png)
2. Vytvořit skupinu prostředků prázdný, vyberte **přidat**.
   
    ![Přidat skupinu prostředků](./media/resource-group-portal/add-resource-group.png)
3. Zadejte název a umístění pro novou skupinu prostředků. Vyberte **Vytvořit**.
   
    ![Vytvořte skupinu prostředků](./media/resource-group-portal/create-empty-group.png)
4. Je nutné vybrat **aktualizovat** zobrazíte skupině nedávno vytvořených prostředků.
   
    ![Aktualizace skupiny prostředků](./media/resource-group-portal/refresh-resource-groups.png)
5. Chcete-li přizpůsobit informace zobrazené pro vaší skupiny prostředků, vyberte **sloupce**.
   
    ![přizpůsobení sloupců](./media/resource-group-portal/select-columns.png)
6. Vyberte sloupce, které chcete přidat a potom vyberte **aktualizace**.
   
    ![Přidání sloupců](./media/resource-group-portal/add-columns.png)
7. Další informace o nasazování prostředků do nové skupiny prostředků najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](resource-group-template-deploy-portal.md).
8. Pro rychlý přístup do skupiny prostředků budete moct připnout na řídicí panel skupině prostředků.
   
    ![Skupina prostředků PIN kódu](./media/resource-group-portal/pin-group.png)
9. Řídicí panel zobrazuje skupina prostředků a její prostředky. Můžete vybrat buď skupiny prostředků, nebo kterýkoli z jeho prostředků a přejděte k položce.
   
    ![Skupina prostředků PIN kódu](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Značka prostředky
Značky můžete použít pro skupiny prostředků a prostředky logicky uspořádat vaše prostředky. Informace o práci s značky najdete v tématu [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Sledování prostředků
Když vyberete prostředek, uvede portálu výchozí grafů a tabulek pro monitorování tohoto typu prostředku.

1. Vyberte prostředek a upozornění **monitorování** části. Obsahuje grafy, které jsou relevantní pro typ prostředku. Následující obrázek znázorňuje výchozí sledování dat pro účet úložiště.
   
    ![zobrazení monitorování](./media/resource-group-portal/show-monitoring.png)
2. Oddíl můžete připnout na řídicí panel tak, že vyberete se třemi tečkami (...) výše v části. Můžete také přizpůsobit velikost části nebo ji úplně odebrat. Následující obrázek ukazuje, jak připnout, upravit nebo odebrat oddíl procesoru a paměti.
   
    ![části kódu PIN](./media/resource-group-portal/pin-cpu-section.png)
3. Po Připnutí části řídicího panelu, zobrazí se souhrn na řídicím panelu. A okamžitě ji vyberete přejdete na další informace o datech.
   
    ![Zobrazení řídicí panel](./media/resource-group-portal/view-startboard.png)
4. Chcete-li zcela přizpůsobit data monitorování prostřednictvím portálu, přejděte na řídicí panel Výchozí a vyberte **novým řídicím panelem**.
   
    ![řídicí panel](./media/resource-group-portal/dashboard.png)
5. Pojmenujte váš nový řídicí panel a přetáhněte dlaždice na řídicím panelu. Dlaždice jsou filtrovány podle různé možnosti.
   
    ![řídicí panel](./media/resource-group-portal/create-dashboard.png)
   
     Další informace o práci s řídicích panelů najdete v tématu [sdílení řídicích panelů na portálu Azure a vytvoření](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Správa prostředků
Při zobrazení prostředku na portálu, zobrazí se možnosti pro správu danému zdroji.

![Správa prostředků](./media/resource-group-portal/manage-resources.png)

Z těchto možností můžete provádět operace, jako je například spuštění a zastavení virtuálního počítače nebo znovu konfigurovat vlastnosti virtuálního počítače.

## <a name="move-resources"></a>Přesunutí prostředků
Pokud potřebujete k přesunu prostředků do jiné skupině prostředků nebo jiné předplatné, přečtěte si [přesunutím prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

## <a name="lock-resources"></a>Uzamčení prostředků
Je možné zamknout předplatné, skupinu prostředků nebo prostředek zabránit ostatním uživatelům ve vaší organizaci neúmyslnému odstranění nebo úprava důležitých prostředků. Další informace najdete v tématu [Zamknutí prostředků pomocí Azure Resource Manageru](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Zobrazit vaše předplatné a náklady
Můžete zobrazit informace o předplatného a náklady na zahrnuté pro všechny vaše prostředky. Vyberte **odběry** a předplatné, které chcete zobrazit. Může mít pouze jeden odběr k výběru.

![předplatné](./media/resource-group-portal/select-subscription.png)

Pracovní tempo zobrazí.

![rychlost zápisu](./media/resource-group-portal/burn-rate.png)

A rozdělení nákladů podle typů prostředků.

![náklady na prostředek](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Export šablony
Po nastavení vaší skupiny prostředků, můžete zobrazit šablony Resource Manageru pro skupinu prostředků. Export šablony nabízí dvě výhody:

1. Budoucí nasazení řešení můžete snadno automatizovat, protože šablona obsahuje kompletní infrastrukturu.
2. Můžete seznámit se syntaxí šablony tak, že vyhledá na JSON JavaScript Object Notation () představující řešení.

Podrobné pokyny najdete v tématu [šablony exportovat Azure Resource Manageru ze stávajících prostředků](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Odstranit skupinu prostředků nebo prostředky
Odstranění skupiny prostředků se odstraní všechny prostředky, které jsou v něm obsažena. Můžete také odstranit jednotlivé prostředky ve skupině prostředků. Buďte opatrní při odstranění skupiny prostředků. Této skupiny prostředků může obsahovat prostředky, které jsou závislé na prostředcích v jiné skupině prostředků.

![Odstranění skupiny](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Další postup
* Chcete-li zobrazit protokoly aktivity, najdete v části [auditovat operace s Resource Managerem](resource-group-audit.md).
* Chcete-li zobrazit podrobnosti o nasazení, přečtěte si téma [zobrazit operace nasazení](resource-manager-deployment-operations.md).
* Nasazení prostředků prostřednictvím portálu najdete v tématu [nasazení prostředků pomocí šablony Resource Manageru a portálu Azure](resource-group-template-deploy-portal.md).
* Pokud chcete spravovat přístup k prostředkům, najdete v části [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](resource-manager-subscription-governance.md).

