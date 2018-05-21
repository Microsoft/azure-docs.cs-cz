---
title: Nasazení prostředků Azure pomocí portálu Azure | Microsoft Docs
description: Portál Azure a Azure Resource Manageru použijte k nasazení vašich prostředků.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: c2232e1ce92eb6198b22bad22b8c3e10bab6bf12
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Nasazení prostředků pomocí šablon Resource Manageru a portálu Azure Portal

Toto téma ukazuje, jak používat [portál Azure](https://portal.azure.com) s [Azure Resource Manager](resource-group-overview.md) nasazení vašich prostředků Azure. Další informace o správě prostředků najdete v tématu [Azure spravovat prostředky prostřednictvím portálu](resource-group-portal.md).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

1. Vytvořit skupinu prostředků prázdný, vyberte **skupiny prostředků**.

   ![Výběr skupin zdrojů](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. V části skupiny prostředků, vyberte **přidat**.

   ![Přidat skupinu prostředků](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Poskytněte název a umístění a v případě potřeby vyberte předplatné. Je třeba zadat umístění pro skupinu prostředků, protože skupina prostředků ukládá metadata o prostředcích. Kvůli dodržování předpisů můžete určit, kam je uložen aby metadata. Doporučujeme obecně platí, že zadáte umístění, kde se bude nacházet většina vašich prostředků. Pomocí stejného umístění, můžete zjednodušit vaše šablony.

   ![nastavené hodnoty skupiny](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Po dokončení nastavení vlastností vyberte **vytvořit**.

1. Pokud chcete zobrazit nové skupiny prostředků, vyberte **aktualizovat**.

   ![Aktualizace skupiny prostředků](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Nasadit prostředky z webu Marketplace

Po vytvoření skupiny prostředků, můžete nasadit prostředky k němu z Marketplace. Marketplace obsahuje předem definovaná řešení pro běžné scénáře.

1. Chcete-li spustit nasazení, vyberte **vytvořit prostředek**.

   ![Nový prostředek](./media/resource-group-template-deploy-portal/new-resources.png)

1. Najdete typ prostředku, který chcete nasadit.

   ![Vyberte typ prostředku](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Pokud nevidíte konkrétní řešení, které chcete nasadit, můžete vyhledat na webu Marketplace ho. Například pokud chcete vyhledat řešení Wordpress, začněte psát **Wordpress** a vyberte požadovanou možnost.

   ![hledání marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. V závislosti na typu vybraného prostředku máte kolekci relevantní vlastnosti nastavit před nasazením. Pro všechny typy musíte vybrat cílové skupiny prostředků. Následující obrázek ukazuje, jak vytvořit webovou aplikaci a nasadit do skupiny prostředků, kterou jste vytvořili.

   ![Vytvoření skupiny prostředků](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternativně můžete vytvořit skupinu prostředků, při nasazení vašich prostředků. Vyberte **vytvořit nový** a zadejte název skupiny prostředků.

   ![Vytvořte novou skupinu prostředků](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Spustí se vaše nasazení. Nasazení může trvat několik minut. Pokud nasazení úspěšně proběhlo, zobrazí se upozornění.

   ![zobrazení oznámení](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po nasazení vašich prostředků, můžete přidat více prostředků do skupiny prostředků tak, že vyberete **přidat**.

   ![Přidat prostředek](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Nasadit prostředky z vlastní šablony

Pokud chcete provést nasazení, ale nechcete použít některou z šablon na webu Marketplace, můžete vytvořit vlastní šablonu, která definuje infrastrukturu pro vaše řešení. Další informace o vytváření šablon najdete v tématu [pochopit strukturu a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).

1. Nasadit vlastní šablonu prostřednictvím portálu, vyberte **vytvořit prostředek**a vyhledejte **nasazení šablony** dokud ho můžete vybrat z možností.

   ![nasazení šablony vyhledávání](./media/resource-group-template-deploy-portal/search-template.png)

1. Vyberte **Vytvořit**.

   ![Vyberte možnost vytvořit](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Zobrazí několik možností pro vytvoření šablony. Vyberte **sestavení vlastní šablonu v editoru**.

   ![Možnosti zobrazení](./media/resource-group-template-deploy-portal/see-options.png)

1. Máte prázdné šablonu, která je k dispozici pro přizpůsobení.

   ![Vytvoření šablony](./media/resource-group-template-deploy-portal/blank-template.png)

1. Můžete upravit syntaxe JSON ručně, nebo vybrat předdefinovaných šablony z [galerii pro rychlý start šablony](https://azure.microsoft.com/resources/templates/). Však použít pro tento článek **přidat prostředek** možnost.

   ![Upravit šablonu](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Vyberte **účet úložiště** a zadejte název. Po dokončení zadáním hodnot, vyberte **OK**.

   ![Výběr účtu úložiště](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Editor automaticky přidá JSON pro typ prostředku. Všimněte si, že tento systém obsahuje parametr pro definování typ účtu úložiště. Vyberte **Uložit**.

   ![Zobrazit šablonu](./media/resource-group-template-deploy-portal/show-json.png)

1. Nyní máte možnost nasadit prostředky definované v šabloně. Pro nasazení, přijmout podmínky a ujednání a vyberte **nákupu**.

   ![Nasazení šablony](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Nasadit prostředky ze šablony do účtu

Na portálu můžete uložit šablonu k účtu Azure a znovu ji nasaďte později. Další informace o práci s těmito uložit šablony [Začínáme se soukromými šablonami na portálu Azure](../marketplace-consumer/mytemplates-getstarted.md).

1. Chcete-li najít uložené šablony, vyberte **další služby**.

   ![Další služby](./media/resource-group-template-deploy-portal/more-services.png)

1. Vyhledejte **šablony** a vyberte tuto možnost.

   ![Prohledat šablony](./media/resource-group-template-deploy-portal/find-templates.png)

1. Seznam šablon do účtu vyberte ten, který si přejete pracovat na.

   ![uložené šablony](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Vyberte **nasadit** k opětovnému nasazení této šablony uložené.

   ![nasazení uloženého šablony](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Další postup
* Chcete-li zobrazit protokoly auditu, najdete v části [auditovat operace s Resource Managerem](resource-group-audit.md).
* Chcete-li vyřešit chyby nasazení, přečtěte si téma [zobrazit operace nasazení](resource-manager-deployment-operations.md).
* Pokud chcete načíst šablonu z nasazení nebo skupinu prostředků, najdete v části [šablony exportovat Azure Resource Manageru ze stávajících prostředků](resource-manager-export-template.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](resource-manager-subscription-governance.md).
