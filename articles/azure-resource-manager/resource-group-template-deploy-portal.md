---
title: Nasazujte prostředky Azure pomocí webu Azure portal | Dokumentace Microsoftu
description: Nasazení prostředků pomocí Azure Resource Manageru a webu Azure portal.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: b8a6d58ad79b591548542d69e69b71ec61af79af
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220121"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Nasazení prostředků pomocí šablon Resource Manageru a portálu Azure Portal

Tento článek popisuje, jak používat [webu Azure portal](https://portal.azure.com) s [Azure Resource Manageru](resource-group-overview.md) k nasazení prostředků Azure. Další informace o správě prostředků najdete v tématu [Správa prostředků Azure prostřednictvím portálu](resource-group-portal.md).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

1. Chcete-li vytvořit prázdné skupiny prostředků, vyberte **skupiny prostředků**.

   ![Výběr skupin zdrojů](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. V rámci skupiny prostředků, vyberte **přidat**.

   ![Přidat skupinu prostředků](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Zadejte jeho název a umístění a v případě potřeby vyberte předplatné. Budete muset zadat umístění pro skupinu prostředků, protože skupina prostředků ukládá metadata o prostředcích. Kvůli dodržování předpisů můžete určit, kde se tato metadata ukládají. Obecně platí doporučujeme vám, že zadáte umístění, kde se bude nacházet maximální využití vašich prostředků. Pomocí stejného umístění může zjednodušit vaši šablonu.

   ![Nastavte hodnoty skupiny](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Po dokončení nastavení vlastností, vyberte **vytvořit**.

1. Pokud chcete zobrazit novou skupinu prostředků, vyberte **aktualizovat**.

   ![Aktualizace skupiny prostředků](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Nasazení prostředků z Marketplace

Když vytvoříte skupinu prostředků, můžete nasadit prostředky do ní z webu Marketplace. Na webu Marketplace poskytuje předdefinované řešení pro běžné scénáře.

1. Chcete-li spustit nasazení, vyberte **vytvořit prostředek**.

   ![Nový prostředek](./media/resource-group-template-deploy-portal/new-resources.png)

1. Najdete typ prostředku, který chcete nasadit.

   ![Vyberte typ prostředku](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Pokud nevidíte konkrétní řešení, které chcete nasadit, můžete pro ni Hledat na Marketplace. Například na vyhledání řešení Wordpress, začněte psát **Wordpress** a vyberte požadovanou možnost.

   ![Hledat v marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. V závislosti na typu vybraného prostředku máte kolekci příslušné vlastnosti a nastavte před nasazením. Pro všechny typy musíte vybrat cílová skupina prostředků. Následující obrázek ukazuje, jak vytvořit webovou aplikaci a nasadit ho do skupiny prostředků, kterou jste vytvořili.

   ![Vytvoření skupiny prostředků](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Alternativně můžete vytvořit skupinu prostředků, při nasazování prostředků. Vyberte **vytvořit nový** a zadejte název skupiny prostředků.

   ![Vytvořte novou skupinu prostředků](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Spustí se vaše nasazení. Nasazení může trvat několik minut. Po dokončení nasazení se zobrazí oznámení.

   ![Zobrazení oznámení](./media/resource-group-template-deploy-portal/view-notification.png)

1. Po nasazení vaše prostředky, můžete přidat další prostředky do skupiny prostředků tak, že vyberete **přidat**.

   ![Přidat prostředek](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Nasazení prostředků z vlastní šablony

Pokud chcete provést nasazení, ale ne použít některý z šablony na webu Marketplace, můžete vytvořit vlastní šablonu, která definuje infrastrukturu pro vaše řešení. Další informace o vytváření šablon najdete v tématu [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).

> [!NOTE]
> Rozhraní portálu nepodporuje odkazování [tajného kódu ze služby Key Vault](resource-manager-keyvault-parameter.md). Místo toho použijte [PowerShell](resource-group-template-deploy.md) nebo [rozhraní příkazového řádku Azure](resource-group-template-deploy-cli.md) k nasazení vaší šablony, místně nebo z externí identifikátor URI.

1. Pokud chcete nasadit vlastní šablony na portálu, vyberte **vytvořit prostředek**a vyhledejte **nasazení šablony** abyste mohli vybrat z možností.

   ![Hledat šablony nasazení](./media/resource-group-template-deploy-portal/search-template.png)

1. Vyberte **Vytvořit**.

   ![Výběr možnosti vytvoření](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Uvidíte několik možností pro vytváření šablony. Vyberte **vytvořit vlastní šablonu v editoru**.

   ![Možnosti zobrazení](./media/resource-group-template-deploy-portal/see-options.png)

1. Budete mít prázdnou šablonou, která je k dispozici pro přizpůsobení.

   ![Vytvoření šablony](./media/resource-group-template-deploy-portal/blank-template.png)

1. Můžete ručně upravit syntaxi skriptu JSON nebo vybrat předem sestavené šablony z [Galerie šablon rychlý Start](https://azure.microsoft.com/resources/templates/). Ale pro účely tohoto článku můžete použít **přidat prostředek** možnost.

   ![Upravit šablonu](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Vyberte **účtu úložiště** a zadejte název. Až budete hotovi se zadáváním hodnot, vyberte **OK**.

   ![Výběr účtu úložiště](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Editor automaticky přidá JSON pro daný typ prostředku. Všimněte si, že obsahuje parametr pro definování typu účtu úložiště. Vyberte **Uložit**.

   ![Zobrazit šablonu](./media/resource-group-template-deploy-portal/show-json.png)

1. Nyní máte možnost nasadit prostředky definované v šabloně. K nasazení, přijměte podmínky a ujednání a vyberte **nákupní**.

   ![Nasazení šablony](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Nasazení prostředků ze šablony do účtu

Na portálu můžete uložit šablonu ke svému účtu Azure a později znovu nasadit. Další informace o šablonách najdete v tématu [vytvoření a nasazení první šablony Azure Resource Manageru](resource-manager-create-first-template.md).

1. Chcete-li najít uložené šablony, vyberte **další služby**.

   ![Další služby](./media/resource-group-template-deploy-portal/more-services.png)

1. Vyhledejte **šablony** a vyberte příslušnou možnost.

   ![Prohledat šablony](./media/resource-group-template-deploy-portal/find-templates.png)

1. Seznam šablon do účtu vyberte ten, který chcete pracovat.

   ![Uložené šablony](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Vyberte **nasadit** k opětovnému nasazení této uložené šablony.

   ![Nasazení uloženého šablony](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Další postup
* Protokoly auditu najdete v tématu [Audit operací pomocí Resource Manageru](resource-group-audit.md).
* Řešení chyb nasazení, najdete v článku [zobrazení operací nasazení](resource-manager-deployment-operations.md).
* Načíst šablonu z nasazení nebo skupinu prostředků, najdete v článku [šablony exportovat Azure Resource Manageru z existujících prostředků](resource-manager-export-template.md).
* Bezpečně zavedení služby napříč několika oblastmi, najdete v článku [Azure Deployment Manager](deployment-manager-overview.md).
