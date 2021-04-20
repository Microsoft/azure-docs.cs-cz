---
title: Přesunutí oblastí pro prostředky v Microsoft. Resources
description: Ukazuje, jak přesunout prostředky, které jsou v oboru názvů Microsoft. Resources, do nových oblastí.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725867"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Přesunout prostředky Microsoft. Resources do nové oblasti

Možná budete muset přesunout existující prostředek do nové oblasti. Tento článek ukazuje, jak přesunout dva typy prostředků – templateSpecs a deploymentScripts – ty jsou v oboru názvů Microsoft. Resources.

## <a name="move-template-specs-to-new-region"></a>Přesunout specifikace šablony do nové oblasti

Pokud máte [specifikace šablony](../templates/template-specs.md) v jedné oblasti a chcete ji přesunout do nové oblasti, můžete ji exportovat a znovu ji nasadit.

1. Pomocí příkazu exportujte existující specifikaci šablony. Pro hodnoty parametrů zadejte hodnoty, které odpovídají specifikaci šablony, kterou chcete exportovat.

   Pro Azure PowerShell použijte:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Pokud používáte Azure CLI, použijte:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Použijte vyexportované specifikace šablony pro vytvoření nové specifikace šablony. Následující příklady ukazují na `westus` novou oblast, ale můžete zadat požadovanou oblast.

   Pro Azure PowerShell použijte:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Pokud používáte Azure CLI, použijte:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Přesunout skripty nasazení do nové oblasti

1. Vyberte skupinu prostředků, která obsahuje skript nasazení, který chcete přesunout do nové oblasti.

1. [Exportujte šablonu](../templates/export-template-portal.md). Při exportování vyberte skript nasazení a všechny další požadované prostředky.

1. V exportované šabloně odstraňte následující vlastnosti:

   * tenantId
   * principalId
   * clientId

1. Vyexportovaná šablona má hodnotu pevně zakódované pro oblast skriptu nasazení.

   ```json
   "location": "westus2",
   ```

   Změňte šablonu tak, aby povolovala parametr pro nastavení umístění. Další informace najdete v tématu [Nastavení umístění prostředků v ŠABLONĚ ARM](../templates/resource-location.md) .

   ```json
   "location": "[parameters('location')]",
   ```

1. [Nasaďte exportovanou šablonu](../templates/deploy-powershell.md) a zadejte novou oblast pro skript nasazení.

## <a name="next-steps"></a>Další kroky

* Další informace o přesouvání prostředků do nové skupiny prostředků nebo předplatného najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).
* Další informace o přesouvání prostředků do nové oblasti najdete v tématu [Přesunutí prostředků do různých oblastí](move-resources-overview.md#move-resources-across-regions).
