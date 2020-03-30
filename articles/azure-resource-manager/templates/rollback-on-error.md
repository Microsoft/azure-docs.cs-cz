---
title: Vrátit se zpět k chybě k úspěšnému nasazení
description: Určete, že neúspěšné nasazení by se mělo vrátit k úspěšnému nasazení.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460139"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Vrácení zpět při chybě k úspěšnému nasazení

Pokud se nasazení nezdaří, můžete automaticky znovu nasadit dřívější, úspěšné nasazení z historie nasazení. Tato funkce je užitečná, pokud máte známý dobrý stav pro nasazení infrastruktury a chcete se vrátit do tohoto stavu. Existuje celá řada upozornění a omezení:

- Opětovné nasazení je spuštěno přesně tak, jak bylo spuštěno dříve se stejnými parametry. Parametry nemůžete změnit.
- Předchozí nasazení je spuštěno v [režimu dokončení](./deployment-modes.md#complete-mode). Všechny prostředky, které nejsou zahrnuty v předchozím nasazení, budou odstraněny a všechny konfigurace prostředků jsou nastaveny na předchozí stav. Ujistěte se, že plně rozumíte [režimům nasazení](./deployment-modes.md).
- Opětovné nasazení má vliv pouze na prostředky, změny dat nejsou ovlivněny.
- Tuto funkci můžete použít pouze s nasazeními skupin prostředků, nikoli s předplatným nebo nasazením na úrovni skupiny pro správu. Další informace o nasazení na úrovni předplatného naleznete v [tématu Vytvoření skupin prostředků a prostředků na úrovni předplatného](./deploy-to-subscription.md).
- Tuto možnost lze použít pouze s nasazeními na úrovni kořenové úrovně. Nasazení z vnořené šablony nejsou k dispozici pro opětovné nasazení.

Chcete-li použít tuto možnost, vaše nasazení musí mít jedinečné názvy, aby mohly být identifikovány v historii. Pokud nemáte jedinečné názvy, aktuální neúspěšné nasazení může přepsat dříve úspěšné nasazení v historii.

## <a name="powershell"></a>PowerShell

Chcete-li znovu nasadit poslední `-RollbackToLastDeployment` úspěšné nasazení, přidejte parametr jako příznak.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Chcete-li znovu nasadit `-RollBackDeploymentName` konkrétní nasazení, použijte parametr a zadejte název nasazení. Zadané nasazení musí mít úspěšné.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Chcete-li znovu nasadit poslední `--rollback-on-error` úspěšné nasazení, přidejte parametr jako příznak.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Chcete-li znovu nasadit `--rollback-on-error` konkrétní nasazení, použijte parametr a zadejte název nasazení. Zadané nasazení musí mít úspěšné.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Chcete-li znovu nasadit poslední úspěšné nasazení, pokud se aktuální nasazení nezdaří, použijte:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Chcete-li znovu nasadit konkrétní nasazení, pokud se aktuální nasazení nezdaří, použijte:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Zadané nasazení musí mít úspěšné.

## <a name="next-steps"></a>Další kroky

- Pokud chcete bezpečně zavést službu do více než jedné oblasti, přečtěte si informace [o Tom, jak najít Správce nasazení Azure](deployment-manager-overview.md).
- Pokud chcete určit, jak zpracovat prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, najdete v [tématu režimy nasazení Správce prostředků Azure](deployment-modes.md).
- Informace o tom, jak definovat parametry v šabloně, [najdete v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
- Informace o nasazení šablony, která vyžaduje token SAS, naleznete v [tématu Nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
