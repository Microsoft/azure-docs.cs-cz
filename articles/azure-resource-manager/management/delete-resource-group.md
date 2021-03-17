---
title: Odstranit skupinu prostředků a prostředky
description: Popisuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager při odstraňování skupiny prostředků vyřadí odstranění prostředků. Popisuje kódy odpovědí a způsob, jakým Správce prostředků je zpracovává, aby bylo možné určit, zda bylo odstranění úspěšné.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: d6c38169916cc6c47d6f05c88645fa435bbdb146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614406"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager skupiny prostředků a odstraňování prostředků

Tento článek popisuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager při odstraňování skupiny prostředků vyřadí odstranění prostředků.

## <a name="how-order-of-deletion-is-determined"></a>Jak je určeno pořadí odstranění

Když odstraníte skupinu prostředků, Správce prostředků určí pořadí, ve kterém se budou prostředky odstraňovat. Používá toto pořadí:

1. Všechny podřízené (vnořené) prostředky se odstraní.

2. Prostředky, které spravují jiné prostředky, se odstraní dále. Prostředek může mít `managedBy` nastavenou vlastnost, která označuje, že ho spravuje jiný prostředek. Je-li tato vlastnost nastavena, bude prostředek, který spravuje jiný prostředek, odstraněn před ostatními prostředky.

3. Zbývající prostředky se odstraní za předchozí dvě kategorie.

Po určení pořadí Správce prostředků vydá operace odstranění pro každý prostředek. Před pokračováním čeká na dokončení všech závislostí.

U synchronních operací jsou očekávané úspěšné kódy odpovědí:

* 200
* 204
* 404

V případě asynchronních operací je očekávaná úspěšná odpověď 202. Správce prostředků sleduje hlavičku umístění nebo hlavičku operace Azure-Async a určí stav asynchronní operace odstranění.
  
### <a name="deletion-errors"></a>Chyby odstranění

Když operace delete vrátí chybu, Správce prostředků se znovu pokusí volání DELETE. U stavových kódů 5xx, 429 a 408 dojde k opakovanému pokusu. Ve výchozím nastavení je časový interval pro opakování 15 minut.

## <a name="after-deletion"></a>Po odstranění

Správce prostředků vyvolá volání GET u každého prostředku, který se pokusil o odstranění. Očekává se, že odpověď tohoto volání GET bude 404. Když Správce prostředků získá 404, považuje se odstranění za úspěšně dokončené. Správce prostředků odstraní prostředek z jeho mezipaměti.

Nicméně pokud volání GET u prostředku vrátí 200 nebo 201, Správce prostředků znovu vytvoří prostředek.

Pokud operace GET vrátí chybu, Správce prostředků se znovu pokusí získat následující kód chyby:

* Méně než 100
* 408
* 429
* Větší než 500

Pro jiné kódy chyb Správce prostředků neúspěšné odstranění prostředku.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné.

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
* Příkazy pro odstranění najdete v tématech [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group#az-group-delete)a [REST API](/rest/api/resources/resourcegroups/delete).
