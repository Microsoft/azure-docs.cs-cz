---
title: Vrátit zpět chybu pro úspěšné nasazení
description: Určete, že nasazení, které selhalo, by se mělo vrátit zpátky k úspěšnému nasazení.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79460139"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Vrácení chyby zpět při úspěšném nasazení

V případě neúspěchu nasazení můžete z historie nasazení automaticky znovu nasadit předchozí úspěšné nasazení. Tato funkce je užitečná v případě, že máte známý dobrý stav pro nasazení infrastruktury a chcete se vrátit k tomuto stavu. Existuje několik aspektů a omezení:

- Opětovné nasazení se spouští přesně tak, jak bylo dříve spuštěno se stejnými parametry. Nemůžete změnit parametry.
- Předchozí nasazení se spouští v [režimu úplného](./deployment-modes.md#complete-mode)použití. Všechny prostředky, které nejsou součástí předchozího nasazení, se odstraní a všechny konfigurace prostředků se nastavují do jejich předchozího stavu. Ujistěte se, že plně rozumíte [režimům nasazení](./deployment-modes.md).
- Opětovné nasazení má vliv pouze na prostředky, změny dat nejsou ovlivněny.
- Tuto funkci můžete použít jenom v nasazeních skupin prostředků, ne na úrovni předplatného nebo skupiny pro správu. Další informace o nasazení na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](./deploy-to-subscription.md).
- Tuto možnost můžete použít jenom u nasazení na kořenové úrovni. Nasazení z vnořené šablony nejsou k dispozici pro opětovné nasazení.

Chcete-li použít tuto možnost, musí mít vaše nasazení jedinečné názvy, aby je bylo možné identifikovat v historii. Pokud nemáte jedinečné názvy, může aktuální nasazení v historii přepsat dříve úspěšné nasazení.

## <a name="powershell"></a>PowerShell

Chcete-li znovu nasadit poslední úspěšné nasazení, přidejte `-RollbackToLastDeployment` parametr jako příznak.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Chcete-li znovu nasadit konkrétní nasazení, použijte `-RollBackDeploymentName` parametr a zadejte název nasazení. Zadané nasazení musí být úspěšné.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Chcete-li znovu nasadit poslední úspěšné nasazení, přidejte `--rollback-on-error` parametr jako příznak.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Chcete-li znovu nasadit konkrétní nasazení, použijte `--rollback-on-error` parametr a zadejte název nasazení. Zadané nasazení musí být úspěšné.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Pokud chcete znovu nasadit poslední úspěšné nasazení, pokud aktuální nasazení neproběhne úspěšně, použijte:

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

Pokud chcete znovu nasadit konkrétní nasazení, pokud aktuální nasazení neproběhne úspěšně, použijte:

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

Zadané nasazení musí být úspěšné.

## <a name="next-steps"></a>Další kroky

- Pokud chcete službu bezpečně zavést do více než jedné oblasti, přečtěte si [Azure Deployment Manager](deployment-manager-overview.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
