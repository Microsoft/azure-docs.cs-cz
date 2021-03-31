---
title: Vrátit zpět chybu pro úspěšné nasazení
description: Určete, že nasazení, které selhalo, by se mělo vrátit zpátky k úspěšnému nasazení.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492208"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Vrácení chyby zpět při úspěšném nasazení

V případě neúspěchu nasazení můžete z historie nasazení automaticky znovu nasadit předchozí úspěšné nasazení. Tato funkce je užitečná v případě, že máte známý dobrý stav pro nasazení infrastruktury a chcete se vrátit k tomuto stavu. Můžete zadat buď konkrétní dřívější nasazení, nebo poslední úspěšné nasazení.

> [!IMPORTANT]
> Tato funkce vrátí nasazení, které selhalo, a znovu nasadí předchozí nasazení. Tento výsledek se může lišit od zrušení neúspěšného nasazení, které byste očekávali. Ujistěte se, že rozumíte tomu, jak se nasazuje předchozí nasazení.

## <a name="considerations-for-redeploying"></a>Pokyny pro opětovné nasazení

Před použitím této funkce zvažte tyto podrobnosti o způsobu, jakým je prováděno opětovné nasazení:

- Předchozí nasazení je spuštěno v [režimu úplné](./deployment-modes.md#complete-mode), i když jste použili [přírůstkový režim](./deployment-modes.md#incremental-mode) během dřívějšího nasazení. Opětovné nasazení v úplném režimu může způsobit neočekávané výsledky, pokud se předchozí použité nasazení přírůstkově použilo. Celý režim znamená, že se odstraní všechny prostředky, které nejsou zahrnuté do předchozího nasazení. Zadejte starší nasazení, které bude představovat všechny prostředky a jejich stavy, které mají být ve skupině prostředků. Další informace najdete v tématu [režimy nasazení](./deployment-modes.md).
- Opětovné nasazení se spouští přesně tak, jak bylo dříve spuštěno se stejnými parametry. Nemůžete změnit parametry.
- Opětovné nasazení má vliv pouze na prostředky, změny dat nejsou ovlivněny.
- Tuto funkci můžete použít jenom s nasazeními skupiny prostředků. Nepodporuje nasazení na úrovni předplatného, skupiny pro správu ani tenanta. Další informace o nasazení na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](./deploy-to-subscription.md).
- Tuto možnost můžete použít jenom u nasazení na kořenové úrovni. Nasazení z vnořené šablony nejsou k dispozici pro opětovné nasazení.

Chcete-li použít tuto možnost, musí mít vaše nasazení jedinečné názvy v historii nasazení. Je to jenom s jedinečnými názvy, ke kterým se dá identifikovat konkrétní nasazení. Pokud nepoužíváte jedinečné názvy, nasazení, které selhalo, může v historii přepsat úspěšné nasazení.

Pokud zadáte dřívější nasazení, které v historii nasazení neexistuje, vrácení zpět vrátí chybu.

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

- Podrobné informace o kompletních a přírůstkových režimech najdete v tématu [Azure Resource Manager režimy nasazení](deployment-modes.md).
- Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
