---
title: Kurz – Přidání značek k prostředkům v souboru Azure Resource Manager bicep
description: Přidejte značky do prostředků, které nasadíte do souborů bicep. Značky umožňují logicky organizovat prostředky.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632594"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Kurz: Přidání značek do souborů Azure Resource Manager bicep

V tomto kurzu se naučíte přidávat značky do prostředků v souborech bicep. [Značky](../management/tag-resources.md) vám pomůžou logicky organizovat vaše prostředky. Hodnoty značek se zobrazí v sestavách nákladů. Dokončení tohoto kurzu trvá **8 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se šablon pro rychlý Start](bicep-tutorial-quickstart-template.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Předchozí soubor bicep nasadil účet úložiště, App Service plán a webovou aplikaci.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Po nasazení těchto prostředků možná budete potřebovat sledovat náklady a hledat prostředky, které patří do určité kategorie. Můžete přidat značky, které vám pomohou tyto problémy vyřešit.

## <a name="add-tags"></a>Přidání značek

Prostředky označíte, abyste přidali hodnoty, které vám pomůžou identifikovat jejich použití. Můžete například přidat značky, které uvádějí prostředí a projekt. Můžete přidat značky, které identifikují nákladové středisko nebo tým, který je vlastníkem daného prostředku. Přidat můžete libovolné hodnoty, které pro vaši organizaci dávají smysl.

Následující příklad ukazuje změny v souboru bicep. Zkopírujte celý soubor a nahraďte soubor bicep jeho obsahem.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

Je čas nasadit soubor bicep a prohlédnout si výsledky.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](bicep-tutorial-create-first-bicep.md#create-resource-group). V příkladu se předpokládá, že jste nastavili `bicepFile` proměnnou na cestu k souboru bicep, jak je znázorněno v [prvním kurzu](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
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
  --template-file $bicepFile \
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

   ![Zobrazit značky](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte pokračovat k dalšímu kurzu, nemusíte odstranit skupinu prostředků.

Pokud nyní zastavíte, budete možná chtít vyčistit prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali značky k prostředkům. V dalším kurzu se dozvíte, jak používat soubory parametrů k zjednodušení předávání hodnot do nasazení.

> [!div class="nextstepaction"]
> [Použít soubor parametrů](bicep-tutorial-use-parameter-file.md)
