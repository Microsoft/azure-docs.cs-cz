---
title: Porovnávání syntaxe pro šablony Azure Resource Manager ve formátu JSON a bicep
description: Porovnává Azure Resource Manager šablon vyvinutých pomocí JSON a bicep a ukazuje, jak převádět mezi jazyky.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036210"
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

## <a name="decompile-json-to-bicep"></a>Dekompilovat JSON do bicep

Bicep CLI poskytuje příkaz pro rekompilaci jakékoli existující šablony ARM do souboru bicep. Chcete-li dekompilovat soubor JSON, použijte: `bicep decompile "path/to/file.json"`

Tento příkaz poskytuje výchozí bod pro vytváření bicep, ale příkaz nefunguje pro všechny šablony. Příkaz může selhat nebo může být nutné opravit problémy po dekompilaci. V současné době lze vnořené šablony dekompilovat pouze v případě, že používají obor vyhodnocení výrazu Inner.

Můžete exportovat šablonu pro skupinu prostředků a pak ji předávat přímo do příkazu bicep dekompilovat. Následující příklad ukazuje, jak dekompilovat exportovanou šablonu.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

[Exportujte šablonu](export-template-portal.md) prostřednictvím portálu. Použijte `bicep decompile <filename>` ve staženém souboru.

---

## <a name="build-json-from-bicep"></a>Sestavit JSON z bicep

Rozhraní příkazového řádku bicep také poskytuje příkaz pro převod bicep na formát JSON. Chcete-li vytvořit soubor JSON, použijte: `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Zobrazení vedle sebe

[Bicep Playground](https://aka.ms/bicepdemo) umožňuje zobrazit ekvivalentní soubory JSON a bicep vedle sebe. Můžete vybrat vzorovou šablonu pro zobrazení obou verzí. Případně můžete vybrat možnost `Decompile` nahrát vlastní šablonu JSON a zobrazit ekvivalentní soubor bicep.

## <a name="next-steps"></a>Další kroky

Informace o bicep najdete v [kurzu bicep](./bicep-tutorial-create-first-bicep.md).
