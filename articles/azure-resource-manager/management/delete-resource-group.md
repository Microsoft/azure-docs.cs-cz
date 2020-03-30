---
title: Odstranit skupinu prostředků a prostředky
description: Popisuje způsob odstranění skupin prostředků a prostředků. Popisuje, jak Azure Resource Manager objednávky odstranění prostředků při odstranění skupiny prostředků. Popisuje kódy odpovědí a způsob, jakým je správce prostředků zpracovává, aby zjistil, zda bylo odstranění úspěšné.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274018"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Skupina prostředků Správce prostředků Azure a odstranění prostředků

Tento článek ukazuje, jak odstranit skupiny prostředků a prostředky. Popisuje, jak Azure Resource Manager objednávky odstranění prostředků při odstranění skupiny prostředků.

## <a name="how-order-of-deletion-is-determined"></a>Jak je určeno pořadí mazání

Když odstraníte skupinu prostředků, Správce prostředků určí pořadí k odstranění prostředků. Používá následující pořadí:

1. Všechny podřízené (vnořené) prostředky jsou odstraněny.

2. Prostředky, které spravují jiné prostředky, budou odstraněny dále. Prostředek může mít `managedBy` vlastnost nastavenou tak, aby označovala, že ji spravuje jiný prostředek. Je-li tato vlastnost nastavena, prostředek, který spravuje druhý prostředek, je odstraněn před ostatními prostředky.

3. Zbývající prostředky jsou odstraněny po předchozích dvou kategoriích.

Po určení objednávky vydá Správce prostředků operaci DELETE pro každý prostředek. Čeká na dokončení všech závislostí před pokračováním.

Pro synchronní operace jsou očekávané kódy úspěšných odpovědí:

* 200
* 204
* 404

Pro asynchronní operace je očekávaná úspěšná odpověď 202. Správce prostředků sleduje záhlaví umístění nebo záhlaví operace azure-async k určení stavu operace asynchronního odstranění.
  
### <a name="deletion-errors"></a>Chyby odstranění

Pokud operace odstranění vrátí chybu, správce prostředků zopakuje volání DELETE. Opakování se stane pro stavové kódy 5xx, 429 a 408. Ve výchozím nastavení je časové období pro opakování 15 minut.

## <a name="after-deletion"></a>Po odstranění

Správce prostředků vydává volání GET u každého prostředku, který se pokusil odstranit. Očekává se, že odpověď tohoto volání GET bude 404. Když Resource Manager získá 404, považuje odstranění úspěšně dokončeno. Správce prostředků odebere prostředek z mezipaměti.

Pokud však volání GET na prostředku vrátí hodnotu 200 nebo 201, správce prostředků prostředek znovu vytvoří.

Pokud operace GET vrátí chybu, Správce prostředků zopakuje get pro následující kód chyby:

* Méně než 100
* 408
* 429
* Více než 500

U jiných kódů chyb správce prostředků se nezdaří odstranění prostředku.

## <a name="delete-resource-group"></a>Odstranění skupiny prostředků

K odstranění skupiny prostředků použijte jednu z následujících metod.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Na [portálu](https://portal.azure.com)vyberte skupinu prostředků, kterou chcete odstranit.

1. Vyberte **Odstranit skupinu prostředků**.

   ![Odstranění skupiny prostředků](./media/delete-resource-group/delete-group.png)

1. Chcete-li odstranění potvrdit, zadejte název skupiny prostředků.

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

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Na [portálu](https://portal.azure.com)vyberte prostředek, který chcete odstranit.

1. Vyberte **Odstranit**. Následující snímek obrazovky ukazuje možnosti správy pro virtuální počítač.

   ![Odstranit prostředek](./media/delete-resource-group/delete-resource.png)

1. Po zobrazení výzvy potvrďte odstranění.

---


## <a name="next-steps"></a>Další kroky

* Informace o konceptech Správce prostředků najdete v [tématu Přehled Správce prostředků Azure](overview.md).
* Příkazy pro odstranění najdete v [tématu PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)a [REST API](/rest/api/resources/resourcegroups/delete).
