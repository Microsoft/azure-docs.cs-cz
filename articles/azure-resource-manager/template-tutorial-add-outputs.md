---
title: Kurz – přidání výstupů do šablony
description: Chcete-li zjednodušit syntaxi, přidejte do šablony Azure Resource Manager výstupy.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 654d3f7cbf6362d982549c86e6f54fea1e890cfc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405996"
---
# <a name="tutorial-add-outputs-to-your-resource-manager-template"></a>Kurz: Přidání výstupů do šablony Správce prostředků

V tomto kurzu zjistíte, jak vrátit hodnotu ze šablony. Výstupy použijete, když potřebujete hodnotu z nasazeného prostředku. Dokončení tohoto kurzu trvá **7 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz o proměnných](template-tutorial-add-variables.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Na konci předchozího kurzu má vaše šablona následující JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json)]

Nasadí účet úložiště, ale nevrátí žádné informace o účtu úložiště. Je možné, že budete muset zachytit vlastnosti z nového prostředku, aby byly k dispozici později pro referenci.

## <a name="add-outputs"></a>Přidat výstupy

Pomocí výstupů můžete vracet hodnoty ze šablony. Může být například užitečné získat koncové body pro váš nový účet úložiště.

Následující příklad zvýrazní změnu šablony pro přidání výstupní hodnoty. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json?range=1-53&highlight=47-52)]

V některých důležitých položkách si všimněte o výstupní hodnotě, kterou jste přidali.

Typ vrácené hodnoty je nastaven na **Object**, což znamená, že vrátí objekt JSON.

Pomocí [referenční](resource-group-template-functions-resource.md#reference) funkce získá běhový stav účtu úložiště. Chcete-li získat běhový stav prostředku, předáte název nebo ID prostředku. V takovém případě použijete stejnou proměnnou, kterou jste použili k vytvoření názvu účtu úložiště.

Nakonec vrátí vlastnost **primaryEndpoints** z účtu úložiště.

## <a name="deploy-template"></a>Nasazení šablony

Jste připraveni šablonu nasadit a podívat se na vrácenou hodnotu.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Ve výstupu příkazu pro nasazení se zobrazí objekt podobný tomuto:

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

## <a name="review-your-work"></a>Kontrola práce

Za posledních šest kurzů jste provedli spoustu. Pojďme se podívat na to, co jste provedli. Vytvořili jste šablonu s parametry, které lze snadno poskytnout. Šablona se opakovaně používá v různých prostředích, protože umožňuje přizpůsobení a dynamicky vytváří potřebné hodnoty. Vrátí taky informace o účtu úložiště, který můžete ve svém skriptu použít.

Teď se podívejme na skupinu prostředků a historii nasazení.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. V závislosti na krocích, které jste provedli, byste měli mít ve skupině prostředků aspoň jeden a možná několik účtů úložiště.
1. K dispozici je také několik úspěšných nasazení uvedených v historii. Vyberte tento odkaz.

   ![Výběr nasazení](./media/template-tutorial-add-outputs/select-deployments.png)

1. V historii uvidíte všechna nasazení. Vyberte nasazení s názvem **addoutputs**.

   ![Zobrazit historii nasazení](./media/template-tutorial-add-outputs/show-history.png)

1. Můžete zkontrolovat vstupy.

   ![Zobrazit vstupy](./media/template-tutorial-add-outputs/show-inputs.png)

1. Můžete si prohlédnout výstupy.

   ![Zobrazit výstupy](./media/template-tutorial-add-outputs/show-outputs.png)

1. Můžete si prohlédnout šablonu.

   ![Zobrazit šablonu](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste do šablony přidali návratovou hodnotu. V dalším kurzu se dozvíte, jak exportovat šablonu a použít části této exportované šablony ve vaší šabloně.

> [!div class="nextstepaction"]
> [Použít exportovanou šablonu](template-tutorial-export-template.md)
