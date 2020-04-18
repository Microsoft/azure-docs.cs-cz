---
title: 'Úvodní příručka: Nové přiřazení zásad se šablonami'
description: V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: ae6b2d151baaa904215639276c7fb74766810c6a
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606599"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Úvodní příručka: Vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků pomocí šablony Správce prostředků

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásad a přiřadíte předdefinovanou definici zásad nazvanou _Auditovat virtuální počítače, které nepoužívají spravované disky_. Částečný seznam dostupných předdefinovaných zásad najdete v [tématu ukázky zásad Azure](./samples/index.md).

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

Zdroj definovaný v šabloně je:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Nasazení šablony

> [!NOTE]
> Služba Azure Policy je zdarma. Další informace najdete [v tématu Přehled zásad Azure](./overview.md).

1. Vyberte následující obrázek, přihlaste se k webu Azure Portal a otevřete šablonu:

   [![Nasazení šablony zásad do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty:

   | Název | Hodnota |
   |------|-------|
   | Předplatné | Vyberte své předplatné Azure. |
   | Skupina prostředků | Vyberte **Vytvořit nový**, zadejte název a pak vyberte **OK**. Na snímku obrazovky je název skupiny prostředků _\<myPolicyquickstart\>Date v MMDD rg_. |
   | Umístění | Vyberte oblast. Například **střední USA**. |
   | Název přiřazení zásady | Zadejte název přiřazení zásady. Pokud chcete, můžete použít zobrazení definice zásad. Například **auditovat virtuální počítače, které nepoužívají spravované disky**. |
   | Název rg | Zadejte název skupiny prostředků, ke které chcete zásadu přiřadit. V tomto rychlém startu použijte výchozí hodnotu **[resourceGroup().name]**. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** je funkce šablony, která načítá skupinu prostředků. |
   | ID definice zásad | Zadejte **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a .** |
   | Souhlasím s výše uvedenými podmínkami | (Vybrat) |

1. Vyberte **Koupit**.

Některé další zdroje:

- Další ukázkové šablony najdete v [tématu Azure Quickstart template](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Pokud chcete zobrazit odkaz na šablonu, přejděte na [odkaz na šablonu Azure](/azure/templates/microsoft.authorization/allversions).
- Informace o vývoji šablon Správce prostředků najdete v [dokumentaci ke Správci prostředků Azure](../../azure-resource-manager/management/overview.md).
- Informace o nasazení na úrovni předplatného najdete [v tématu Vytvoření skupin prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

V levé části stránky vyberte **Dodržování předpisů.** Potom vyhledejte **virtuální počítače auditu, které nepoužívají** přiřazení zásad spravované disky, které jste vytvořili.

![Stránka přehledu dodržování předpisů zásad](./media/assign-policy-template/policy-compliance.png)

Pokud existují existující prostředky, které nejsou kompatibilní s tímto novým **přiřazením,** zobrazí se v části Nekompatibilní prostředky .

Další informace naleznete v tématu [Jak funguje dodržování předpisů](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořené přiřazení, postupujte takto:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klepněte pravým tlačítkem myši na **moduly Auditovat virtuální počítače, které nepoužívají** přiřazení zásad spravovaného disku, a vyberte příkaz **Odstranit přiřazení**.

   ![Odstranění přiřazení ze stránky přehledu dodržování předpisů](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste k oboru přiřadili předdefinovanou definici zásad a vyhodnotili jste jeho sestavu dodržování předpisů. Definice zásadověřuje, že všechny prostředky v oboru jsou kompatibilní a identifikuje, které z nich nejsou.

Další informace o přiřazení zásad k ověření, že nové prostředky jsou kompatibilní, pokračujte v kurzu pro:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)