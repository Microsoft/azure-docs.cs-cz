---
title: Výuka - přidání výstupů do šablony
description: Přidejte výstupy do šablony Azure Resource Manager, abyste zjednodušili syntaxi.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 150801fe66da1ea8c6f10d437c6d7616a77b544e
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411003"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Kurz: Přidání výstupů do šablony ARM

V tomto kurzu se dozvíte, jak vrátit hodnotu ze šablony Správce prostředků Azure (ARM). Výstupy se používají, když potřebujete hodnotu z nasazeného prostředku. Tento kurz trvá **7 minut.**

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [kurz o proměnných](template-tutorial-add-variables.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Na konci předchozího kurzu měla vaše šablona následující JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Nasazuje účet úložiště, ale nevrací žádné informace o účtu úložiště. Možná budete muset zachytit vlastnosti z nového prostředku, aby byly k dispozici později pro referenci.

## <a name="add-outputs"></a>Přidání výstupů

Výstupy můžete použít k vrácení hodnot ze šablony. Například může být užitečné získat koncové body pro váš nový účet úložiště.

Následující příklad zvýrazní změnu šablony a přidá výstupní hodnotu. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

O přidané výstupní hodnotě je třeba poznamenat několik důležitých položek.

Typ vrácené hodnoty je nastaven na **objekt**, což znamená, že vrátí objekt JSON.

Používá [referenční](template-functions-resource.md#reference) funkci k získání stavu runtime účtu úložiště. Chcete-li získat stav za běhu prostředku, předáte název nebo ID prostředku. V takovém případě použijete stejnou proměnnou, kterou jste použili k vytvoření názvu účtu úložiště.

Nakonec vrátí **vlastnost primaryEndpoints** z účtu úložiště.

## <a name="deploy-template"></a>Nasazení šablony

Jste připraveni nasadit šablonu a podívejte se na vrácenou hodnotu.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Ve výstupu pro příkaz nasazení uvidíte objekt podobný:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Pokud se nasazení nezdařilo, zobrazte protokoly ladění pomocí **přepínače ladění** s příkazem deployment.  Můžete také použít **podrobný** přepínač k zobrazení úplné protokoly ladění.

## <a name="review-your-work"></a>Kontrola práce

Udělali jste hodně v posledních šesti výukových programů. Pojďme se na chvíli projít, co jste udělali. Vytvořili jste šablonu s parametry, které lze snadno poskytnout. Šablona je opakovaně použitelná v různých prostředích, protože umožňuje přizpůsobení a dynamicky vytváří potřebné hodnoty. Vrátí také informace o účtu úložiště, který můžete použít ve skriptu.

Teď se podívejme na skupinu prostředků a historii nasazení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. V závislosti na krocích, které jste provedli, byste měli mít alespoň jeden a možná i několik účtů úložiště ve skupině prostředků.
1. Měli byste mít také několik úspěšných nasazení uvedených v historii. Vyberte tento odkaz.

   ![Vybrat nasazení](./media/template-tutorial-add-outputs/select-deployments.png)

1. Zobrazí se všechna vaše nasazení v historii. Vyberte nasazení nazývané **addoutputs**.

   ![Zobrazit historii nasazení](./media/template-tutorial-add-outputs/show-history.png)

1. Můžete zkontrolovat vstupy.

   ![Zobrazit vstupy](./media/template-tutorial-add-outputs/show-inputs.png)

1. Můžete zkontrolovat výstupy.

   ![Zobrazit výstupy](./media/template-tutorial-add-outputs/show-outputs.png)

1. Šablonu si můžete prohlédnout.

   ![Zobrazit šablonu](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali vrácenou hodnotu do šablony. V dalším kurzu se dozvíte, jak exportovat šablonu a použít části této exportované šablony v šabloně.

> [!div class="nextstepaction"]
> [Použít exportovnou šablonu](template-tutorial-export-template.md)
