---
title: Kurz – Přidání značek do prostředků v šabloně Azure Resource Manager
description: Přidejte značky do prostředků, které nasadíte v šabloně Azure Resource Manager. Značky umožňují logicky organizovat prostředky.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b6ff50f7254a51bcdf37ecb0afd8f0041a2c5da
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177570"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Kurz: Přidání značek do šablony Správce prostředků

V tomto kurzu se naučíte přidávat značky do prostředků v šabloně. [Značky](resource-group-using-tags.md) vám pomůžou logicky organizovat vaše prostředky. Hodnoty značek se zobrazí v sestavách nákladů. Dokončení tohoto kurzu trvá **8 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se šablon pro rychlý Start](template-tutorial-quickstart-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením Správce prostředků Tools a buď Azure PowerShell, nebo v rozhraní příkazového řádku Azure. Další informace najdete v tématu [nástroje šablon](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Kontrola šablony

Předchozí šablona nasadila účet úložiště, App Service plán a webovou aplikaci.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

Po nasazení těchto prostředků možná budete potřebovat sledovat náklady a hledat prostředky, které patří do určité kategorie. Můžete přidat značky, které vám pomohou tyto problémy vyřešit.

## <a name="add-tags"></a>Přidat značky

Prostředky označíte přidáním hodnot, které vám pomůžou identifikovat jejich použití. Můžete například přidat značky, které uvádějí prostředí a projekt. Můžete přidat značky, které identifikují nákladové středisko nebo tým, který je vlastníkem daného prostředku. Přidejte všechny hodnoty, které dávají smysl pro vaši organizaci.

Následující příklad zvýrazní změny šablony. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-the-template"></a>Nasazení šablony

Je čas nasadit šablonu a prohlédnout si výsledky.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). V příkladu se předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[Prostředí](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Rozhraní příkazového řádku Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-the-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupiny prostředků z Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vyberte jeden z prostředků, například prostředek účtu úložiště. Vidíte, že má nyní značky.

   ![Zobrazit značky](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali značky k prostředkům. V dalším kurzu se dozvíte, jak pomocí souborů parametrů zjednodušit předávání hodnot do šablony.

> [!div class="nextstepaction"]
> [Použít soubor parametrů](template-tutorial-use-parameter-file.md)
