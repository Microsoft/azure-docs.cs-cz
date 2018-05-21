---
title: Výstupy šablony Azure Resource Manager | Microsoft Docs
description: Popisuje, jak definovat výstupy pro šablon Azure Resource Manageru pomocí deklarativní syntaxe JSON.
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
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: e3c5a581b02f1dd7b7415ebd93de0e425ac2f8ae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Výstupy část v šablonách Azure Resource Manager
V části výstupy zadejte hodnoty, které jsou vráceny z nasazení. Například může vrátit identifikátor URI pro přístup k prostředkům nasazené.

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

Po dokončení nasazení můžete načíst hodnotu pomocí skriptu. Pokud používáte PowerShell, použijte:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Výstupní hodnotu z propojené šablony můžete načíst pomocí [odkaz](resource-group-template-functions-resource.md#reference) funkce. Výstupní hodnotu z propojené šablony získáte načíst hodnotu vlastnosti se syntaxí, jako je: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Můžete například nastavit IP adresu zařízení na Vyrovnávání zatížení načtením hodnotu z propojené šablony.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nelze použít `reference` funkce v části výstupy [vnořené šablony](resource-group-linked-templates.md#link-or-nest-a-template). K návratu hodnot pro prostředek nasazené v šabloně vnořené, převeďte na šablonu propojené vnořené šablony.

## <a name="available-properties"></a>Dostupné vlastnosti

Následující příklad ukazuje strukturu definici výstup:

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
| outputName |Ano |Název výstupní hodnotu. Musí být platný identifikátor jazyka JavaScript. |
| type |Ano |Typ hodnoty výstup. Výstupní hodnoty podporují stejné typy jako vstupní parametry šablony. |
| hodnota |Ano |Výraz jazyka šablony, který se vyhodnotí a vrátí jako výstupní hodnotu. |

## <a name="recommendations"></a>Doporučení

Pokud používáte šablonu pro vytvoření veřejné IP adresy, zahrnout oddíl výstupy, který vrací podrobnosti IP adresy a plně kvalifikovaný název domény (FQDN). Výstupní hodnoty můžete použít k snadnému načtení podrobností o veřejné IP adresy a plně kvalifikované názvy domény po nasazení.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Příklad šablony


|Šablona  |Popis  |
|---------|---------|
|[Zkopírujte proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří komplexní proměnné a uloží tyto hodnoty. Všechny prostředky, není nasazen. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a výstupy ID prostředku. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Odkazy na předchozí šablony. Při vytváření nástroje pro vyrovnávání zatížení, používá ID prostředku ve výstupu. |


## <a name="next-steps"></a>Další postup
* Hotové šablony pro mnoho různých typů řešení najdete na stránce [Šablony Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/).
* Podrobnosti o funkcích, které můžete použít z v rámci šablon najdete v tématu [funkce šablon Azure Resource Manager](resource-group-template-functions.md).
* Pokud chcete kombinovat několik šablon během nasazení, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Musíte používat prostředky, které existují v jiné skupině prostředků. Tento scénář je běžný, při práci s účty úložiště a virtuální sítě, které jsou sdíleny více skupin prostředků. Další informace najdete v tématu [resourceId funkce](resource-group-template-functions-resource.md#resourceid).