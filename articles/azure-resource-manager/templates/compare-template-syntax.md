---
title: Porovnávání syntaxe pro šablony Azure Resource Manager ve formátu JSON a bicep
description: Porovnává Azure Resource Manager šablon vyvinutých pomocí JSON a bicep a ukazuje, jak převádět mezi jazyky.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461790"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Porovnávání JSON a bicep pro šablony

Tento článek porovnává syntaxi bicep se syntaxí JSON pro šablony Azure Resource Manager (šablony ARM). Ve většině případů poskytuje bicep syntaxi, která je méně podrobná než ekvivalent ve formátu JSON.

Pokud jste obeznámeni s používáním formátu JSON pro vývoj šablon ARM, použijte následující příklady, kde se dozvíte o ekvivalentní syntaxi pro bicep.

## <a name="expressions"></a>Výrazy

Chcete-li vytvořit výraz:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parametry

Deklarace parametru s výchozí hodnotou:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Získání hodnoty parametru:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Proměnné

Deklarace proměnné:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Získání hodnoty proměnné:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Řetězce

Pro zřetězení řetězců:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Logické operátory

Pro vrácení logického operátoru **and**:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Podmíněné nastavení hodnoty:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Rozsah nasazení

Nastavení cílového oboru nasazení:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Zdroje informací

Deklarace prostředku:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Postup při podmíněném nasazení prostředku:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Nastavení vlastnosti prostředku:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Postup získání ID prostředku v šabloně:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Smyčky

Iterace za položkami v poli nebo počtu:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Závislosti prostředků

Nastavení závislosti mezi prostředky:

Pro bicep se buď spoléhá na automatické zjišťování závislostí, nebo na ruční nastavení závislosti.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Referenční zdroje

Získání vlastnosti z prostředku v šabloně:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Chcete-li získat vlastnost ze stávajícího prostředku, který není nasazen v šabloně:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Výstupy

Chcete-li vytvořit výstup vlastnosti z prostředku v šabloně:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Opětovné použití kódu

Oddělení řešení do více souborů:

* Pro bicep použijte [moduly](bicep-tutorial-add-modules.md).
* Pro JSON použijte [propojené šablony](linked-templates.md).

## <a name="recommendations"></a>Doporučení

* Pokud je to možné, vyhněte se použití [reference](template-functions-resource.md#reference) a funkcí [ResourceID](template-functions-resource.md#resourceid) v souboru bicep. Když odkazujete na prostředek ve stejném nasazení bicep, použijte místo něj identifikátor prostředku. Například pokud jste nasadili prostředek v souboru bicep s `stg` identifikátorem prostředku, použijte syntaxi jako `stg.id` nebo `stg.properties.primaryEndpoints.blob` k získání hodnot vlastností. Pomocí identifikátoru prostředku vytvoříte implicitní závislost mezi prostředky. Nemusíte explicitně nastavit závislost s vlastností dependsOn.
* Pokud prostředek není nasazen v souboru bicep, můžete i nadále získat symbolický odkaz na prostředek pomocí **existujícího** klíčového slova.
* Pro identifikátory používejte konzistentní velká a malá písmena. Pokud si nejste jistí, jaký typ malých a velkých písmen chcete použít, zkuste ve stylu CamelCase velká a malá písmena. Například, `param myCamelCasedParameter string`.
* Přidejte popis do parametru pouze v případě, že popis poskytuje základní informace uživatelům. `//`Pro některé informace můžete použít komentáře.

## <a name="next-steps"></a>Další kroky

* Informace o bicep najdete v [kurzu bicep](./bicep-tutorial-create-first-bicep.md).
* Další informace o převodu šablon mezi jazyky najdete v tématu [Převod šablon ARM mezi JSON a bicep](bicep-decompile.md).
