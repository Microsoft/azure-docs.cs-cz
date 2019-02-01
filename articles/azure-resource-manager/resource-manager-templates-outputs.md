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
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: 29181b19498b6735651869b6499c4a1cda5a4c3a
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488841"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Část Outputs následujícím v šablonách Azure Resource Manageru

V části výstupů zadáte hodnoty, které se vracejí z nasazení. Například může vrátit identifikátor URI pro přístup k nasazených prostředků.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Název elementu | Požaduje se | Popis |
|:--- |:--- |:--- |
| outputName |Ano |Název výstupní hodnoty. Musí být platný identifikátor jazyka JavaScript. |
| type |Ano |Typ výstupní hodnoty. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. |
| hodnota |Ano |Výraz jazyka šablony, která je vyhodnocena a vrátila jako výstupní hodnota. |


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
