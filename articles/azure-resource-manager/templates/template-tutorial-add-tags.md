---
title: Kurz – Přidání značek k prostředkům v šabloně
description: Přidejte značky do prostředků, které nasadíte v šabloně Azure Resource Manager (šablona ARM). Značky umožňují logicky organizovat prostředky.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 625a88c0ee946b1ca67737d9cc67b638699d12f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106999"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Kurz: Přidání značek do šablony ARM

V tomto kurzu se naučíte přidávat značky do prostředků v šabloně Azure Resource Manager (šablona ARM). [Značky](../management/tag-resources.md) vám pomůžou logicky organizovat vaše prostředky. Hodnoty značek se zobrazí v sestavách nákladů. Dokončení tohoto kurzu trvá **8 minut** .

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili [kurz týkající se šablon pro rychlý Start](template-tutorial-quickstart-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Předchozí šablona nasadila účet úložiště, App Service plán a webovou aplikaci.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Po nasazení těchto prostředků možná budete potřebovat sledovat náklady a hledat prostředky, které patří do určité kategorie. Můžete přidat značky, které vám pomohou tyto problémy vyřešit.

## <a name="add-tags"></a>Přidání značek

Prostředky označíte, abyste přidali hodnoty, které vám pomůžou identifikovat jejich použití. Můžete například přidat značky, které uvádějí prostředí a projekt. Můžete přidat značky, které identifikují nákladové středisko nebo tým, který je vlastníkem daného prostředku. Přidat můžete libovolné hodnoty, které pro vaši organizaci dávají smysl.

Následující příklad zvýrazní změny šablony. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Nasazení šablony

Je čas nasadit šablonu a prohlédnout si výsledky.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `templateFile` proměnnou na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vyberte jeden z prostředků, například prostředek účtu úložiště. Vidíte, že má nyní značky.

   ![Zobrazit značky](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali značky k prostředkům. V dalším kurzu se dozvíte, jak pomocí souborů parametrů zjednodušit předávání hodnot do šablony.

> [!div class="nextstepaction"]
> [Použít soubor parametrů](template-tutorial-use-parameter-file.md)
