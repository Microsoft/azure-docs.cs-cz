---
title: Odstranit skupinu prostředků a prostředky
description: Popisuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager při odstraňování skupiny prostředků vyřadí odstranění prostředků. Popisuje kódy odpovědí a jak je určit, pokud bylo odstranění úspěšné zpracovává Resource Manageru.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: c84dcf64555f40f45dbdaeedb05d4cc461f2cbb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150816"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager skupiny prostředků a odstraňování prostředků

Tento článek popisuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager při odstraňování skupiny prostředků vyřadí odstranění prostředků.

## <a name="how-order-of-deletion-is-determined"></a>Jak je určeno pořadí odstranění

Když odstraníte skupinu prostředků, Resource Manager určuje pořadí, odstraňte prostředky. Použije následujícím pořadí:

1. Se odstraní všechny prostředky vnořená ().

2. Dále se odstraní prostředky, které spravují další prostředky. Prostředek může mít `managedBy` nastavenou k označení, že jiný prostředek spravuje ho. Pokud je tato vlastnost nastavena, před dalším prostředkům odstranění prostředku, který spravuje jiný prostředek.

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Na [portálu](https://portal.azure.com)vyberte skupinu prostředků, kterou chcete odstranit.

1. Vyberte **Odstranit skupinu prostředků**.

   ![Odstranění skupiny prostředků](./media/resource-group-delete/delete-group.png)

1. Odstranění potvrďte tak, že zadáte název skupiny prostředků.

---

## <a name="delete-resource"></a>Odstranit prostředek

K odstranění prostředku použijte jednu z následujících metod.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Na [portálu](https://portal.azure.com)vyberte prostředek, který chcete odstranit.

1. Vyberte **Odstranit**. Následující snímek obrazovky ukazuje možnosti správy pro virtuální počítač.

   ![Odstranit prostředek](./media/resource-group-delete/delete-resource.png)

1. Po zobrazení výzvy potvrďte odstranění.

---


## <a name="next-steps"></a>Další kroky

* Koncepce Resource Manageru, najdete v článku [přehled Azure Resource Manageru](resource-group-overview.md).
* Odstranění příkazy naleznete v tématu [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [rozhraní příkazového řádku Azure](/cli/azure/group?view=azure-cli-latest#az-group-delete), a [rozhraní REST API](/rest/api/resources/resourcegroups/delete).
