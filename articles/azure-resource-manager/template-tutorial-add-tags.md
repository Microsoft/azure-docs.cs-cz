---
title: Kurz – Přidání značek k prostředkům v šabloně
description: Přidejte značky do prostředků, které nasadíte v šabloně Azure Resource Manager. Značky umožňují logicky organizovat prostředky.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 078d85b3120baf5b8bea9c305fb1f97763f8df59
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406066"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Kurz: Přidání značek do šablony Správce prostředků

V tomto kurzu se naučíte přidávat značky do prostředků v šabloně. [Značky](resource-group-using-tags.md) vám pomůžou logicky organizovat vaše prostředky. Hodnoty značek se zobrazí v sestavách nákladů. Dokončení tohoto kurzu trvá **8 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se šablon pro rychlý Start](template-tutorial-quickstart-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Zkontrolovat šablonu

Předchozí šablona nasadila účet úložiště, App Service plán a webovou aplikaci.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

Po nasazení těchto prostředků možná budete potřebovat sledovat náklady a hledat prostředky, které patří do určité kategorie. Můžete přidat značky, které vám pomohou tyto problémy vyřešit.

## <a name="add-tags"></a>Přidání značek

Prostředky označíte přidáním hodnot, které vám pomůžou identifikovat jejich použití. Můžete například přidat značky, které uvádějí prostředí a projekt. Můžete přidat značky, které identifikují nákladové středisko nebo tým, který je vlastníkem daného prostředku. Přidejte všechny hodnoty, které dávají smysl pro vaši organizaci.

Následující příklad zvýrazní změny šablony. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-template"></a>Nasazení šablony

Je čas nasadit šablonu a prohlédnout si výsledky.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vyberte jeden z prostředků, například prostředek účtu úložiště. Vidíte, že má nyní značky.

   ![Zobrazit značky](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali značky k prostředkům. V dalším kurzu se dozvíte, jak pomocí souborů parametrů zjednodušit předávání hodnot do šablony.

> [!div class="nextstepaction"]
> [Použít soubor parametrů](template-tutorial-use-parameter-file.md)
