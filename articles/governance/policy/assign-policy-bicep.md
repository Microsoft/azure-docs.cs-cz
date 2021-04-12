---
title: 'Rychlý Start: nové přiřazení zásad se souborem bicep (Preview)'
description: V tomto rychlém startu použijete soubor bicep (Preview) k vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223968"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí souboru bicep

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků.
Tento rychlý Start vás provede procesem použití souboru [bicep (Preview)](https://github.com/Azure/bicep) zkompilovaného do šablony Azure Resource Manager (šablona ARM) k vytvoření přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Na konci tohoto procesu úspěšně identifikujete virtuální počítače, které nepoužívají spravované disky. _Neodpovídají_ přiřazení zásad.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v Azure Portal.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Tlačítko pro nasazení šablony ARM pro přiřazení Azure Policy k Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Je `0.3` nainstalovaná verze bicep nebo novější. Pokud ještě nemáte bicep CLI nebo potřebujete aktualizace, přečtěte si téma [install bicep (Preview)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Kontrola souboru bicep

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte definici předdefinované zásady nazvanou _audit virtuálních počítačů, které nepoužívají spravované disky_ ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Částečný seznam dostupných integrovaných zásad najdete v tématu [Azure Policy Samples](./samples/index.md).

Vytvořte následující soubor bicep jako `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

Prostředek definovaný v souboru je:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Nasazení šablony

> [!NOTE]
> Služba Azure Policy je zadarmo. Další informace najdete v tématu [přehled Azure Policy](./overview.md).

Po instalaci rozhraní příkazového řádku bicep a vytvoření souboru můžete nasadit soubor bicep pomocí:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Některé další zdroje informací:

- Další šablony ukázek najdete v tématu [Šablona Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Odkaz na šablonu zobrazíte tak, že přejdete na [odkaz šablony Azure](/azure/templates/microsoft.authorization/allversions).
- Informace o vývoji šablon ARM najdete v [dokumentaci Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Pokud chcete zjistit nasazení na úrovni předplatného, přečtěte si téma [Vytvoření skupin prostředků a prostředků na úrovni předplatného](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

Na levé straně stránky vyberte **dodržování předpisů** . Pak vyhledejte _auditované virtuální počítače, které nepoužívají přiřazení zásad spravovaných disků_ , které jste vytvořili.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Snímek obrazovky s podrobnostmi o kompatibilitě na stránce dodržování zásad" border="false":::

Pokud nějaké existující prostředky nedodržují předpisy tohoto nového přiřazení, zobrazí se v části **nekompatibilní prostředky**.

Další informace najdete v tématu [Jak funguje dodržování předpisů](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného přiřazení použijte následující postup:

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů** (nebo **Přiřazení**) a vyhledejte přiřazení zásady _Audit virtuálních počítačů, které nepoužívají spravované disky_, které jste vytvořili.

1. Klikněte pravým tlačítkem na _Auditovat virtuální počítače, které nepoužívají správu zásad spravovaných disků_ , a vyberte **Odstranit přiřazení**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Snímek obrazovky s použitím kontextové nabídky k odstranění přiřazení ze stránky dodržování předpisů." border="false":::

1. Odstraňte `assignment.bicep` soubor.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili integrovanou definici zásady k oboru a vyhodnotili sestavu dodržování předpisů. Definice zásady ověří, že všechny prostředky v oboru jsou v souladu s předpisy, a určí, které z nich nejsou.

Další informace o přiřazování zásad k ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)