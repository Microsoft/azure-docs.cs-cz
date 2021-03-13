---
title: Porovnávání syntaxe pro šablony Azure Resource Manager ve formátu JSON a bicep
description: Porovnává Azure Resource Manager šablon vyvinutých pomocí JSON a bicep a ukazuje, jak převádět mezi jazyky.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 85f85e66e69eede68bab847e4bc68514e65115eb
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418041"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Porovnávání JSON a bicep pro šablony

Tento článek porovnává syntaxi bicep se syntaxí JSON pro šablony Azure Resource Manager (šablony ARM). Ve většině případů poskytuje bicep syntaxi, která je méně podrobná než ekvivalent ve formátu JSON.

## <a name="syntax-equivalents"></a>Ekvivalenty syntaxe

Pokud jste obeznámeni s používáním formátu JSON pro vývoj šablon ARM, použijte následující tabulku, kde se dozvíte o ekvivalentní syntaxi pro bicep.

| Scenario | Bicep | JSON |
| -------- | ------------ | ----- |
| Vytvořit výraz | `func()` | `"[func()]"` |
| Získat hodnotu parametru | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Získat hodnotu proměnné | `exampleVar` | `[variables('exampleVar'))]` |
| Řetězení řetězců | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Nastavit vlastnost prostředku | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Vrátí logickou hodnotu a | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Získat ID prostředku v šabloně | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Získat vlastnost z prostředku v šabloně | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Podmíněně nastaví hodnotu. | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Oddělení řešení do více souborů | Použití modulů | Použití propojených šablon |
| Nastavení cílového rozsahu nasazení | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Nastavit závislost | Buď spoléhat na automatické zjišťování závislostí, nebo ručně nastavit závislost na `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Deklarace prostředku | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Doporučení

* Pokud je to možné, vyhněte se použití [reference](template-functions-resource.md#reference) a funkcí [ResourceID](template-functions-resource.md#resourceid) v souboru bicep. Když odkazujete na prostředek ve stejném nasazení bicep, použijte místo něj identifikátor prostředku. Například pokud jste nasadili prostředek v souboru bicep s `stg` identifikátorem prostředku, použijte syntaxi jako `stg.id` nebo `stg.properties.primaryEndpoints.blob` k získání hodnot vlastností. Pomocí identifikátoru prostředku vytvoříte implicitní závislost mezi prostředky. Nemusíte explicitně nastavit závislost s vlastností dependsOn.
* Pro identifikátory používejte konzistentní velká a malá písmena. Pokud si nejste jistí, jaký typ malých a velkých písmen chcete použít, zkuste ve stylu CamelCase velká a malá písmena. Například, `param myCamelCasedParameter string`.
* Přidejte popis do parametru pouze v případě, že popis poskytuje základní informace uživatelům. `//`Pro některé informace můžete použít komentáře.

## <a name="next-steps"></a>Další kroky

* Informace o bicep najdete v [kurzu bicep](./bicep-tutorial-create-first-bicep.md).
* Další informace o převodu šablon mezi jazyky najdete v tématu [Převod šablon ARM mezi JSON a bicep](bicep-decompile.md).
