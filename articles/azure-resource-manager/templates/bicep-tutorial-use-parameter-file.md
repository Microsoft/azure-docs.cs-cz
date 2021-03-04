---
title: Kurz – použití souboru parametrů k nasazení Azure Resource Manager souboru bicep
description: Použijte soubory parametrů, které obsahují hodnoty, které se mají použít k nasazení souboru bicep.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 233bab26d659fef2da9a9f5a7080d4b3ecbce7d4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748037"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Kurz: použití souborů parametrů k nasazení Azure Resource Manager souboru bicep

V tomto kurzu se naučíte používat [soubory parametrů](parameter-files.md) k uložení hodnot, které předáte během nasazování. V předchozích kurzech jste v příkazu nasazení použili vložené parametry. Tento přístup pracoval při testování souboru bicep, ale při automatizaci nasazení může být snazší předat sadu hodnot pro vaše prostředí. Soubory parametrů usnadňují zabalení hodnot parametrů pro konkrétní prostředí. Při nasazování šablony JSON použijete stejný soubor parametrů JSON. V tomto kurzu vytvoříte soubory parametrů pro vývojová a produkční prostředí. Dokončení trvá přibližně **12 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili [kurz týkající se značek](bicep-tutorial-add-tags.md), ale není to nutné.

Musíte mít Visual Studio Code s rozšířením bicep a buď Azure PowerShell nebo Azure CLI. Další informace najdete v tématu [bicep Tools](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Zkontrolovat soubor bicep

Váš soubor bicep má mnoho parametrů, které můžete během nasazení zadat. Na konci předchozího kurzu vypadal váš soubor bicep jako:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Tento soubor bicep funguje dobře, ale nyní chcete snadno spravovat parametry, které předáte pro soubor bicep.

## <a name="add-parameter-files"></a>Přidat soubory parametrů

Soubory parametrů jsou soubory JSON se strukturou, která je podobná šablonám JSON. V souboru zadejte hodnoty parametrů, které chcete předat během nasazování.

V rámci souboru parametrů zadáte hodnoty pro parametry v souboru bicep. Název každého parametru v souboru parametrů se musí shodovat s názvem parametru v souboru bicep. V názvu se nerozlišují malá a velká písmena, ale pokud chcete snadno zobrazit odpovídající hodnoty, doporučujeme, abyste se shodovali se škálováním na velká a malá písmena ze souboru bicep.

Nemusíte zadávat hodnotu pro každý parametr. Pokud má nespecifikovaný parametr výchozí hodnotu, použije se tato hodnota během nasazování. Pokud parametr nemá výchozí hodnotu a není zadán v souboru parametrů, budete vyzváni k zadání hodnoty během nasazování.

V souboru parametrů nemůžete zadat název parametru, který se neshoduje s názvem parametru v souboru bicep. Pokud jsou k dispozici neznámé parametry, zobrazí se chyba.

V Visual Studio Code vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem _azuredeploy.parameters.dev.jsv_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Tento soubor je vaším souborem parametrů pro vývojové prostředí. Všimněte si, že používá **Standard_LRS** pro účet úložiště, názvy prostředků s předponou pro **vývoj** a nastaví `Environment` značku na **dev**.

Znovu vytvořte nový soubor s následujícím obsahem. Uložte soubor s názvem _azuredeploy.parameters.prod.jsv_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Tento soubor je vaším souborem parametrů pro produkční prostředí. Všimněte si, že používá **Standard_GRS** pro účet úložiště, názvy prostředků s předponou **Contoso** a nastaví `Environment` značku na **produkční**. V reálném produkčním prostředí byste také chtěli použít službu App Service s jinou skladovou jednotkou než bezplatnou, ale pro tento kurz bude tato SKU nadále používat.

## <a name="deploy-bicep-file"></a>Nasadit soubor bicep

K nasazení souboru bicep použijte rozhraní příkazového řádku Azure nebo Azure PowerShell.

V tomto kurzu vytvoříme dvě nové skupiny prostředků. Jednu pro vývojové prostředí a jednu pro produkční prostředí.

Pro proměnné Template a parametr, nahraďte `{path-to-the-bicep-file}` , `{path-to-azuredeploy.parameters.dev.json}` , a `{path-to-azuredeploy.parameters.prod.json}` složené závorky `{}` se souborem bicep a cestami k souboru parametrů.

Nejdřív nasadíme do vývojového prostředí.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li spustit tuto rutinu nasazení, je nutné mít [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Jestliže chcete spustit tento příkaz nasazení, musíte mít k dispozici [nejnovější verzi](/cli/azure/install-azure-cli) Azure CLI.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

Nyní nasadíme do produkčního prostředí.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Pokud se nasazení nepovedlo, pomocí `verbose` přepínače Získejte informace o vytvářených prostředcích. K `debug` získání dalších informací pro ladění použijte přepínač.

## <a name="verify-deployment"></a>Ověření nasazení

Nasazení můžete ověřit prozkoumáním skupin prostředků z Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **skupiny prostředků**.
1. V tomto kurzu se zobrazí dvě nové skupiny prostředků, které jste nasadili.
1. Vyberte buď skupinu prostředků, a zobrazte nasazené prostředky. Všimněte si, že odpovídají hodnotám, které jste zadali v souboru parametrů pro toto prostředí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu použijete soubor parametrů k nasazení souboru bicep. V dalším kurzu se dozvíte, jak naplánovat modularizaci soubory bicep.

> [!div class="nextstepaction"]
> [Přidat moduly](./bicep-tutorial-add-modules.md)
