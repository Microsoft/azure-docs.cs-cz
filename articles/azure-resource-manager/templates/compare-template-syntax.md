---
title: Převod šablon Azure Resource Manager mezi JSON a bicep
description: Porovná šablony Azure Resource Manager vytvořené pomocí JSON a bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744585"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Porovnávání JSON a bicep pro šablony

Tento článek porovnává syntaxi bicep se syntaxí JSON pro šablony Azure Resource Manager (šablony ARM). Ve většině případů poskytuje bicep syntaxi, která je méně podrobná než ekvivalent ve formátu JSON.

## <a name="syntax-equivalents"></a>Ekvivalenty syntaxe

Pokud jste obeznámeni s používáním formátu JSON pro vývoj šablon ARM, použijte následující tabulku, kde se dozvíte o ekvivalentní syntaxi pro bicep.

| Scenario | Šablona ARM | Bicep |
| -------- | ------------ | ----- |
| Vytvořit výraz | `"[func()]"` | `func()` |
| Získat hodnotu parametru | `[parameters('exampleParameter'))]` | `exampleParameter` |
| Získat hodnotu proměnné | `[variables('exampleVar'))]` | `exampleVar` |
| Řetězení řetězců | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| Nastavit vlastnost prostředku | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| Vrátí logickou hodnotu a | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| Získat ID prostředku v šabloně | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| Získat vlastnost z prostředku v šabloně | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| Podmíněně nastaví hodnotu. | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| Oddělení řešení do více souborů | Použití propojených šablon | Použití modulů |
| Nastavení cílového rozsahu nasazení | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| Nastavit závislost | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | Buď spoléhat na automatické zjišťování závislostí, nebo ručně nastavit závislost na `dependsOn: [ stg ]` |

K deklaraci typu a verze prostředku použijte následující v bicep:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

Namísto ekvivalentní syntaxe ve formátu JSON:

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>Doporučení

* Pokud je to možné, vyhněte se použití [reference](template-functions-resource.md#reference) a funkcí [ResourceID](template-functions-resource.md#resourceid) v souboru bicep. Když odkazujete na prostředek ve stejném nasazení bicep, použijte místo něj identifikátor prostředku. Například pokud jste nasadili prostředek v souboru bicep s `stg` identifikátorem prostředku, použijte syntaxi jako `stg.id` nebo `stg.properties.primaryEndpoints.blob` k získání hodnot vlastností. Pomocí identifikátoru prostředku vytvoříte implicitní závislost mezi prostředky. Nemusíte explicitně nastavit závislost s vlastností dependsOn.
* Pro identifikátory používejte konzistentní velká a malá písmena. Pokud si nejste jistí, jaký typ malých a velkých písmen chcete použít, zkuste ve stylu CamelCase velká a malá písmena. Například, `param myCamelCasedParameter string`.
* Přidejte popis do parametru pouze v případě, že popis poskytuje základní informace uživatelům. `//`Pro některé informace můžete použít komentáře.

## <a name="decompile-json-to-bicep"></a>Dekompilovat JSON do bicep

Bicep CLI poskytuje příkaz pro rekompilaci jakékoli existující šablony ARM do souboru bicep. Chcete-li dekompilovat soubor JSON, použijte: `bicep decompile "path/to/file.json"`

Tento příkaz poskytuje výchozí bod pro vytváření bicep, ale příkaz nefunguje pro všechny šablony. Příkaz může selhat nebo může být nutné opravit problémy po dekompilaci. V současné době má příkaz následující omezení:

* Šablony používající kopírovací smyčky nelze dekompilovat.
* Vnořené šablony lze dekompilovat pouze v případě, že používají obor vyhodnocení výrazu Inner.

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

Informace o projektu bicep naleznete v tématu [Project bicep](https://github.com/Azure/bicep).
