---
title: Převod šablon mezi JSON a bicep
description: Popisuje příkazy pro převod šablon Azure Resource Manager z bicep na JSON a z JSON na bicep.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422155"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Převod šablon ARM mezi JSON a bicep

Tento článek popisuje, jak převést šablony Azure Resource Manager (šablony ARM) z JSON na bicep a z bicep na JSON.

Aby bylo možné spustit příkazy převodu, je nutné mít nainstalované rozhraní příkazového [řádku bicep](bicep-install.md) .

Příkazy převodu vytváří šablony, které jsou funkčně ekvivalentní. Ale nemusí být naprosto stejné v implementaci. Převod šablony z formátu JSON na bicep a pak zpět na formát JSON pravděpodobně způsobí šablonu s jinou syntaxí, než je původní šablona. Při nasazení budou převedené šablony vracet stejné výsledky.

## <a name="convert-from-json-to-bicep"></a>Převést z formátu JSON na bicep

Bicep CLI poskytuje příkaz pro rekompilaci existující šablony JSON do souboru bicep. Chcete-li dekompilovat soubor JSON, použijte:

```azurecli
bicep decompile mainTemplate.json
```

Tento příkaz poskytuje výchozí bod pro vytváření bicep. Příkaz nefunguje pro všechny šablony. V současné době lze vnořené šablony dekompilovat pouze v případě, že používají obor vyhodnocení výrazu Inner. Šablony, které používají kopírovací smyčky, nelze dekompilovat.

## <a name="convert-from-bicep-to-json"></a>Převést z bicep na JSON

Rozhraní příkazového řádku bicep také poskytuje příkaz pro převod bicep na formát JSON. Chcete-li vytvořit soubor JSON, použijte:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Exportovat šablonu a převést

Můžete exportovat šablonu pro skupinu prostředků a pak ji předat přímo do příkazu dekompilovat. Následující příklad ukazuje, jak dekompilovat exportovanou šablonu.

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

[Exportujte šablonu](export-template-portal.md) prostřednictvím portálu.

Použijte `bicep decompile <filename>` ve staženém souboru.

---

## <a name="side-by-side-view"></a>Zobrazení vedle sebe

[Bicep Playground](https://aka.ms/bicepdemo) umožňuje zobrazit ekvivalentní soubory JSON a bicep vedle sebe. Můžete vybrat vzorovou šablonu pro zobrazení obou verzí. Případně můžete vybrat možnost `Decompile` nahrát vlastní šablonu JSON a zobrazit ekvivalentní soubor bicep.

## <a name="next-steps"></a>Další kroky

Informace o bicep najdete v [kurzu bicep](./bicep-tutorial-create-first-bicep.md).
