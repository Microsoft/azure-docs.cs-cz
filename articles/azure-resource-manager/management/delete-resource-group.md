---
title: Odstranit skupinu prostředků a prostředky
description: Popisuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager při odstraňování skupiny prostředků vyřadí odstranění prostředků. Popisuje kódy odpovědí a jak je určit, pokud bylo odstranění úspěšné zpracovává Resource Manageru.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360674"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager skupiny prostředků a odstraňování prostředků

Tento článek popisuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager při odstraňování skupiny prostředků vyřadí odstranění prostředků.

## <a name="how-order-of-deletion-is-determined"></a>Jak je určeno pořadí odstranění

Když odstraníte skupinu prostředků, Resource Manager určuje pořadí, odstraňte prostředky. Použije následujícím pořadí:

1. Se odstraní všechny prostředky vnořená ().

2. Dále se odstraní prostředky, které spravují další prostředky. Prostředek může mít nastavenou vlastnost `managedBy` k označení toho, že ho spravuje jiný prostředek. Pokud je tato vlastnost nastavena, před dalším prostředkům odstranění prostředku, který spravuje jiný prostředek.

3. Zbývající prostředky odstraní po předchozí dvě kategorie.

Po pořadí je určeno, vydá Resource Manageru operace odstranění pro každý zdroj. Čeká nějak záviset na dokončení, než budete pokračovat.

U synchronních operací může kódy úspěšné odpovědi očekávané jsou:

* 200
* 204
* 404

Pro asynchronní operace je očekávané úspěšné odpovědi 202. Resource Manager sleduje hlavičky location nebo hlavičce azure asynchronní operace k určení stavu asynchronní operaci.
  
### <a name="deletion-errors"></a>Chyby odstranění

Při operaci delete vrátí chybu, správce prostředků zopakuje pokus o volání aktualizace. Opakované pokusy se provede pro 5xx, 429 a 408 stavové kódy. Výchozí časový interval opakování je 15 minut.

## <a name="after-deletion"></a>Po jeho odstranění

Resource Manager vydává volání GET u jednotlivých prostředků, které se pokusil odstranit. Odpověď na toto volání GET má být 404. Resource Manager získá 404, považuje se odstraňování byly úspěšně dokončeny. Resource Manager Odstraní prostředek uloženou v mezipaměti.

Ale pokud volání GET s prostředkem vrátí 200 nebo 201, znovu správce prostředků vytvoří prostředek.

Pokud operace GET vrátí chybu, Resource Manager opakuje GET pro následující kód chyby:

* Méně než 100
* 408
* 429
* Větší než 500

Pro další kódy chyb nezdaří správce prostředků odstranění prostředku.

## <a name="delete-resource-group"></a>Odstranění skupiny prostředků

Chcete-li odstranit skupinu prostředků, použijte jednu z následujících metod.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Na [portálu](https://portal.azure.com)vyberte skupinu prostředků, kterou chcete odstranit.

1. Vyberte **Odstranit skupinu prostředků**.

   ![Odstranění skupiny prostředků](./media/delete-resource-group/delete-group.png)

1. Odstranění potvrďte tak, že zadáte název skupiny prostředků.

---

## <a name="delete-resource"></a>Odstranit prostředek

K odstranění prostředku použijte jednu z následujících metod.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Na [portálu](https://portal.azure.com)vyberte prostředek, který chcete odstranit.

1. Vyberte **Odstranit**. Následující snímek obrazovky ukazuje možnosti správy pro virtuální počítač.

   ![Odstranit prostředek](./media/delete-resource-group/delete-resource.png)

1. Po zobrazení výzvy potvrďte odstranění.

---


## <a name="next-steps"></a>Další kroky

* Vysvětlení Správce prostředků konceptů najdete v tématu [Azure Resource Manager Overview](overview.md).
* Příkazy pro odstranění najdete v tématech [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)a [REST API](/rest/api/resources/resourcegroups/delete).
