---
title: 'Rychlý Start: nové přiřazení zásad pomocí šablon'
description: V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy.
ms.date: 05/21/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: c3a37e9086744fb45c8d1e4ebe3bd16da7a83770
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971126"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-azure-resource-manager-template"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí šablony Azure Resource Manager

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý start vás provede procesem vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásady

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici předdefinované zásady nazvanou _audit virtuálních počítačů, které nepoužívají spravované disky_. Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-30" highlight="20-28":::

Prostředek definovaný v šabloně je:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Nasazení šablony

> [!NOTE]
> Služba Azure Policy je zadarmo. Další informace najdete v tématu [přehled Azure Policy](./overview.md).

1. Vyberte následující obrázek, přihlaste se k webu Azure Portal a otevřete šablonu:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení šablony ARM pro přiřazení Azure Policy k Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. Vyberte nebo zadejte následující hodnoty:

   | Name | Hodnota |
   |------|-------|
   | Předplatné | Vyberte své předplatné Azure. |
   | Skupina prostředků | Vyberte **vytvořit novou**, zadejte název a pak vyberte **OK**. Název skupiny prostředků na snímku obrazovky je _mypolicyquickstart \<Date in MMDD\> RG_. |
   | Umístění | Vyberte oblast. Například **USA – střed**. |
   | Název přiřazení zásady | Zadejte název přiřazení zásady. Pokud chcete, můžete použít zobrazení definice zásad. Například proveďte **audit virtuálních počítačů, které nepoužívají spravované disky**. |
   | Název RG | Zadejte název skupiny prostředků, ke které chcete zásadu přiřadit. V tomto rychlém startu použijte výchozí hodnotu **[resourceName (). name]**. Skupina prostředků **[()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** je šablonou funkce, která načítá skupinu prostředků. |
   | ID definice zásady | Zadejte **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-b460-a2d36003525a**. |
   | Souhlasím s podmínkami a ujednáními uvedenými nahoře | Vybrali |

1. Vyberte **Koupit**.

Některé další zdroje informací:

- Další šablony ukázek najdete v tématu [Šablona Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Odkaz na šablonu zobrazíte tak, že přejdete na [odkaz šablony Azure](/azure/templates/microsoft.authorization/allversions).
- Další informace o vývoji šablon Správce prostředků (šablony ARM) najdete v [dokumentaci k Správce prostředků](../../azure-resource-manager/management/overview.md).
- Pokud chcete zjistit nasazení na úrovni předplatného, přečtěte si téma [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte **auditované virtuální počítače, které nepoužívají přiřazení zásad spravovaných disků** , které jste vytvořili.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Stránka s přehledem dodržování zásad" border="false":::

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Další informace najdete v tématu [Jak funguje dodržování předpisů](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady **Audit virtuálních počítačů, které nepoužívají spravované disky**, které jste vytvořili.

1. Klikněte pravým tlačítkem na **Auditovat virtuální počítače, které nepoužívají správu zásad spravovaných disků** , a vyberte **Odstranit přiřazení**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Odstranění přiřazení na stránce Přehled dodržování předpisů" border="false":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili integrovanou definici zásady k oboru a vyhodnotili sestavu dodržování předpisů. Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)