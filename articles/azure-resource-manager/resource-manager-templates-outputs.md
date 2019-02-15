---
title: Vypíše šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje, jak definovat výstupy pro šablony Azure Resource Manageru pomocí deklarativní syntaxe JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 92e5dd5190a76bd09e33ea4c40a5b5cc2d66bc7b
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301124"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Část Outputs následujícím v šablonách Azure Resource Manageru

V části výstupů zadáte hodnoty, které se vracejí z nasazení. Například může vrátit identifikátor URI pro přístup k nasazených prostředků. Použít nepovinný `condition` vlastnosti k určení, zda je vrácena hodnota výstupu.

## <a name="define-and-use-output-values"></a>Definice a používání výstupní hodnoty

Následující příklad ukazuje, jak vrátit ID prostředku pro veřejnou IP adresu:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Následující příklad ukazuje, jak podmíněně vrátit ID prostředku pro veřejnou IP adresu na základě, jestli je nový, jeden byla nasazena:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Po nasazení můžete načíst hodnotu pomocí skriptu. Pokud používáte PowerShell, použijte:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Výstupní hodnota z propojené šablony můžete načíst pomocí [odkaz](resource-group-template-functions-resource.md#reference) funkce. Výstupní hodnota z propojené šablony získáte načtení hodnoty vlastností se syntaxí, jako jsou: `"[reference('deploymentName').outputs.propertyName.value]"`.

Při získávání výstupu vlastnost z propojené šablony, název vlastnosti nemůže obsahovat čárku.

Můžete například nastavit IP adresu nástroje pro vyrovnávání zatížení načtením hodnotu z propojené šablony.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nelze použít `reference` funkce v části výstupů [vnořené šablony](resource-group-linked-templates.md#link-or-nest-a-template). Na návratové hodnoty pro nasazený prostředek ve vnořené šablony, převeďte vnořené šablony na propojenou šablonu.

## <a name="available-properties"></a>Dostupné vlastnosti

Následující příklad ukazuje strukturu definici výstupu:

```json
"outputs": {
    "<outputName>" : {
        "condition": "<boolean-value-whether-to-output-value>",
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| outputName |Ano |Název výstupní hodnoty. Musí být platný identifikátor jazyka JavaScript. |
| condition |Ne | Logická hodnota označující, zda tento výstupní hodnota je vrácena. Když `true`, hodnota je součástí výstupu pro nasazení. Když `false`, výstupní hodnota je vynecháno pro toto nasazení. Pokud není zadán, výchozí hodnota je `true`. |
| type |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. |
| hodnota |Ano |Výraz jazyka šablony, která je vyhodnocena a vrátila jako výstupní hodnota. |

Informace o přidávání komentářů najdete v tématu [komentáře v šablonách](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Příklad šablony

|Šablona  |Popis  |
|---------|---------|
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří proměnné komplexní a vrací tyto hodnoty. Nenasadí žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vypíše ID prostředku. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Obsahuje odkazy na předchozí šablonu postupem. Při vytváření nástroje pro vyrovnávání zatížení, používá ID prostředku ve výstupu. |


## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkce, které můžete použít z v rámci šablony najdete v tématu [funkce šablon Azure Resource Manageru](resource-group-template-functions.md).
* Doporučení o vytváření šablon naleznete v tématu [osvědčené postupy pro šablony Azure Resource Manageru](template-best-practices.md).
