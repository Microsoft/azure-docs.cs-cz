---
title: Kurz – přidání výstupů do souboru Azure Resource Manager bicep
description: K zjednodušení syntaxe přidejte výstupy do souboru bicep.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594304"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Kurz: Přidání výstupů do souboru Azure Resource Manager bicep

V tomto kurzu zjistíte, jak vrátit hodnotu z nasazení. Výstupy použijete, když potřebujete hodnotu z nasazeného prostředku. Dokončení tohoto kurzu trvá **7 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili [kurz o proměnných](bicep-tutorial-add-variables.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Na konci předchozího kurzu měl váš soubor bicep následující obsah:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Nasadí účet úložiště, ale nevrátí žádné informace o účtu úložiště. Je možné, že budete muset zachytit vlastnosti z nového prostředku, aby byly k dispozici později pro referenci.

## <a name="add-outputs"></a>Přidat výstupy

Výstupy můžete použít k vrácení hodnot z nasazení. Může být například užitečné získat koncové body pro váš nový účet úložiště.

Následující příklad ukazuje změnu v souboru bicep pro přidání výstupní hodnoty. Zkopírujte celý soubor a nahraďte soubor bicep jeho obsahem.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

V některých důležitých položkách si všimněte o výstupní hodnotě, kterou jste přidali.

Typ vrácené hodnoty je nastaven na `object` , což znamená, že vrátí objekt šablony.

K získání `primaryEndpoints` vlastnosti z účtu úložiště použijete symbolické jméno účtu úložiště. Funkce automatického dokončování Visual Studio Code zobrazí úplný seznam vlastností:

   ![Vlastnosti objektu symbolického názvu Visual Studio Code bicep](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

Jste připraveni nasadit soubor bicep a podívat se na vrácenou hodnotu.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Ve výstupu příkazu pro nasazení se zobrazí objekt podobný následujícímu příkladu pouze v případě, že výstup je ve formátu JSON:

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
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="review-your-work"></a>Kontrola práce

Za posledních šest kurzů jste provedli spoustu. Pojďme se podívat na to, co jste provedli. Vytvořili jste soubor bicep s parametry, které se dají snadno poskytnout. Soubor bicep se opakovaně používá v různých prostředích, protože umožňuje přizpůsobení a dynamicky vytváří potřebné hodnoty. Vrátí taky informace o účtu úložiště, který můžete ve svém skriptu použít.

Teď se podívejme na skupinu prostředků a historii nasazení.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. V závislosti na krocích, které jste provedli, byste měli mít ve skupině prostředků aspoň jeden a možná několik účtů úložiště.
1. K dispozici je také několik úspěšných nasazení uvedených v historii. Vyberte tento odkaz.

   ![Výběr nasazení](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. V historii uvidíte všechna nasazení. Vyberte nasazení s názvem **addoutputs**.

   ![Zobrazit historii nasazení](./media/bicep-tutorial-add-outputs/show-history.png)

1. Můžete zkontrolovat vstupy.

   ![Zobrazit vstupy](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Můžete si prohlédnout výstupy.

   ![Zobrazit výstupy](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Můžete zkontrolovat šablonu JSON.

   ![Zobrazit šablonu](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste do souboru bicep přidali návratovou hodnotu. V dalším kurzu se dozvíte, jak exportovat šablonu JSON a použít části této exportované šablony v souboru bicep.

> [!div class="nextstepaction"]
> [Použít exportovanou šablonu](bicep-tutorial-export-template.md)
