---
title: Výuka – přidání značek do zdrojů v šabloně
description: Přidejte značky k prostředkům, které nasadíte v šabloně Azure Resource Manager. Značky umožňují logicky organizovat prostředky.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3e0deb53e57cd29cbfce4c37f2d6c6729f15bebd
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411708"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Kurz: Přidání značek do šablony ARM

V tomto kurzu se dozvíte, jak přidat značky k prostředkům v šabloně Správce prostředků Azure (ARM). [Značky](../management/tag-resources.md) vám pomohou logicky uspořádat prostředky. Hodnoty značek se zobrazují v sestavách nákladů. Tento kurz trvá **8 minut.**

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit [kurz o šablonách rychlého startu](template-tutorial-quickstart-template.md), ale není to nutné.

Musíte mít kód Visual Studio s rozšířením Nástroje správce prostředků a buď Azure PowerShell nebo Azure CLI. Další informace naleznete v [tématu nástroje šablony](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Šablona revize

Předchozí šablona nasadila účet úložiště, plán služby App Service a webovou aplikaci.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Po nasazení těchto prostředků může být nutné sledovat náklady a najít prostředky, které patří do kategorie. Můžete přidat značky, které vám pomohou tyto problémy vyřešit.

## <a name="add-tags"></a>Přidání značek

Označíte prostředky a přidáte hodnoty, které vám pomohou identifikovat jejich použití. Můžete například přidat značky, které uvádějí prostředí a projekt. Můžete přidat značky, které identifikují nákladové středisko nebo tým, který zdroj vlastní. Přidejte všechny hodnoty, které dávají vaší organizaci smysl.

Následující příklad zvýrazní změny šablony. Zkopírujte celý soubor a nahraďte šablonu jeho obsahem.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Nasazení šablony

Je čas nasadit šablonu a podívat se na výsledky.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](template-tutorial-create-first-template.md#create-resource-group). Příklad předpokládá, že jste nastavili proměnnou **templateFile** na cestu k souboru šablony, jak je znázorněno v [prvním kurzu](template-tutorial-create-first-template.md#deploy-template).

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

Chcete-li spustit tento příkaz nasazení, musíte mít [nejnovější verzi](/cli/azure/install-azure-cli) azure cli.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Pokud se nasazení nezdařilo, zobrazte protokoly ladění pomocí **přepínače ladění** s příkazem deployment.  Můžete také použít **podrobný** přepínač k zobrazení úplné protokoly ladění.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit tak, že prozkoumáte skupinu prostředků z webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé nabídce vyberte **položku Skupiny prostředků**.
1. Vyberte skupinu prostředků, do které jste nasadili.
1. Vyberte jeden z prostředků, například prostředek účtu úložiště. Vidíte, že nyní má značky.

   ![Zobrazit značky](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházíte na další kurz, nemusíte odstraňovat skupinu prostředků.

Pokud nyní zastavujete, můžete chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali značky do prostředků. V dalším kurzu se dozvíte, jak používat soubory parametrů ke zjednodušení předávání hodnot do šablony.

> [!div class="nextstepaction"]
> [Použít soubor parametrů](template-tutorial-use-parameter-file.md)
