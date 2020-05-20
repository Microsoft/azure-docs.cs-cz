---
title: Nasazení prostředků mezi předplatnými & skupiny prostředků
description: Ukazuje, jak během nasazení cílit více než jedno předplatné Azure a skupinu prostředků.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650639"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Nasazení prostředků Azure v rámci předplatných nebo skupin prostředků

Správce prostředků umožňuje nasazení do více než jedné skupiny prostředků v jednom nasazení. Vnořené šablony můžete použít k určení skupin prostředků, které se liší od skupiny prostředků v rámci operace nasazení. Skupiny prostředků můžou existovat v různých předplatných.

> [!NOTE]
> V jednom nasazení můžete nasadit do **skupin prostředků 800** . Obvykle toto omezení znamená, že můžete nasadit do jedné skupiny prostředků zadané pro nadřazenou šablonu a až 799 skupiny prostředků ve vnořených nebo propojených nasazeních. Pokud ale vaše nadřazená šablona obsahuje jenom vnořené nebo propojené šablony a sám o sobě neimplementuje žádné prostředky, můžete do vnořených nebo propojených nasazení zahrnout až 800 skupin prostředků.

## <a name="specify-subscription-and-resource-group"></a>Zadat předplatné a skupinu prostředků

Chcete-li cílit na skupinu prostředků, která je jiná než ta pro nadřazenou šablonu, použijte [vnořenou nebo propojenou šablonu](linked-templates.md). V části typ prostředku nasazení zadejte hodnoty pro ID předplatného a skupinu prostředků, do které chcete vnořenou šablonu nasadit.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Pokud nezadáte ID předplatného nebo skupinu prostředků, použije se předplatné a skupina prostředků z nadřazené šablony. Před spuštěním nasazení musí existovat všechny skupiny prostředků.

Účet, který nasazuje šablonu, musí mít oprávnění k nasazení na zadané ID předplatného. Pokud zadané předplatné existuje v jiném tenantovi Azure Active Directory, musíte [Přidat uživatele typu host z jiného adresáře](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Následující příklad nasadí dva účty úložiště. První účet úložiště se nasadí do skupiny prostředků zadané v operaci nasazení. Druhý účet úložiště se nasadí do skupiny prostředků zadané v `secondResourceGroup` `secondSubscriptionID` parametrech a:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Pokud nastavíte `resourceGroup` název skupiny prostředků, která neexistuje, nasazení se nezdařilo.

K otestování předchozí šablony a zobrazení výsledků použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete nasadit dva účty úložiště do dvou skupin prostředků ve **stejném předplatném**, použijte:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

K nasazení dvou účtů úložiště do **dvou předplatných**použijte:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nasadit dva účty úložiště do dvou skupin prostředků ve **stejném předplatném**, použijte:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

K nasazení dvou účtů úložiště do **dvou předplatných**použijte:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Použití funkcí

Funkce [Resource ()](template-functions-resource.md#resourcegroup) a [Subscription ()](template-functions-resource.md#subscription) se vyřeší odlišně podle toho, jak zadáte šablonu. Když propojíte externí šablonu, funkce se vždy vyhodnotí do oboru pro tuto šablonu. Při vnořování šablony v rámci nadřazené šablony použijte `expressionEvaluationOptions` vlastnost k určení, zda funkce překládá na skupinu prostředků a odběr nadřazené šablony nebo vnořené šablony. Nastavte vlastnost na hodnotu `inner` , aby se přeložila na obor vnořené šablony. Nastavte vlastnost na hodnotu `outer` , aby se přeložila na rozsah nadřazené šablony.

Následující tabulka ukazuje, jestli se funkce překládají na nadřazenou nebo integrovanou skupinu prostředků a předplatné.

| Typ šablony | Rozsah | Řešení |
| ------------- | ----- | ---------- |
| vnořen        | vnější (výchozí) | Nadřazená skupina prostředků |
| vnořen        | vnořen | Dílčí skupina prostředků |
| Spojeného        | –   | Dílčí skupina prostředků |

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) ukazuje:

* vnořená šablona s výchozím (vnějším) rozsahem
* vnořená šablona s vnitřním rozsahem
* odkazovaná šablona

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

K otestování předchozí šablony a zobrazení výsledků použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Další kroky

* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
* Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
